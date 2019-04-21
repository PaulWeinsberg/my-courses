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

* `charset` : Doit contenir le nom de l'encodage sous forme de string, par défaut UTF8.
* `cache` : Doit contenir une chemin sous forme de string vers le dossier contenant les fichiers de cache.
* `debug` : Doit contenir un booléen, si true les noeud peuvent utiliser une méthode `dump()` qui facilite le debogage (attention une extension est nécessaire nous y reviendrons).
* `base_template_class` : Contient sous forme de string le nom de classe à utiliser pour générer les templates. Par défaut Twig_Templates.
* `autoescape` : Permet d'échapper les balises et autres caratères spécifiques au différents langages utilisés. `false` pour désactiver l'option.

D'autres existe et vous pouvez aisemment les découvrir dans la documentation, ici je n'ai répertorié qe les plus utilisés en général.

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

## La syntaxe de Twig

Avant d'utiliser la syntaxe de Twig je vous recommande de modifier les extensions de vos fichiers présents dans le dossier `templates`. En effet, toutes extension fonctionnent car le parsing de Twig n'en dépend pas en revanche garder une extension `.php` peut facilement porter à confusion surtout que ce langage ne peut pas être utilisé de façon classique avec. Deuxième intérêt, votre IDE et la reconnaissance de la syntaxe. Beaucoup d'IDE comprennent la syntaxe de twig et demandent en général l'extension twig pour se baser sur sa syntaxe sans avoir besoin de configurer chacun de nos pages. Je vous propose donc d'utiliser l'extension suivante `.html.twig` comme c'est le cas nativement dans Symfony. N'oubliez pas de modifier les extensions des fichiers appelés dans votre fichier `index.php`.

Mettez à jour vos fichiers et rechargez votre page. Rien ne devrait être différent et c'est tout à fait normal car Twig comprend le HTML comme n'importe qu'elle autre fichier HTML.

### Afficher des variables

Avec Twig il est possible d'afficher des variable dans notre template en utilisant une syntaxe simple `{{ maVariable }}`. Entre ces deux crochets s'affichera donc le contenu de `maVariable` si celle-ci est passée au template lors de l'appel de la méthode `render()`. Ces variables doivent être passées sous forme de tableau assiciatif ou sous forme d'objet. Nous allons voir ces deux méthodes.

#### Afficher notre variable

Dans un premier temps nous allons modifier notre code du template `home.html.twig` pour que celui-ci affiche le nom de notre page :

```html
<h1>Bienvenue sur la page {{ title }}</h1>
```

Ici notre page demande à afficher le contenu de la variable `title`. Tentez d'actualiser votre page, vous constaterez que lorsqu'une variable est inexistante Twig ne leve pas d'exception. C'est un des ces avantage.

#### Passer un tableau associatif

Il nous faut maintenant passer une variable dans un tableau ou un objet pour pouvoir afficher son contenu. Ce que nous allons faire avec la première des deux solutions. Pour cela rendez-vous dans votre `index.php` et modifiez les lignes suivantes comme suit :

```php
    case 'home':
        echo $twig->render('home.html.twig', [
            'title' => 'Accueil'
        ]);
```

Maintenant recharger votre page et voyez comment cela fonctionne. C'est bon dans votre titre vous pouvez voir s'afficher Bienvenue sur la page Accueil. Il est bien sûr possible de passer un grand nombre de variables dans un tableau comme celui-ci. Il est également possible de passer un tableau dans un tableau et c'est ce que nous allons voir. Pour cela créons dans notre tableau une variable `page` contenant un titre `title` et un paragraph `paraph` comme ceci :

```php
    case 'home':
        echo $twig->render('home.html.twig', [
            'page' => [
                'title' => 'Accueil',
                'paraph' => 'Mon incroyable paragraph'
            ]
        ]);
```

Pour utiliser ces sous élément Twig utilise une syntaxe proche du JavaScript et différente de PHP. En effet pour afficher un sous élément on utilisera un simple `.`. Ce qui veut dire que pour afficher `title` du tableau `page` la syntaxe sera : `page.title`. Essayons ceci avec notre page `home` :

