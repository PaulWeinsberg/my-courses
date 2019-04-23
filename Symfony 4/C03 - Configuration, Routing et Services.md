# CHAPITRE III : CONFIGURATION

Dans ce chapitre nous allons rapidement effectuer quelques petites configuration pour démarrer avec Symfony.

## Symfony Profiler

Retournons sur la page d'accueil de notre framework Symfony en lançant le serveur PHP avec la console comme nous l'avions vu dans le premier chapitre de ce cours. Vous remarquerez en bas de notre page une petite barre indiquant diverses information sur le framework. Si vous cliquez sur l'une de ces information vous serez redirigé vers un dashboard.

Ce dashboard permet entre autre un accès visuel et détaillé des logs de Symfony en plus de nombreuses informations. C'est l'outil Profiler de Symfony et il va vous être très utile lors que vous rencontrerez des bugs ou autres problèmatiques.

Pas la peine ici de faire un tour complet du propriétaire, je crois qu'il est particulièrement simple de compréhension. Cependant nous y reviendrons plusieur fois dans ce cours. Vous pouvez également y accéder à l'adresse suivante `localhost:8000/_profiler` et il faudra ajouter votre token de session en fin d'URL. Le clique sur la barre en bas de tâche effectue cette action pour vous.

## Configuration de la base de données

Comme nous l'avons également vu dans le chapitre concernant la structure de Symfony, le fichier `.env` contient entre autre une constante `DATABASE_URL`. Cette constante contient plusieurs informations :

* Le système de la base de donnée (mysql par défaut).
* Le nom d'utilisateur pour s'y connecter
* Le mot de passe de l'utilisateur
* L'adresse IP (ou l'adresse URL de l'host)
* Le port de connection
* Le nom de la BDD

Le tout est concaténé en une seule ligne et donne le schéma suivant :

`systeme`://`nomUtilisateur`:`motDePasse`@`adresseHost`:`port`/`bomBDD`

Nous allons donc créer une nouvelle base de donnée sur notre machine avec MySQL qui doit être maîtrisé avant de commencer ce cours. Si ce n'est pas le cas je vous invite à découvrir MySQL et son fonctionnement avec PHP dans des cours précédent.

Nous appelerons notre nouvelle BDD `symfony_agency`. Une fois créé il nous suffit de renseigner la ligne, dans mon cas la connection sera celle-ci :

`mysql://root:@127.0.0.1:3306/symfony_agency`

Je ne renseigne pas de mot de passe car je n'en ai pas défini.

## Le routing

Symfony fonctionne avec un système de routing que nous allons devoir renseigner pour commencer à travailler sur nos différentes pages. Comme vous pouvez le constater lorsque vous allez dans le Profiler une erreur stipule que Symfony n'a pas trouvé de page correspondante à la racine de notre URL. C'est donc celle-ci que nous allons créer dans un premier temps.

Avant de commencer à utiliser le système de routing standard de symfony sachez qu'il en existe plusieur de valable. En effet, il existe trois façon de définir nos routes avec Symfony :

* `Yaml` : Avec le fichier de configuration `routes.yaml`.
* `Annotation` : Il est également possible d'utiliser des annotation au sein des controleurs.
* `PHP` : Enfin on peut diretement utiliser PHP et certaines fonctions de Symfony

Le format `Yaml` étant celui mis en avant dans Symfony c'est celui que nous utiliserons dans ce chapitre. Son avantage principal est d'offrir une très bonne lisibilité, personnelement je préfère revenir sur ce type de routing dans le cas de maintenance d'un projet car reprendre le code à ce niveau est plus facile.

### Ajout d'une nouvelle route

Rendez-vous dans le dossier config et dans la fichier `routes.yaml`, celui-ci contient les informations relatives au différentes routes de notre applications. Les routes prennent en comptent différentes informations

* Le nom de notre route
* L'adresse à laquelle elle correspond
* Le nom de controleur associé à celle-ci
* La méthode du controleur à appeler lorsque la route est demandée

Voici la syntaxe pour créer une route :

```yaml
home: # Le nom de notre toute
    path: / # L'adresse à laquelle elle correspond
    controller: App\Controller\HomeController::index # Le controleur correspondant et sa méthode
```

### Création de notre premier controleur

Voilà, il nous faut maintenant créer le controleur correspondant à notre nouvelle route home, que nous avons décidé de nommer `HomeController`. Ce fichier `HomeController.php` ce retrouvera donc dans `src/Controller/` soit le namespace correspondant à `App\Controller` que nous allons devoir préciser.

Notre controleur doit également avoir une méthode public `index` qui retourne une instance de la classe `Response` du namespace `Symfony\Component\HttpFoundation`. Je vous propose dans un premier temps de retourner simplement un petit `Hello World !`. Voici donc notre premier controleur :

