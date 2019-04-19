# CHAPITRE I : INTRODUCTION

Dans ce chapitre je vous propose de voir globalement ce que peut proposer l'utilisation d'un framework comme Symfony mais également de revenir sur son histoire. Nous ne rentrerons pas dans les détails car cela reviendrait à commencer le cours dès cette petite introduction. Nous verrons également comment installer Symfony sur un serveur Linux. Notez qu'avec le PowerShell de Windows vous avez la possibilité d'installer une version de Debian et ainsi ne pas avoir besoin de switcher sur Linux. Sinon vous pouvez également utiliser un logiciel comme WAMP qui pourra faire office de serveur HTTP Apache/MySQL et PHP sur votre bécane.

#### Symfony un framework PHP

Symfony est plus qu'un simple fraamework c'est le framework le plus utiliser dans le monde pour ce qui concerne les frameworks PHP. Il permet, comme de nombreux frameworks de faciliter le travail d'équipe en imposant une structure et des règles d'implémentation à respecter. Outre le travail d'équipe, utiliser un framework permet de mieux sécuriser son site web car beaucoup d'utilisateurs de ce framework contribuent à son développement mais également aux aspects plus sécuritaire. La dernière chose qui parrait peut-être évidente pour certains mais peut-être pas pour les débutants c'est qu'un framework qui ce respect facilite le développement et en diminue le temps. Evidemment en partant du principe que le framework ne vous est pas inconnu.

#### L'histoire de Symfony

Symfony est donc un framework basé sur le langage PHP et créé par une boite nommée **SensioLabs** en 2005 et pour ces propres besoin. Le framework est ensuite rendu public via l'Open Source et beaucoup de développeur y contribuent régulièrement. En quelques chiffres le framework Symfony à passé la barre du milliard de téléchargements. Petite informations pour votre égo `SensioLabs` est une boite française... Cocorico.

## Installation

#### Les dépendances

Avant d'installer le framework à proprement parler il va nous falloir installer plusieur dépendances absolument nécessaire pour la suite de ce cours :

* Apache2 : Serveur HTTP.
* PHP7.X : Interpreteur PHP.
* MySQL ou MariaDB : Pour la gestion de base de données.
* Composer : Gestionnaire de dépendances pour projets PHP.

**La démarche ci-dessous est fonctionnelle sous Debian 9.8 et Windows PowerShell avec une Debian 9.8 également :**

###### Apache 2

Dans un premier temps nous allons installer apache2 pour cela il vous suffit de taper l'instruction suivante dans votre shell bash :

`sudo apt-get install apache2`

Pour vérifier si cela fonctionne, lancer la commande suivante :

`sudo service apache2 start`

Si tout est bon votre serveur devrait être démarrer et l'adresse `localhost` dans votre navigateur devrait afficher une page HTML de Apache2 pour Debian. Si ce n'est pas le cas je vous laisse rechercher la problèmatique, il y a de nombreuses documentations sur le sujet.

###### MySQL

Pour MySQL rien de bien compliqué non plus la commande suivante installera aussi bien le client que le serveur :

`sudo apt install mysql-server mysql-client`

Une fois l'installation terminée vous pouvez vérifier le fonctionnement de votre BDD comme ceci :

`sudo service mysql start`

Si cela fonctionne vous pouvez ouvrir un second terminal pour vous connecter :

`mysql -u root`

Vous devriez maintenant être connecter à votre serveur MySQL. Si ce n'est pas le cas, documentation et recherche vous aiderons.

###### PHP 7.X

Nous allons également installer les dernières versions de PHP et pour cela vous pouvez taper la commande suivante :

`sudo apt install php-common libapache2-mod-php php-cli php-mysql php-curl`

Pour vérifier si l'installation s'est bien effectuée taper la commande suivante elle devrait inscrire hello dans la console puis quitter l'interpreteur avec `Ctrl+C`.

`php echo 'Hello';`

Si votre console affiche la chaine de caractères demandée votre interpreteur est fonctionnel.

###### Composer

Maintenant nous allons installer de manière global le gestionnaire de paquet composer et pour cela rien de plus facile

`sudo apt-get install composer`

Testez le fonctionnement de composer avec la commande suivante :

`composer -V`

Si la version de composer s'affiche alors composer est correctement installé. Sinon vous avez la possibilité de l'installer via l'interpreteur PHP comme indiqué sur la documentation de composer que je vous laisse découvrir si besoin.

Nous avons maintenant l'ensemble des dépendances absolument nécessaires au fonctionnement du frameworks Symfony, évidemment nous ne verrons pas ici en détail les différentes configurations à mettre en places pour Apache, MySQL et PHP sachez que vous trouverez de la documentation parfaitement claire et déjà rédifée de nombreuses fois pour ces aspects de notre projet.


## Installation de Symfony

Chez Symfony on est cool et les développeur nous on concocté un *petit* squelette de site web utilisant Symfony pour démarrer rapidemet un nouveau projet. Et devinez quoi ? Une ligne de commande et c'est réglé =). Enfin j'exagère un petit peu mais c'est presque ça. Alors dans notre terminal nous allons installer ce squelette dans un nouveau projet avec l'outil composer qui se chargera du téléchargement et de l'installation de l'ensemble, nous nommerons ce projet `website` parce que nous ne sommes pas là pour la créativité :

`composer create-project symfony/skeleton-website website`

Et voilà que composer ce charge du boulot. 

#### Un serveur simple et efficace

Maintenant que nous avons notre squelette Symfony je vous propose d'installer un serveur un peu différent de ce que vous avez utilisé jusqu'a présent si vous avez déjà utiliser un serveur Apache. Mais alors pourquoi l'avoir installé ? Et bien vous verrez pourquoi à la fin du cours en attendant nous allons nous faciliter la vie.

Pour installer ce fameux serveur à partir de votre terminal il vous suffit d'utiliser la commande suivante :

`composer require server --dev`

Le flag en fin de ligne permet à composer de comprendre que cette dépendance n'est la que le temps du développement de notre application et donc nous pourrons nous en dispenser pour la mise en production ou déploiement.

Un fois installer il vous suffira d'exécuter la commande suivante pour lancer votre serveur :

`php ./bin/console server:run`

Symfony met à disposition une petite librairie d'outils depuis le dossier `/bin` qui peuvent etre très utiles dans de nombreux cas et vont par la suite grandement nous simplifier la vie au cours du développement.

Rendez-vous dans votre navigateur et taper l'adresse suivante `localhost:8000`. Attention à bien saisir le port 8000 et non 80 qui est le port par défaut. Ce serveur peut tourner en même temps qu'un serveur apache sur votre machine ce qui peut être assez pratique.


#### Problème de version (autre que 4.X)

Lorsque vous utiliserez ce tutoriel il est fort probable que la version de PHP stable soit au minimum la 7.2 pour Debian si ce n'est pas encore le cas comme pour moi au moment ou j'écris ce paragraph il vous faudra alors installer les dépendances de php7.2. Réinstallez ensuite composer qui installera les dépendances liées au packages de php7.2. En effet si vous utiliser composer avec une version PHP antérieur à la 7.2 alors celui-ci installera automatiquement une version de Symfony antérieure, dans mon cas la 3.4.