```html
<h1>Bienvenue sur la page {{ page.title }}</h1>
<p>{{ page.paraph }}</p>
```

Comme vous pouvez le constaer le contenu s'affiche comme nous le souhaitons. Maintenant ajoutez les balises HTML `<strong></strong>` entre `Mon incroyable paragraph` et vous pourez également constater que les balise s'affiche comme du texte. C'est normal car Twig echape naturellement les balise HTML ce qui nous facilite également la vie.

#### Passer un objet

Il est également possible de passer un objet ce qui est également plus courant lors qu'on utilise le paradigme de la POO. Créons donc un objet qui ressemblera à notre tableau précédent dans sa structure ce qui nous évitera de modifier notre template `home` et en gardant un resultat identique. Dans notre fichier `index.php` nous allons créer l'objet `$page` qui contiendra deux propriétés `title` et `paraph.

```php
class Page {

    public $title;
    public $paraph;

    function __construct($title,$paraph) {
        $this->title = $title;
        $this->paraph = $paraph;
    }
}

$page = new Page('Accueil','Mon incroyable paragraph');
```

Maintenant que nous avons notre object `$page` nous allons le lier à la clé de `'page'` du tableau associatif passé en paramètre de la méthode `render()` ce qui nous donnera ceci :

```php
    case 'home':
        echo $twig->render('home.html.twig',[
            'page' => $page
            ]);
        break;
```

Vous pouvez recharger la page et constater simplement que l'ensemble s'affiche comme dans le cas précédent. Bien sûr dans cet exemple tout n'est pas parfait. Nous n'avons pas respecté la syntaxe pour les propriétés de classe PHP qui doivent normalement commencer par un underscore mais dans notre exemple cela suffit. 

Vous pouvez également utiliser des méthodes dans votre template Twig. Pour cela il suffit d'appeler la méthode exactement comme pour une propriété et donc sans les parenthèses. Je vous propose de créer la méthode suivante dans notre classe :

```php
    function getTitle() {
        return $this->title;
    }
```

Puis dans notre template à la place de `page.title` utilisez `page.getTitle` et vous pourez constater que l'ensemble fonctionne à l'identique.


### Tags et système de block

Twig permet de répondre à une problèmatique courante lors de la confection d'un site web. Le layout. En effet personne n'a envie de se répeter à chaque fois que l'on veut créer une page et y inclure le header le footer etc etc. Bien sûr vous connaissez déjà la solution avec PHP en utilisant `include` mais avec Twig la syntaxe est légèrement différente et nous allons utiliser un système de block. L'ensemble de ce que nous verrons dans cette partie font parties de la famille des `tags` Twig et prennent la syntaxe suivante `{% tag %}`.

#### Créer un layout

Pour créer un layout il vous suffit de créer un template classique, ici nous le nommerons `layout.html.twig`. Et nous allons créer dans ce template un `head` pour importer bootstrap et rendre notre page plus jolie. Nous y incluerons un block `content` et pour ce faire nous utiliserons la syntaxe suivante `{% block nomdublock %}` directement suivi de `{% endblock %}`. Ces deux balise nous permettrons d'inclure le contenu que nous souhaitons dans les `templates` utilisant ce layout. Voici donc notre template :

```html
<head>
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
</head>
{% block content %}{% endblock %}
```

Pour utiliser ce layout dans notre template `home` nous allons devoir préciser que ce template hérite du template `layout.html.twig` et pour cela il existe une instruction faisant penser à la POO : `{% extends 'layout.html.twig' %}`. Ensuite nous ne pouvons pas simplement inclure cette ligne sans quoi cela provoquerait une erreur fatale car nous n'avons pas précisé ou se trouvaient les lignes en dessous et ce que nous voulons faire c'est les inclure dans notre block `content` alors nous utiliserons la même syntaxe que dans template `layout`. Voici notre page ce qui sera plus parlant :

```html
{% extends 'layout.html.twig' %}
{% block content %}
	<h1>Bienvenue sur la page {{ page.getTitle }}</h1>
	<p>{{ page.paraph }}</p>
{% endblock %}
```

En rechargeant la page vous constaterez que Bootstrap à bien était chargé et que votre contenu est donc stylisé avec celui-ci. Pour aller un peu plus loins je vous propose d'inclure une barre de navigation Bootstrap sur chacun de nos `templates` qui héritent de `layout`. Le template `404` doit également hériter de `layout` comme nous 'avons fait pour `home`, je vous laisse faire la transition et dans notre template layout voilà comment ajouter notre barre de navigation :

```html
<head>
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
</head>
<body>
	<header>
	<nav class="navbar navbar-expand-lg navbar-light bg-light">
		<a class="navbar-brand" href="#">Navbar</a>
		<button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
			<span class="navbar-toggler-icon"></span>
		</button>
		<div class="collapse navbar-collapse" id="navbarNav">
			<ul class="navbar-nav">
				<li class="nav-item">
					<a class="nav-link" href="./?page=home">Home</a>
				</li>
				<li class="nav-item">
					<a class="nav-link" href="./?page=articles">Articles</a>
				</li>
			</ul>
		</div>
	</nav>
	</header>
	<div class="container">
		<div class="row">
			<div class="col-12 mt-3">
				{% block content %}{% endblock %}
			</div>
		</div>
	</div>
