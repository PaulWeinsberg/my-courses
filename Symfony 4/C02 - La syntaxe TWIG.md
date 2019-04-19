# CHAPITRE III : La syntaxe TWIG

Dans ce chapitre nous allons voir comment utiliser la syntaxe `TWIG`. Cette syntaxe n'est vraiment pas complexe à utiliser et va nous permettre de faciliter l'écriture et la lecture des templates HTML/PHP plus classiques.

## Qu'est-ce que TWIG ?

`TWIG` est un moteur de template créé et utilisé dans Symfony pour générer nos vues. `TWIG` à plusieurs avantages et ce frameworks peut être utilisé dans d'autre projets qui n'implémentent pas forcément le frameworks Symfony. Mais quels sont ces principaux avantages ? Sans aucuns doute la lisibilité du code et la mise en place d'un code complexe en un minimum de temps.

Un peu de pratique vous aidera à y voir plus clair, je vous propose donc de créer un projet annexe spécialement pour apprend à utiliser cette nouvelle syntaxe.

## Création d'un projet annexe

Dans un répertoire de votre choix, nous allons utiliser composer pour créer un nouveau projet n'implémentant que `TWIG` ceci nous permettra de bien isoler cette syntaxe du reste à apprendre sur le framework Symfony. D'ailleurs nous allons ici nous servir de notre serveur Apache car nous allons utiliser un site en PHP. Donc pensez bien à chaque modification à transférer vos modifications vers le dossier de votre serveur s'il est différent de celui de développement.

### Installation de Twig

Dans votre dossier nouvellement créé, taper la ligne suivante dans le terminal :

`composer init`

Puis une fois réalisé nous allons taper la commande suivante pour installer `Twig` ave composer :

`coposer require twig/twig:^2`

Voilà Twig est maintenant installé dans notre répertoire. Nous allons dans le prochain chapitre faire un bref rappel du système de routing classique puis celui utilisé avec Twig.

## Le routing

### Routing traditionnel

Nous allons créer un petit système de distribution de page suivant les requêtes contenues dans l'URL et donc dans la variable `$_GET` de php. Ces pages seront placées dans le dossier `templates` à la racine de notre dossier racine du projet.

Je vous invite à créer également un fichier dans le dossier `templates` que nous appelerons `home.php`. Ce fichier sera affiché par notre site web si le paramètre `page` contient `home` soit l'URL suivante :

`localhost/?page=home`

Voilà ne faisons pas compliqué pour le moment cela suffira amplement. Je vous propose de compléter le fichier `home.php` comme ceci :

```html
<h1>Page d'accueil</h1>
<p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Quam et numquam consequatur eum, laborum beatae blanditiis, magni quod suscipit qui harum ipsam iusto ullam corrupti totam id error odio cupiditate?Odit mollitia dolor exercitationem obcaecati repellat. Maxime iure delectus exercitationem ex labore magni recusandae praesentium cum, officia error totam vero autem ut corrupti! Quaerat reprehenderit hic molestias deleniti! Eaque, natus!</p>
```

Pour l'instant notre page ne contient donc que du code HTML et ce sera suffisant pour commencer. Je vous invite à créer également une page nommée `404.php` inutile ici de présiser son rôle et je vous laisse imaginer son contenu.

Pour ce qui est du fichier `index.php` voici ce que nous pourrions écrire pour notre petit système de routing :

```php
<?php

if (!isset($_GET['page'])) {
    $_GET['page'] = 'home';
}

switch ($_GET['page']) {
    case 'home':
        require './templates/home.php';
        break;
    default:
        require './templates/404.php';
        break;
}

?>
```

Avouez ne pas avoir vu plus simple comme système de routing depuis vos début. J'en convient il n'y a franchement pas pire mais bon ça fera l'affaire dans notre cas. Nous avons vu avec ce bref rappel comment fonctionne un routing classique.

### Routing Twig

Première chose nous allons préparer notre fichier `index.php` pour qu'il charge les dépendances, en première ligne de notre fichier inscrivez la ligne suivante :

`require './vendor/autoload.php';`

Voilà maintenant nous sommes fin prêt à utiliser Twig pour l'ensemble de nos fichiers chargés via notre index. Pour intégrer le routing disponible avec Twig nous allons devoir importer la classe `FilesystemLoader` depuis le namespace `\Twig\Loader\`. Cette classe va nous permettre de définir dans son constructeur une chaine de caractères contenant le path vers notre dossier contenant nos différents templates, cette classe sera créée par convention dans une variable appelée `$loader`, sachez qu'il est possible également d'avoir plusieur répertoires et dans ce cas la syntaxe sera différente et je vous laisse la découvrir en cas de besoin dans la documentation de Twig. Pour l'instant voici notre nouvelle ligne :

```php
$loader = new \Twig\Loader\FilesystemLoader('./templates');
```

Pour continuer nous allons maintenant créer une instance de Twig et de sa classe `Environment` qui se trouve dans le namespace `\Twig\`, cette classe prend en paramètre deux chose, la première est l'instance de `FilesystemLoader` que nous avons assigné à la variable `$loader` la seconde est un tableau associatif qui permet très facilement de créer un système de cache, ce que nous n'utiliserons pas car nous allons régulièrement modifier nos pages. Nous nommerons cette nouvelle instance `$twig`, toujours pour des raison de convention.

```php
$loader = new \Twig\Loader\FilesystemLoader('./templates');
$twig = new \Twig\Environment($loader, [
    'cache' => false
]);
```
Le second paramètre n'étant pas obligatoire j'ai choisi d'inscrire `false` pour que vous compreniez son fonctionnement. Si vous remplacez le booléen pas une chaine de caractère pointant vers un dossier alors celui-ci contiendra vos mise en cache, tout simplement. D'ailleur voici les options que vous pouvez renseigner :

* `cache` : 

Maintenant nous avons tous les outils pour utiliser `Twig` et notre instance de `Environment` contient une méthode `render()`, elle prend en paramètre le nom de votre fichier à afficher, sous forme de string et présent dans le dossier `templates`. Cette méthode doit être précédée de la fonction php `echo` sinon elle ne fera que s'exécuter et parser le document sans afficher quoi que ce soit, même pas une erreur, pensez-y. Voici donc notre fichier index au complet reconfectionné :

```php
<?php

require './vendor/autoload.php';

$loader = new \Twig\Loader\FilesystemLoader('./templates');
$twig = new \Twig\Environment($loader, [
    'cache' => false
]);

if (!isset($_GET['page'])) {
    $_GET['page'] = 'home';
}

switch ($_GET['page']) {
    case 'home':
        echo $twig->render('home.php');
        break;
    default:
        echo $twig->render('404.php');
        break;
}

?>
```

Celui fonctionne exactement pareil vu du côté du navigateur. En effet tout HTML valide est du twig également valide, cependant attention ce n'est pas le cas pour PHP !

## 