```php
<?php
namespace App\Controller;
use Symfony\Component\HttpFoundation\Response;

class HomeController {

	public function index(): Response {
		return new Response('Hello World !');
	}

}
?>
```

Notre page ne fait pour l'instant rien d'extraordinaire et comme nous l'avons vu dans le chapitre précédent il serait intéressant d'utiliser `Twig`.

Pour commencer nous allons créer une simple page `twig` dans notre dossier `templates` qui contiendra un dossier `pages`. Dans ce dossier notre page se nommera `home.html.twig` et contiendra le code suivant pour le moment :

```html
<h1>Hello World !</h1>
```

## Les services

Twig fait parti des services déjà présents et facilement implémentables dans nos controleurs. Il dispose également d'un système d'autowiring que nous verons dans une seconde partie, pour l'instant nous allons voir comment ajouter un service à notre controleur de façon standard.

Les services sont défini dans le fichier `config/services.yaml`. L'objectif ici va être de passer une variable `$twig` contenant une instance de `Twig\Environment`. C'est ce que nous allons définir dans notre fichier `yaml`. Plutôt qu'on long discours voici les lignes de code commentés pour réaliser l'opération :


```yaml
    App\Controller\HomeController: # Nous précisons le namespace et le controleur affecté
        tags: ['controller.service_arguments'] # Ici nous indiquons qu'ils s'agit d'un service passé en argument
        arguments: # En dessous ce seront les arguments passés
            $twig: '@twig' # On présice passer l'argument $twig qui contiendra le service twig
```

Les services doivent être précédés d'un arobase `@` comme vous pouvez le constater. Pour voir la liste des services présents dans Symfony il suffit d'utiliser la console. La ligne suivante indique les service disponibles :

`php bin/console debug:container`

Vous retrouverez le service `twig` que nous venons d'utiliser. Maintenant notre controleur doit impérativement avoir un argument `$twig` dans son constructeur sinon cela générera une erreur. Pour cela il nous suffit de créer le constructeur de la classe de notre controleur et de lui passer en argument. Evidemment nous allons devoir créer une propriété qui contiendra l'argument que nous venons de passer en paramètre.

```php
	private $_twig;

	public function __construct($twig) {
		$this->_twig = $twig;
	}
```

Il ne nous reste plus qu'à modifier notre méthode index retournant une instance de `Response`. Elle prendra en argument la méthode `render()` de la propriété `_twig` :


```php
	public function index(): Response {
		return new Response($this->_twig->render('pages/home.html.twig'));
	}
```

Voilà notre méthode pointe maintenant vers le template que nous venons de créer précédemment, tentez de recharger votre site web et le résultat devrait s'afficher sinon vous avez surement un oubli dans votre code. On peut donc en déduire que notre configuration à automatiquement injecter le service après avoir récupéré les informations présentes dans le fichier `yaml`. Ce qui nous évite d'avoir à préciser le namespace de `Twig` dans notre controleur. 

### L'autowiring

Un peu plus tôt je vous ai parlé du système d'autowiring de Symfony, alors comment fonctionne-t-il et surtout à quoi sert-il ? Et bien pour vous illustrer sont intérêt imaginez vous avoir à injecter le service twig dans tous vos contrôleur et bien ce serait fastidieux car au final vous allez vous répéter sans pour autant faire de modification intéressante ou nécessaires.

Pour voir la liste des services disponibles avec l'autowiring il suffit de taper la commande suivante dans votre terminal :

`php bin/console debug:autowiring`

On peut voir que Twig est disponible et à partir de là nous pouvons donc l'utiliser directement sans passer par un service. C'est du coup la méthode que nous allons utiliser. rendez-vous dans votre fichier des services `services.yaml` et supprimez les lignes que nous venons de créer.

Allons maintenant dans le fichier de notre controleur et importons le namespace spécifié dans la console c'est à dire `Twig\Environment` :

```php
use Twig\Environment;
```

Enfin pour notre constructeur :

```php
	public function __construct(Environment $twig) {
		$this->_twig = $twig;
	}
```

Le reste du code est identique, vous pouvez maintenant recharger votre page et constater que cela fonctionne de la même façon. Il existe également une autre syntaxe qui permet de ne pas avoir à utiliser use en haut de notre page :

```php
	public function __construct(\Twig\Environment $twig) {
		$this->_twig = $twig;
	}
```

De cette façon cela fonctionne également mais retire de la lisibilité. C'est donc à vous de choisir ce que vous préférez utiliser. Dans le prochain chapitre nous ferons essentiellement de la mise en forme pour nos pages, je vous donnerai la majeur parti du code à réaliser.