</body>
```

Maintenant vous pouvez naviguer. Bien sûr la page articles n'existe pas et renvoie une erreur 404 comme définie dans notre index. Sachez également que ce système de layout peut s'appliquer à plusieurs niveau de hiérarchie. Il est possible par exemple de créer un layout avec sidebar qui hériterait du layout que nous avons créé puis d'utiliser ensuite le layout avec sidebar ce qui est très pratique lorsque l'on veut créer des pages différentes sans pour autant refaire l'ensemble d'un layout. Il est également à savoir que chaque block déclarer dans un layout enfant et dans un parent ne seront pas dupliqués. Le block enfant écrase le block du layout parent. Dernière chose à se sujet il est parfois nécessaire d'utiliser la fonction `include` et il suffit de l'utiliser comme d'habitude mais avec la syntaxe d'insertion de variable soit `{{ inlude './path/to/file' }}`.

#### Les boucles

Pour la suite de ce cours nous allons simuler un tableau d'objet retourné par une base de données. Pour cela nous allons créer une classe `Person` et instancier 5 objet à partir de celle-ci. Rien de bien compliqué ici, ces instances contiendrons uniquement un prénom, un nom et une date comme propriétés. Enfin nous mettrons ces objets dans un tableau associatif `$persons` que nous passerons en paramètre de la méthode `render()`. Enfin nous utiliserons une boucle dans notre template `home` pour afficher sous forme de tableau les nom et prénom contenu dans chacun des objets.

A la racine de notre site web nous allons créer un fichier persons.php qui sera chargé de retourner justement notre tableau, il contiendra également la classe `Person` et la création de chacune des instances :

```php
<?php

class Person {

	public $_firstname;
	public $_lastname;
	public $_date;

	function __construct($firstname,$lastname) {
		$this->_firstname = $firstname;
		$this->_lastname = $lastname;
		$this->_date = date('m-d-Y H:i:s',1494511938);
	}
}

$jean = new Person('Jean','Rivière');
$marine = new Person('Marine','Gauduchon');
$marc = new Person('Marc','Bertaud');
$jeanLuc = new Person('Jean-luc','Monetti');
$paul = new Person('Paul','Dujardin');

$persons = [$jean,$marine,$marc,$jeanLuc,$paul];

?>
```

Ensuite nous incluons justement cette classe dans notre fichier `index.php` :

```php
include './persons.php';
```
Puis nous passons le tableau `$persons` en paramètre de `render()` :

```php
    case 'home':
        echo $twig->render('home.html.twig',[
            'page' => $page,
            'persons' => $persons
            ]);
        break;
