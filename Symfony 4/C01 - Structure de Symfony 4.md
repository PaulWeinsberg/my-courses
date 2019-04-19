# CHAPITRE II : COMPRENDRE LA STRUCTURE DE SYMFONY 4

Dans ce chapitre nous allons voir comment se compose le dossier racine de Symfony 4. Il est important de comprendre ces notions car elles vous permettrons de mieux intéragir avec le frameworks et rapidement comprendre comment l'utiliser et éviter des problèmatiques liées à une mauvaise interprétation de son fonctionnement structurel.

## La structure MVC

Avant toute chose quelques informations sur Symfony absolument nécessaires à comprendre avant d'aller plus loin. Symfony est un frmaeworks basé sur un modele MVC (model, view, controller) c'est à dire un système de :

* Model : Les models ou modèles correspondent aux classes, fonctions ou structures de données de l'application.
* View : Les views ou vues correspondent-elles au éléments affichés à l'utilisateur.
* Controller : Les controller ou contrôleurs, comme leur nom l'indique effectues diverses opération suivant les requêtes qu'ils recoivent pour afficher la page et ses informations correspondantes.

En gros comment fonctionne une architecture MVC ?

1. Le client envoie une requête au serveur via l'url saisie dans son navigateur.
2. Le fichier racine du serveur dirige la requête en fonction de l'URL (routes, paramètres, session etc...).
3. Il l'envoie au contrôleur associé suivant la route demandée via l'URL.
4. Le contrôleur traite les informations en fonctions de l'ensemble des informations transmises en différents models.
5. Une fois ce traitement effectué le contrôleur génère une vue (view) en fonction des résulats qu'il à obtenue.
6. La vue est renvoyée au client.
7. Le navigateur web affiche la vue.

Ce shéma mémotehcnique est particulièrement simplifié mais il permet d'en l'ensemble de résumer le fonctionnement d'un système adoptant une arhitecture MVC.

## La structure de fichiers

Maintenant voyons comment se structure Symfony juste après une installation sur notre machine.

#### Le dossier racine

Plutôt qu'une longue explication rien de vaut un petit shéma que je vous propose de voir sous forme de tableau descriptif.

|      Fichier        |                             Fonction                                     |
| ------------------- | ------------------------------------------------------------------------ |
├── bin/              | Librairie d'outils et de tests unitaires
├── config/           | Contient la configuration de notre projet au format YAML
├── public/           | Racine du site web
├── src/              | Contient le code PHP de l'application, il correspond au namespace App\
├── templates/        | Contient les vues de notre application au format TWIG
├── tests/            | Contient les tests fonctionnels et unitaires, il correspond au namespace App\tests\
├── translations/     | Contient les traductions pour notre application
├── var/              | Contient les logs et autres fichiers créés par l'application
├── vendor/           | Dossier propre de composeer, il contient ces dépendances
├── .env              | Configuration de l'environnement (prod ou dev)
├── .env.test         | Configuration de l'environnement de test
├── .gitignore        | No comment sinon apprendre Git en urgence
├── composer.json     | Fichier de dépendances du projet généré par composer
├── composer.lock     | Fichier de dépendances actuellement installée (ne pas toucher)
├── phpunit.xml.dist  | Fichier permettant à Symfony d'afficher les résultats de test
└── symfony.lock      | Informations sur les versions des packages de symfony (ne pas toucher)

Comme vous pouvez le constater nous nous sommes déjà servi du dossier `bin/` et de son sous fichier `console` pour lancer notre serveur. Vous avez également pu voir après l'installation de composer une recommandation vous indiquant la possibilité de configurer doctrine depuis le fichier `.env`. Nous y reviendrons par la suite. Continuons notre exploration pour en savoir un peu plus sur cette structure de fichiers.

## Les sous-dossiers essentiels à comprendre

Dans cette partie nous verrons uniquement les sous-dossiers dont le contenu va particulièrement nous intéresser pendant le développement de notre application, les autres sous-dossiers, bien qu'essentiels sont relativement simple à comprendre et un tour rapide sur la documentation de Symfony qui par ailleur est particulièrement bien explicite vous donnerons ses différentes informations.

### Le dossier config/

Le dossier `config/` est donc comme son nom l'indique un dossier contenant les configurations principaes liées à notre application Symfony et ses différentes dépendances, packages. Comme nous l'avons fait précédemment je vous propose un petit schéma récapitulatif de sa structure :


|               Fichiers                     |                          Fonction                                        |
| ------------------------------------------ | ------------------------------------------------------------------------ |
├── packages/                                |  Contient la configuration de chacun des packages de Symfony
│   ├── dev/                                 |  Contient les configurations de packages liées au développement
│   ├── prod/                                |  Contient les configurations de packages liées à la mise en production
│   ├── test/                                |  Idem mais pour les tests
│   ├── cache.yaml                           |  Fichier de configuration du package associé
│   ├── doctrine_migrations.yaml             |  Fichier de configuration du package associé
│   ├── doctrine.yaml                        |  Fichier de configuration du package associé
│   ├── framework.yaml                       |  Fichier de configuration du package associé
│   ├── routing.yaml                         |  Fichier de configuration du package associé
│   ├── security.yaml                        |  Fichier de configuration du package associé
│   ├── sensio_framework_extra.yaml          |  Fichier de configuration du package associé
│   ├── swiftmailer.yaml                     |  Fichier de configuration du package associé
│   ├── translation.yaml                     |  Fichier de configuration du package associé
│   ├── twig.yaml                            |  Fichier de configuration du package associé
│   └── validator.yaml                       |  Fichier de configuration du package associé
├── routes/                                  |  Contient diverses configuration sur les routes de notre application
│   ├── dev/                                 |  Contient les routes spécifiques au développement
│   └── annotations.yaml                     |  Comme son nom l'indique
├── bootstrap.php                            |  Fichier appelé lors du lancement de l'application (vérifie la config du fichier .env)
├── bundles.php                              |  Fichier d'importation des bundles ou packages Symfony
├── routes.yaml                              |  Fichiers de configuation des routes
└── services.yaml                            |  Nous reviendrons ssur ce fichier lors du chapitre sur les services


Vous en savez maintenant un peu plus sur ce dossier mais je ne rentrerais pas plus dans les détails cela serait inutile tant que vous n'avez pas pratiqué plus avec Symfony car nous risquerions de nous perdre dans des détails de fonctionnement pour l'instant peu utile à l'avancé de notre cours.


### Le dossier public/

Rapide précision sur ce dossier. Lorsque vous mettrez votre projet en production il est important de savoir qu'il s'agit de la racine de votre application il contient notamment et pour l'instant exclusivement le fameux fichier `index.php` que vous connaissez qurement déjà. Si ce n'est pas le cas ce cours est d'un niveau trop important pour le moment et je vous conseille de suivre les différents cours disponible sur PHP en amont.

Lorsque vous déploierez votre application pensez bien à faire pointer votre serveur Apache dans ce dossier.


### Le dossier src/



|               Fichiers                     |                          Fonction                                        |
| ------------------------------------------ | ------------------------------------------------------------------------ |
├── Controller                               |  Contient l'ensemble des contrôleurs de notre application
├── Entity                                   |  
├── Migrations                               |  
├── Repository                               |  
└── Kernel.php                               |  