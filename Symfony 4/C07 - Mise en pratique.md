# CHAPITRE VII : MISE EN PRATIQUE

Vous avez acquis de nombreuses connaissances de base pour l'utilisation du framework Symfony. Tout du moins pour commencer à réaliser des pages web dynamiques comprenant des intéractions avec un base de données. Dans ce chapitre je vous propose de créer la page de présentation des biens avec également une présentation à l'unité. Nous approfondirons certaines notions mais dans l'ensemble cela sera surtout de la mise en pratique.

## Page de présentation des biens

Première étape nous allons afficher l'ensemble de nos biens sur la page Les biens, correspondant à la route `properties`.

### Le controller

Nous avons déjà défini notre route pour afficher les biens il nous faut maintenant les récupérer (*même si nous n'en avons qu'un pour le moment*). Pour cela il nous faut donc utiliser le `PropertyRepository` comme nous l'avons vu dans le précédent chapitre. Nous utiliserons la méthode `findAll()` pour récupérer l'ensemble de nos biens. Il nous faudra ensuite passer la valeur retourner sous forme d'un tableau à notre template twig. Voici ce que nous pourrions faire

```php
	private $_repository;

	public function __construct(PropertyRepository $repository) {
		$this->_repository = $repository;
	}

	public function index(): Response {
		$properties = $this->_repository->findAll();
		return $this->render('pages/properties.html.twig', [
			'current_page' => 'properties',
			'properties' => $properties
		]);
	}
```

Nous avons maintenant un tableau properties accessible dans notre template et qui contient l'ensemble de nos biens. Avant de continuer je dois vous préciser une chose, dans le template Twig vous pouvez faire appel aux méthodes `get` supprimant ce préfixe. Ce qui donne `createdAt` pour la méthode `getCreatedAt` et non `created_at` qui est une propriété privée, attention à ne pas vous tromper !

### Le template

Maintenant que nous avons ces informations et que nous connaissons la syntaxe Twig vous avez déjà surement en tête que nous allons réaliser une boucle, pour la mise en forme je vous propose d'utiliser le component `card` de Bootstrap. Pour ce qui est de l'image nous utiliserons un `placeholder` du site [https://place-hold.it/](https://place-hold.it/).

```html
{% extends 'layouts/std-layout.html.twig' %}
{% block content %}
	{% for property in properties %}
		{% if loop.index0 % 3 == 0 or loop.index0 == 0 %}
			<div class="row">
		{% endif %}
			<div class="col-4">
				<div class="card" style="width: 18rem">
					<img src="https://place-hold.it/400x300" alt="Image représentant le bien" class="card-img-top">
					<div class="card-body">
						<h5 class="card-title">{{ property.title }}</h5>
						<p class="card-text">{{ property.description }}</p>
						<p class="text-primary" style="font-size: 1.5rem">{{ property.price | number_format(0,'.','.') ~ ' €'}}</p>
						<a href="">En savoir plus</a>
					</div>
					<div class="card-footer">
						<small class="text-muted">Publié le : {{ property.createdAt | date('d-m-Y \\à H\\hi') }}</small>
					</div>
				</div>
			</div>
		{% if loop.index % 3 == 0 %}
		</div>
		{% endif %}
	{% endfor %}
{% endblock %}
```

Niveau mise en forme nous somme plutot pas mal, si vous avez fait différemment bien sûr gardez ce que vous préférez du moment que l'ensemble est cohérent. Pour information la concaténation se fait avec l'opérateur `~` dans Twig, cela va vous servire pour le prochain templates ;).

## La page de présentation à l'unité

Pour créer cette page il nous faut de nouvelles informations dont nous n'avons pas encore parlé. En effet comment pouvons nous passer les informations sur notre bien via une nouvelle route ? Et bien ne vous inquiétez pas Symfony à prévu le coup ! Pour cela nous allons commencer par créer une route qui ressemblera au final à ce genre d'URL :

`monsite.fr/properties/nom-du-bien-id`

Nous avons donc deux éléments à passer en paramètre de notre nouvelle route :