```

Maintenant notre template `home` peut accéder au tableau d'instances que nous venons de créer, nous allons créer un tableau HTML avec Bootstrap et plutôt que de créer une nouvelle ligne `tr` et de ce répeter nous allons utiliser une boucle avec `Twig` dont la syntaxe est `{% for item of array %}``{% endfor %}` :

```html
{% extends 'layout.html.twig' %}
{% block content %}
	<h1>Bienvenue sur la page {{ page.getTitle }}</h1>
	<p>{{ page.paraph }}</p>
	<div class="mt-2">
		<table class="table border">
			<thead class="thead-light">
				<tr>
					<th>N°</th>
					<th>Prénom</th>
					<th>Nom</th>
					<th>Date</th>
				</tr>
			</thead>
			<tbody>
			{% for person in persons %}
				<tr>
					<td>{{ loop.index }}</td>
					<td>{{ person._firstname }}</td>
					<td>{{ person._lastname }}</td>
					<td>{{ person._date }}</td>
				</tr>
			{% endfor %}
			</tbody>
		</table>	
	</div>
{% endblock %}
```

A l'intérieur d'une bloucle vous pouvez utiliser des variable avec la syntaxe `{{ maVariable }}` qui sont relatives aux boucles. Par exemple `loop.index` renvoie l'itération en cours de la boucle et commence par 1, c'est celle dont nous nous sommes servi pour afficher les numéro de notre liste. Il est possible d'utiliser `loop.index0` qui dans ce cas commence à 0. D'autres, très pratiques sont disponibles dans la documentation.

#### Autres tags

Il existe unn grand nombre d'autrestags constamment utilisés comme `if` cependant je vous laisse rechercher dans la documentation car vous les présenter un à un reviendrait à refaire une documentation qui est déjà bien complète et bien faite, l'objectif ici étant de comprendre les grands principes et ainsi vous donner un maximum d'autonomie en un minimum de temps.

### Utiliser les filtres

Les filtres permettent de modifier le contenu d'une variable simplement. Ils ressemble au pipes que l'on utilise avec Angular dans leur syntaxe et leur utilisation. Un filtre s'utilise dans l'insertion d'une variable donc entre les crochets et doivent être précédés par un `|` ce qui donne la syntaxe suivante `{{ maVariable | monFiltre }}`. Les filtres peuvent être particulièrement utile dans de nombreux cas par exemple nous pouvons facilement modifier la date que nous avons créé dans nos instances de `Person` avec le filtre date :

```html
<td>{{ person._date | date('\\l\\e d-m-Y \\à H:i')}}</td>
```

Nous pouvons constater que notre date à maintenant une valeur différente mais est bien basée sur la date que nous avons renseignée dans notre propriété `$_date`. Nous pouvons utiliser énormément de filtres comme par exemple `upper` et `lower` qui respectivement mettent en majuscules ou minuscules les caractères des string sur lesquelles ils s'appliquent. D'autre sont beaucoup plus spécifique comme `nl2br` qui permet de transformer les `\n` d'une string en `<br>` et permettre ainsi le retour à la ligne en HTML.

### Ajouter des fonctions, filtres et autres

En avançant avec Twig vous allez vous rendre rapidement compte que vous n'avez pas toutes les fonctions ou filtres que vous souhaiteriez avoir? Mais pas d'inquiétude, les développeur on pensez à vous et nous permettent avec des méthodes prévues pour cela d'ajouter nos propres outils à twig. Ces méthodes s'utilisent sur l'instance de `\Twig\Environment` et donc dans notre cas sur l'objet `$twig`, voici les principales à connaitre :

* `addFunction` : Permet d'ajouter une fonction.
* `addFilter` : Permet d'ajouter un filtre.
* `addGlobal` : Permet d'ajouter une variable global.
* `addExtension` : Permet d'ajouter une extension.

Ce que je vous propose pour comprendre un peu ce principe c'est d'inclure une fonction puis un filtre permettant tout deux d'afficher du texte au format Markdown. Dans un premier temps ajoutons le package `Michelf/php-markdown` de composer à notre projet :

`composer require michelf/php-markdown`

Pour tranformer du texte markdown en html la classe `Markdown` du namespace `Michelf\` contient une méthode `defaultTransform()` et prend en paramètre la valeur à transformer avant de la retourner au bon format.

Mais avant de nous servir de cette méthode nous devons créer une instance de la classe `\Twig\TwigFunction()` elle prend en paramètre le nom au format string de la fonction à appeler dans nos templates Twig et en second paramètre la fonction à réaliser. Voici comment nous pourrions utiliser ce principe, dans le fichier `index.php` :

```php
use Michelf\Markdown;
$markdown = new \Twig\TwigFunction('markdown', function($value) {
    return Markdown::defaultTransform($value);
});
```

Nous devons ensuite utiliser la méthode `addFunction` de notre objet `$twig` et lui passer l'instance de `TwigFunction` que nous venons de créer :

```php
$twig->addFunction($markdown);
```

Pour utiliser notre fonction au sein de notre template on utilisera la syntaxe suivante :

```html
<p>{{ markdown('# Salut') }}</p>
```

Cependant nous avons un problème `Salut` devrait s'afficher en `<h1>` mais twig échape le html... Heureusement nous avons une solution toute prêt il suffit de préciser dans les paramètres de notre function et en 3ième position le tableau suivant qui permettra de préciser à twig que ce que retourne notre fonction est sécurisé :

```php
use Michelf\Markdown;
$markdown = new \Twig\TwigFunction('markdown', function($value) {
    return Markdown::defaultTransform($value);
}, ['is_safe' => ['html']]);
```

Et voilà `Salut` s'affiche maintenant en appliquant le format Markdown à notre html. C'est pas beau ça ? Pour faire la même chose mais avec un filtre il suffit de changer 3 choses dans notre code :

* L'instance TwigFunction par TwigFilter
* addFunction par addFilter
* Et enfin modifier la syntaxe du template Twig.

Ce qui nous donne ceci dans le fichier `index.php` :

```php
use Michelf\Markdown;
$markdown = new \Twig\TwigFilter('markdown', function($value) {
    return Markdown::defaultTransform($value);
}, ['is_safe' => ['html']]);

