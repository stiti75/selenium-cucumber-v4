# Cucumber-selenium-runner

## Description
Un socle d'automatisation de test utilisant les frameworks selenium, cucumber et JUnit entre autres,
préconfiguré et adaptable.<br>Il vient avec toute la configuration nécessaire pour gérer un projet d'autom. <br>
Pour plus d'information sur le socle rendez-vous sur le [lien vers Neurones.](https://neurones.web.bpifrance.fr/display/HYP/Upgrade+Selenium+Runner+JUnit5)

## Prérequis
Quelques prérequis nécessaires pour exécuter le projet :
- Java 8 ou supérieur
- Maven 3.6.3 ou supérieur
- Un navigateur dont la version correspond à celle mentionnée dans `configuration.properties`

## Installation
Étapes pour installer le projet :
1. Clonez le dépôt : `git clone https://gitlab.laforge.cloud.bpifrance.fr/team/TAO/public/cucumber_runner_junit5`
2. Une fois cloné, ouvrez le avec votre IDE préféré
3. Un exemple d'une simple recherche sur Google est disponible
4. Vous respecterez cette architecture (Page Object Model) tout au long de votre proj

## Architecture du Projet
Le projet contient 5 packages qui sont :
- Configuration :
  Comme son nom l'indique, dans ce package, on a les classes pour configurer à la fois l'execution en local et sur PTA <br>
  Sauf cas de force majeur, il ne faut surtout pas modifier les classes ici.
- Pages :
  Toutes les pages web de l'appli à automatiser sont implémentées ici. <br>Elles héritent BaseTools pour les fonctions recurrentes de selenium.
- Runner :
  Seule la classe RunCucumberTest doit être dans ce package.
- Steps :
  La définition des steps des sénarios cucumber, elles utilisent les instances des pages web.
- Utils :
  Contient des classes utilitaires comme BaseTools cité ci-dessus.

## Utilisation
Pour lancer l'execution des tests :
### En local vous pouvez soit :
- Exécutez la commande suivante sur un terminal : `mvn clean install` et/ou `mvn clean test`
- Pour exécuter un scénario donné: `mvn test -Dcucumber.filter.tags="@tag_du_scenario"`
- Sur votre IDE, exécuter la classe `RunCucumberTest`.
- Pour spécifier le navigateur, ajoutez `SELENIUM_BROWSER = EDGE` dans les variables d'environnement de `RunCucuberTest`
    - Notez que par défaut, c'est le navigateur Chrome qui est utilisé.
### Sur un PTA soit :
- De votre IDE vers le PTA en ajoutant les variables d'environnement de `RunCucuberTest`
    - `SELENIUM_HUB_URL=https://selenium-laforge:fdNE8pE8U3tePnQgI5dr@selenium.laforge.cloud.bpifrance.fr/wd/hub`
- En lançant un pipeline sur Jenkins



## Documentation
- Documentation du projet : [lien vers Neurones](https://neurones.web.bpifrance.fr/pageCucumberRunner)

## Licence
Propriété de BPI France
## Auteurs
- Salim OUARET  (@M08838)
- Wassim ALLAL  (@M12609)
- Mouctar BARRY (@mouctar)