* `Le slug` : Le slug est le nom en minuscule, sans accents ou specialchars et dont les espaces correspondent à des tirets.
* `L'id` : L'id est simplement l'id correspondant à notre bien.

Avant de voir comment nous allons implémenter notre route, débrouillons nous pour créer un slug de notre propriété `title` pour cela nous allons ajouter une propiété en bas de notre class `Property` que nous appellerons `getSlug()` et qui retournera donc une string. Au lieux de nous prendre la tête avec la création d'une regexp je vous propose tout simplement de d'inclure une petite dépendance dans notre code avec composer :

`composer require cocur/slugify`

Il nous suffira d'utiliser la méthode `slugify` d'une instance de la classe `Slugify` qui se trouve maintenant dans le namespace `Cocur\Slugify\Slugify`. Si quelqu'un vient me dire qu'il ne comprend pas quelle est l'utilité de cette dépendance je le tue. Voici donc notre nouvelle méthode :

```php
    public function getSlug(): string {
        $slugify = new Slugify();
        $slug = $slugify->slugify($this->title);
        return $slug;
    }
```

Nous avons maintenant accés facilement à nos éléments necessaire à la création de notre nouvelle route.

### Création de la route

Dans le fichier `yaml` correspondant à nos route nous allons créer une nouvelle route qui s'appellera `property` qui est donc bien au singulier en anglais. Cette route ne pointera pas vers un nouveau controleur comme nous aurions pu le faire mais vers le controleur `PropertiesController` cependant la méthode appellée sera `show()`. Cette façon de procéder est une convention.

* Liste des objets -> `index()`
* Objet à l'unité -> `show()`

Voici ce que pourrait donner notre route avec vos connaissances actuelles :

```yaml
property:
  path: /properties/
  controller: App\Controller\PropertiesController::show
```

Cependant il nous manque des informations. En effet où sont précisé les pramètre `slug` et `id` ? Pour l'instant nul part. Les pramètre des routes ce précise entre accolades `{paramètre}` dans la propriété `path` et nous voulons le format vu plus haut, voici ce qu'il nous faudra donc indiquer :

```yaml
property:
  path: /properties/{slug}-{id}
  controller: App\Controller\PropertiesController::show
```
Nickel mais... On a un problème ! Comment Symfony va-t-il pouvoir comprendre qu'il s'agît de l'id et non du slug sachant que nous avons mit un tirêt... Et bien nous avons encore une solution ! Nous allons utiliser la propriété `requirements` qui va nous permettre de préciser ce à quoi doivent ressembler nos deux valeur. Ce seront donc deux expression régulière que voici :

```yaml
property:
  path: /properties/{slug}-{id}
  requirements:
    slug: '[a-zA-Z\-]+'
    id: '\d+'
  controller: App\Controller\PropertiesController::show
```

Ouff notre route est enfin définie ! Maintenant place au show ! Enfin à la méthode `show()` ! Moins sexy tout de  suite hein =P ! Rendonc nous donc dans notre controleur `PropertiesController`. Cette méthode devra prendre en paramètre les deux valeur que nous venons de définir plus haut, ce qui permet lorsque vous saisissez l'adresse correspondant sans passer par un bouton appelant la méthode et lui passant les paramètres necessaires que celle-ci s'affiche tout de même. Ces deux paramètres doivent obligatoirement prendre le nom des propriétés définie dans le fichier `yaml` soit `$id` et `$slug`.

Cette méthode fera donc une requête au choix (ici `findOneBy()` que je trouve approprié) pour récupérer notre bien depuis la base de données. Puis l'enverra tout simplement à notre template :

```php
	public function show(string $slug,int $id): Response {
		$property = $this->_repository->findOneBy(['id' => $id]);
		return $this->render('pages/property.html.twig', [
			'current_page' => 'properties',
			'property' => $property
		]);
```

Il ne nous reste qu'a créer notre template ! Mains avant ajoutons un bouton pour afficher nos biens sur chacune de nos card Bootstrap. Dans le template des biens listés :

```html
<a href="{{ path('property', {'slug':property.getSlug,'id':property.id}) }}" class="btn btn-primary float-right">En savoir plus</a>
```

Attention à bien implémenter le tirêt et le slash !