$twig->addFilter($markdown);
```

Et dans notre template `home` :

```html
<p>{{ '# Salut' | markdown }}</p>
```

Et voilà ! Rien de plus facile n'est-ce pas ?

### Les extensions

Les extensions peuvent être soit créer soit importer depuis la librairie de twig ou une autre. Quoi qu'il en soit elle doivent être inclues dans l'objet `$twig` pour pouvoir être fonctionnelle. 

#### Les extensions natives de Twig

Comme pour les filtres et les fonctions on utilise la syntaxe `addExtension()` pour ajouter une extension. D'ailleur nous avions vu dans le début du cours qu'il était possible d'ajouter une fonction `dump()` pour faciliter le débogage et bien celle-ci nécessite une extension. Je vous propose de l'implémenter dans notre fichier index :

```php
$twig->addExtension(new \Twig\Extension\DebugExtension);
```

N'oubliez pas de mettre `'debug'` sur `true` dans les options de `\Twig\Environment` :

```php
$twig = new \Twig\Environment($loader, [
    'cache' => false,
    'debug' => true,
]);
```

Maintenant testons notre fonction `dump()` directement sur notre tableau d'objet personne et dans notre template `home` :

```html
<pre>
{{ dump(persons) }}
</pre>
```

Les balise `<pre>` ne sont pas obligatoires mais permettent un meilleures affichage. Rechargez votre page et vous pourrez constater que votre tableau s'affiche exactement comme un `var_dump()`. Vous trouverez d'autre extensions intéressantes directement dans la documentation de Twig prévue à cet effet.

#### Créer une extension

Un extension va simplement nous permettre de créer et insérrer à l'interieur de celle-ci nos filtres, fonction et autres spécifités de Twig. Ce qui est très pratique car cela évite de répéter `addFunction()` ou autre méthode d'insertion à de nombreuse reprises. Je vous propose d'insérer notre filtre et notre fonction Markdown à l'intérieur d'une extension que nous allons créer.

Pour commencer in faut savoir que les extensions de Twig doivent hériter de la classe `\Twig\Extension\AbstractExtension` je vous invite d'ailleur à aller voir ce fichier dans le path de votre votre dossier racine suivant : `./vendor/Twig/src/Extension` vous comprendrez mieux le fonctionnement de cette classe abstraite.

Nous pouvons constater que les méthodes de la classe doivent retourner un tableau. Ce tableau doit être composé d'instaces des classes pour créer nos éléments. Par exemple la méthodes `getFunctions()` retourne un tableau d'instances de `\Twig\TwigFunction`. Il nous suffit d'inclure les instances que nous avons réalisées dans notre index. En premier lieu créez un dossier `extension` à la racin de votre site puis ajoutez un fichier `myextension.php` Nous utiliserons un namespace pour notre nouvelle classe que j'ai choisi de nommer `Custom\Extension`. Notre classe se nommera simplement `MyExtension`. Voici ce à quoi elle pourrait ressembler :

```php
<?php
namespace Custom\Extension;

