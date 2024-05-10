pipeline {
    agent { label 'jdk17' }

    environment {
        selenium_auth = credentials('jenkins-user-selenium-laforge')
        browserstack_auth = credentials('jenkins-user-browserstack-laforge')
        JIRA_XRAY_TOKEN = credentials('xray-token-api')
    }

    parameters {
        string(name: 'SELENIUM_HUB_URL', defaultValue: 'selenium.laforge.cloud.bpifrance.fr/wd/hub')
        string(name: 'SELENIUM_FRONT_URL', defaultValue: 'https://www.google.com/')
        string(name: 'SELENIUM_BROWSER', defaultValue: 'CHROME')
        string(name: 'TEST_PLAN_KEY', defaultValue: 'TAO-1675')
    }

    options {
        gitLabConnection("gitlab")
    }


    stages {
        stage('Get Features') {
            steps {
                script {
                    sh "curl -H 'Content-Type: application/json' -H 'Authorization: Bearer ${JIRA_XRAY_TOKEN}' -X GET 'https://jira.web.bpifrance.fr/rest/raven/1.0/export/test?keys=${TEST_PLAN_KEY}&fz=true' --output features.zip"
                    unzip dir: 'src/test/resources/features', glob: '', zipFile: 'features.zip'
                    sh "ls src/test/resources/features"
                }
            }
        }

        stage('Run Tests UI') {
            steps {

                catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                    configFileProvider([configFile(fileId: 'global-main-artifactory-la-forge', variable: 'MAVEN_SETTINGS_XML')]) {
                        script {
                            sh 'mvn -s $MAVEN_SETTINGS_XML clean test -Dfile.encoding=utf-8 -U -DSELENIUM_BROWSER=' + params.SELENIUM_BROWSER + ' -DSELENIUM_FRONT_URL=' + params.SELENIUM_FRONT_URL + ' -DSELENIUM_HUB_URL=https://${selenium_auth_USR}:${selenium_auth_PSW}@' + params.SELENIUM_HUB_URL
                        }
                    }
                }
            }

        }


        stage('Update XRay') {
            steps {
                script {
                    def xrayInfosPath = "${WORKSPACE}/xray-infos.json" as Object

                    sh """
                            cat ${xrayInfosPath} | sed -r "s/<build>/${BUILD_NUMBER}/g" | sed -r "s/<job>/${JOB_NAME}/g" | sed -r "s/<testPlanKey>/${TEST_PLAN_KEY}/g" > temp.json
                            mv temp.json ${xrayInfosPath}
                            zip results.zip target/cucumber.json
                        """

                    env.TEST_EXEC_KEY = sh(script: "curl -s -H 'accept: application/json' -H 'Content-Type: multipart/form-data' -H 'Authorization: Bearer ${JIRA_XRAY_TOKEN}' -F 'file=@results.zip' POST https://jira.web.bpifrance.fr/rest/raven/1.0/import/execution/bundle |  jq -r .testExecIssue.key", returnStdout: true).trim()
                    sh "curl -H 'Content-Type: application/json' -H 'Authorization: Bearer ${JIRA_XRAY_TOKEN}' -X PUT  --data '@${xrayInfosPath}' https://jira.web.bpifrance.fr/rest/api/2/issue/${TEST_EXEC_KEY}"
                }
            }
        }
    }


    post {
        always {
            deleteDir()
        }
    }
}