### Création du template

Ce template devra contenir une présentation complète du bien, utilisez Bootstrap pour ne pas perdre votre temps avec du CSS ce n'est pas l'objectif ici que de faire une présentation magnifique !

Ici je vous laisse réellement vous excercer avec Twig car vous avez absolument toutes les informations necessaires mais néanmoins je vous propose ci-dessous une solution toute faite afin que vous puissiez choisir entre les deux présentation et également pour pouvoir solutionner un éventuel problème :

```html
{% extends 'layouts/std-layout.html.twig' %}
{% block content %}
<div class="card mb-3 border-0">
  <div class="row no-gutters">
    <div class="col-md-4">
      <img src="https://place-hold.it/300x300" class="card-img mt-4" alt="...">
    </div>
    <div class="col-md-8">
      <div class="card-body">
        <h2>{{ property.title }}</h3>
				<hr>
        <p class="mb-1"><small class="text-muted">Description du bien : </small></p>
        <p class="card-text mb-1">{{ property.description }}</p>
        <p class="mb-1"><small class="text-muted">Prix du bien : </small></p>
        <p class="card-text mb-1 text-primary" style="font-size: 1.5em">{{ property.price | number_format(0,'.','.') ~ ' €' }}</p>
        <p class="mb-1"><small class="text-muted">Date de mise en vente : </small></p>
        <p class="card-text">{{ property.createdAt | date('d-m-Y \\à H\\hi') }}</p>
        <p class="p-2 px-3 border bg-light text-muted"><strong>Informations complémentaires : </strong></p>
				<table class="table border">
				  <tbody>
				    <tr>
				      <td style="width: 250px" class="bg-light border-right text-muted">Surface</td>
				      <td>{{ property.surface ~ 'm²'}}</td>
				    </tr>
				    <tr>
				      <td style="width: 250px" class="bg-light border-right text-muted">Nombre de pièce</td>
				      <td>{{ property.rooms }}</td>
				    </tr>
				    <tr>
				      <td style="width: 250px" class="bg-light border-right text-muted">Nombre de chambre</td>
				      <td>{{ property.bedrooms }}</td>
				    </tr>
				    <tr>
				      <td style="width: 250px" class="bg-light border-right text-muted">Étage</td>
							{% if property.floor != 0 %}
				      	<td>{{ property.floor }}</td>
							{% else %}
				      	<td>Sans étage</td>								
							{% endif %}
				    </tr>
				    <tr>
				      <td style="width: 250px" class="bg-light border-right text-muted">Type de chauffage</td>
				      <td>{{ property.getHeat(true) }}</td>
				    </tr>
				    <tr>
				      <td style="width: 250px" class="bg-light border-right text-muted text-capitalize">Ville</td>
				      <td>{{ property.city ~ ' ' ~ property.postalCode}}</td>
				    </tr>
				  </tbody>
				</table>
      </div>
    </div>
  </div>
</div>
{% endblock %}
```
Pour notre chauffage `heat` je vous propose de modifier directement la méthode `getHeat()`. En effet nous allons créer une méthode qui renvoie par défaut le chauffage sous forme de string. Si elle contient `false` en paramètre alors elle retournera le numéro correspondant :

```php
  	public function getHeat(bool $toString = false)
  	{
  	    $heatType = [
  	        'Électrique',
  	        'Gaz'
  	    ];
  	    if ($toString) {
  	        return $heatType[$this->heat];
  	    } else {
  	        return $this->heat;
  	    }
  	}
```

Pourquoi ne pas renvoyer directement la conversion par défaut ? Et bien si nous changeons le comportement par défaut de nos méthodes cela nous générera des erreurs par la suite. En effet Symfony vérifi entre autre le typage des valeur de retour lors de nombreuse actions, comme par exemple la création de formulaire que nous verrons dans le chapitre suivant.

Nous commençons à bien avancer sur notre projet et je vous recommande à ce stade d'initialiser votre projet avec git. Un git ignore est déjà présent donc cela devrait ce faire relativement rapidement. Dans le prochain chapitre nous allons voir comment utiliser les formulaires et commencer à créer différents biens.