class MyExtension extends \Twig\Extension\AbstractExtension {

	public function getFilters() {
		return [
			new \Twig\TwigFilter('markdown', function($value) {
				return \Michelf\Markdown::defaultTransform($value);
			}, ['is_safe' => ['html']])
		];
	}

	public function getFunctions() {
		return [
			new \Twig\TwigFunction('markdown', function($value) {
				return \Michelf\Markdown::defaultTransform($value);
			}, ['is_safe' => ['html']])
		];
	}

}
?>
```

Voilà maintenant il nous faut supprimer le code inutile de la partie index c'est à dire celui relié au filtre et à la fonciton déplacés. Puis d'ajouter les lignes suivantes :

```php
require './extension/myextension.php';
// du code ..........
$twig->addExtension(new \Custom\Extension\MyExtension());
```

Maintenant utiliser le filtre et la fonction markdown dans votre template. Vous constaterez que tous fonctionne comme auparavant excepté que votre code est beaucoup mieux organisé.


### Les macros

Les macros permettent de générer facilement du code répétitif comme cela peut être le cas pour les formulaires par exemple. Elles fonctionnent de façon très similaire à une fonction car elles peuvent prendre des paramètre en compte et changer de comportement en fonction de ceux ci.

Pour illustrer leur fonctionnement nous allons créer une macro permettant de générer un formulaire de type Bootstrap et plus précisement ces différents inputs. Commençons donc à créer un dossier `macros` dans notre dossier `templates`, celui contiendra un fichier que nous nommerons `form.html.twig`. Dans ce fichier npous allons utiliser le tag `macro` et générer notre première macro. Celle-ci s'appelera `input` et prendra en paramètre le type, la valeur des attributs name, id ainsi que le nom du label. Nous ajouterons une condition qui dira *Si label est défini alors j'affiche une balise label qui contiendra la valeur transmise et l'id de l'input*.

Voilà ce que donne notre fichier :

```html
{% macro input(type,name,id,label) %}
<div class="form-group">
	{% if label %}
	<label for="{{ id }}">{{ label }}</label>
	{% endif %}
	<input class="form-control" type="{{ type | default('text') }}" name="{{ name }}" id="{{ id }}">
</div>
{% endmacro %}
```

Notre macro est prête à l'emploi, il faut maintenant l'importer dans le template `home`. Pour cela il existe le tag `import` qui fonctionne comme ceci :

```html
{% import "./macros/form.html.twig" as form %}
```

Attention au chemin il est toujours relatif au dossier template et la sortie via deux point `../` ne fonctionnera pas. 

Créons donc notre formulaire en bas de page, entre une balise `form` bien entendu. Ce formualire comportera un prénom, un nom ainsi qu'un email :

```html
	<div class="row">
		<div class="col-8 mx-auto">
			<form>
				{{ form.input('text','firstname','firstname', 'Prénom') }}
				{{ form.input('text','lastname','lastname', 'Nom') }}
				{{ form.input('email','email','email', 'Adresse email') }}
			</form>
		</div>
	</div>
```

En seulement quelques lignes vous avez créez un formaire =).

## Conslusion

Nous avons fais le tour des grands principes du moteur ed templates Twig, je vous laisse découvrir le reste maintenant que vous avez une idée assez précise de son fonctionnement général. Ce que vous pouvez remarquer avant de quitter ce chapitre est de passer au suivant c'est que nos templates HTML sont particulièrement lisibles et c'est je crois l'un des plus gros avantage de Twig mise à part sa facilité d'utilisation.