# CHAPITRE VIII : LES FORMULAIRES

Dans ce chapitre nous allons voir plusieurs façon d'utiliser les formulaires avec Symfony. Pour cela nous allons créer une interface d'administration très simple qui contiendra un formulaire permettant d'éditer un bien déjà enregistré.

Dans un premier temps nous allons créer deux routes avec leur templates associés. La première servira à afficher une liste des biens enregistrés, la seconde contiendra un formulaire permettant d'éditer et d'enregistrer nos modifications.

## Création des pages

Avant d'attaquer nos formulaires créons nos templates (partiellement), routes et controleurs.

### Les controleurs

Commençons les controleurs, ou plutôt le controleur qui servira pour nos deux routes. Je vous propose de créer un répertoire `Admin` dans le répertoire `Controller` afin de bien séparer nos différentes interfaces. Puis dans ce nouveau répertoire créons notre controleur `AdminPropertyController`. Ce controleur devra comporter deux méthode en sont sein :

* `index` : Qui servira à afficher notre liste et retournera une vue du template `index.html.twig` ainsi qu'un tableau de l'ensemble de nos biens.
* `edit` : Qui servira à afficher notre formulaire et renverra une vue du template `edit.html.twig` ainsi que le bien correspondant. Elle prendra donc en pramamètre un `id`.

Je vous laisse le soin d'implémenter ces deux méthodes seul. Si vous n'y arrivez pas voici la solution ci-dessous.

```php
<?php
namespace App\Controller\Admin;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use App\Repository\PropertyRepository;

class AdminPropertyController extends AbstractController {

	private $_repository;

	public function __construct(PropertyRepository $repository) {
		$this->_repository =  $repository;
	}

	public function index(): Response {
		$properties = $this->_repository->findAll();
		return $this->render('admin/index.html.twig',[
			'properties' => $properties
		]);
	}

	public function edit(int $id): Response {
		$property = $this->_repository->findOneBy(['id' => $id]);
		return $this->render('admin/edit.html.twig', [
			'property' => $property
		]);
	}

}
?>
```

Cela ressemble fortement à ce quenous avons fait jusqu'à présent.

### Les routes

Il nous faut maintenant définir deux routes pour chacun de nos rendu :

* `admin` : Qui doit ressembler à `monsite.fr/admin/index` et faire référence à la méthode `index()` de notre controleur. 
* `admin-edit` : Qui doit ressembler à `monsite.fr/admin/edit/22` dans le cas ou l'id du bien est 22.

Même exercice que précédemment je vous imaginez comment il serait possible de créer une route en ce sens. Sinon vous avez la solution pour ces deux routes ci-dessous :

```yaml
admin:
  path: /admin/index
  controller: App\Controller\Admin\AdminPropertyController::index

admin-edit:
  path: /admin/edit/{id}
  requirements:
    id: '\d+'
  controller: App\Controller\Admin\AdminPropertyController::edit
```

### Les templates

Comme d'habitude nous allons utiliser Bootstrap pour designer notre liste de bien dans une table HTML pour la route `admin/index` pour la seconde route le template sera vide pour le moment mais sera créer et étendra notre layout habituel. Les templates seront créer dans un dossier `admin` et non à la racine du dossier `templates` (*Comme vous avez pu le voir dans le render des controleurs ci-dessus*).

Encore une fois tentez de réaliser l'opération sinon vous pouvez vous servire des solutions ci-dessous :

index.html.twig

```html
{% extends 'layouts/std-layout.html.twig' %}
{% block content %}
<table class="table table-bordered">
  <thead class="thead-light">
    <tr>
      <th scope="col">Identifiant</th>
      <th scope="col">Titre</th>
      <th scope="col">Date</th>
      <th scope="col">Prix</th>
      <th scope="col">Édition</th>
    </tr>
  </thead>
  <tbody>
{% for property in properties %}
    <tr>
      <td>{{ property.id }}</td>
      <td>{{ property.title }}</td>
      <td>{{ property.createdAt | date('d-m-Y') }}</td>
      <td>{{ property.price | number_format(0,'.',' ') ~ ' €' }}</td>
      <td><a href="{{ path('admin-edit',{'id':property.id}) }}" class="btn btn-dark">Éditer</a></td>
    </tr>
{% endfor %}
	</tbody>
</table>

{% endblock %}
```

edit.html.twig

```html
{% extends 'layouts/std-layout.html.twig' %}
{% block content %}

{% endblock %}
```

Et c'est maintenant que les choses vont pouvoir se corcer ! =)

## La gestion des formulaires

Symfony dispose d'un système de formulaire prêt à l'emploi et orienté objet comme le reste du framework. En effet le framework permet de gérer des entité facilement et même de générer directement des formulaires HTML bien que ça ne soit pas la méthode la plus efficace au monde car elle limite un peu nos possibilité pour ce qui est du design mais également des champs que l'on souhaite afficher. Nous verrons néanmoins les différentes méthodes existantes.


### Générer un formulaire

Pour générer un formulaire côté backend de notre application nous pouvons utiliser directement la console proposée avec Symfony. Et comment nous l'avions vu pour les entités nous pouvons utiliser la commande `make` cette fois ci nous lui demanderons de fabriquer un formulaire et la syntaxe sera donc la suivant :

`php bin/console make:form`

Après cette commande, Symfony nous demande le nom que nous souhaitons attribuer à notre formulaire. Par convention on utilise le nom de l'entité sufixé de `Type` ce qui nous donnera `PropertyType`. Ensuite Symfony nous demande à quelle entité nous souhaitons ratacher le formulaire il s'agint donc de `Property`. Normalement Symfony doit vous proposer une autocomplétion, cela permet d'éviter les erreurs également.

Retournons dans notre IDE. Nous pouvons voir apparaitre un nouveau dossier `src/Forms` qui contient un fichier `PropertyType.php`. Je vous propose de regarder un peu ce que contient ce fichier afin de mieux comprendre le fonctionnement du framework.

### Insérer le formulaire

Pour insérer le formulaire dans notre template nous pouvons utiliser une méthode de `$this` s'appelant `createForm()`. Elle prend en paramètre :

* **Le formulaire** : La classe correspondante au formulaire ici `PropertyType`.
* **Le contenu** : Soit un tableau des champs soit un objet. Ici ce sera notre objet instance de `Property`.

Une fois le formulaire créer, nous pouvons utiliser la méthode `createView()` qui transformera notre formulaire en objet de type vue. Voici donc ce que nous pouvons modifier notre controleur `AdminPropertyController` pour qu'il renvoi le formulaire :

```php
	public function edit(int $id): Response {
		$property = $this->_repository->findOneBy(['id' => $id]);
		$form = $this->createForm(PropertyType::class,$property);
		return $this->render('admin/edit.html.twig', [
			'property' => $property,
			'form' => $form->createView()
		]);
	}
```

### Afficher le formulaire dans le template

Nous pouvons maintenant afficher le formulaire dans notre template avec la méthode `form()` qui prend en pramètre notre formulaire. Dans le fichier `edit.html.twig` :

```html
{% extends 'layouts/std-layout.html.twig' %}
{% block content %}
	{{ form(form) }}
{% endblock %}
```

Dirigez vous maintenant vers la liste des biens de l'interface d'administration à l'adresse suivante `.../admin/index` puis cliquez sur le bouton d'édition. Votre formulaire apparait ! Super mais il nous manque deux chose le bouton pour soumetre notre formulaire et clairement du design !!! Cependant on constate que les champs sont bien ceux que nous pourions attendre, par exemple un booléen est représenté par une cas à cocher ! C'est pas mal du tout =)

Pour ajouter un bouton d'envoi il nous suffit d'ajouter un bouton en bas de notre formulaire. Mais comment Twig va savoir s'il s'agit bien du bouton d'envoi de notre formulaire ? Et bien pour cela nous allons devoir encapsuler notre formulaire entre deux fonction `form_start()` et `form_end()`, elles prennent en pâramètre le formulaire en question et inutile de préciser laquelle vient en premier ^^'.

```html
{% extends 'layouts/std-layout.html.twig' %}
{% block content %}
	{{ form_start(form) }}
		{{ form(form) }}
		<button type="submit" class="btn btn-primary">Enregistrer</button>
	{{ form_end(form) }}
{% endblock %}
```

C'est bien mais franchement cela mérite un peu de design ! Pour cela Symfony fourni directement des modèles de design CSS que l'ont peu préciser de deux manière :

* Dans le template Twig pour que cela s'applique à un seul formulaire.
* Dans le fichier de configuration de Twig soit `config/package/twig.yaml`.

#### La méthode config yaml

Dans le fichier que nous venons de citer ajouter les lignes suivante pour appliquer le thème Bootstrap à tous nos formualaires :

```yaml
twig:
    default_path: '%kernel.project_dir%/templates'
    debug: '%kernel.debug%'
    strict_variables: '%kernel.debug%'
    form_themes: ['bootstrap_4_horizontal_layout.html.twig']
```

C'est bien la dernière ligne qui nous intéresse ! Maintenant rechargé votre formulaire vous pourrez constater qu'il est déjà bien plus joli !

#### La méthode avec Twig

Dans notre template nous allons préciser le thème à utiliser avec un block `form_theme` sans le `s` malgré que comme dans le fichier `yaml` nous puissions en ajouter plusieurs. Pour être honnête je n'ai pas compris ce choix de la par des développeurs. Quoi qu'il en soit voici ce que nous pouvons ajouter en haut de notre template. (*N'oubliez pas de supprimer la ligne ajoutée dans le fichier yaml*) :

```html
{% form_theme form with ['bootstrap_4_horizontal_layout.html.twig'] %}
```
On peut constater que cela fonctionne exactement de la même façon.

#### Ajouter son propre theme

Vous pouvez également ajouter vos propres thèmes pour cela je vous met un rapide lien vers la documentation correspondante aux thème, vous y retrouverez également lles thème qu'il est possible d'utiliser :

[Symfony DOC FORM Thèmes](https://symfony.com/doc/current/form/form_themes.html)

#### Créer son propre design

Là méthode la plus commune quand il s'agit dans grand formulaire comme le notre est de le personnaliser directement avec du HTML.

Pour cela il existe 3 fonction de Twig :

* `form_row()` : Qui renvoie le label et le champs correspondant au paramètre renseigné.
* `form_widget()` : Qui renvoie uniquement le champs correspondant au paramètre renseigné.
* `form_label()` : Qui renvoie uniquement le label correspondant au paramètre renseigné.

Attention néanmoins, ces fonctions utilises le theme indiqué seulement il est possible comme avec Bootstrap, d'ajouter des élément HTML comme des `div` qui vont nous permettre de mieux disposer notre formulaire. Nous utiliserons le theme Bootstrap sans l'horizontalité des champs cette fois ci :

Voici par exemple ce que nous pourrions faire :

```html
{% extends 'layouts/std-layout.html.twig' %}
{% form_theme form with ['bootstrap_4_layout.html.twig'] %}
{% block content %}
	{{ form_start(form) }}
		<div class="row">
			<div class="col-8">{{ form_row(form.title) }}</div>
			<div class="col-4">{{ form_row(form.price) }}</div>
		</div>
		<div class="row">
			<div class="col-12">{{ form_row(form.description) }}</div>
		</div>
		<div class="row">
			<div class="col-4">{{ form_row(form.surface) }}</div>
			<div class="col-4">{{ form_row(form.rooms) }}</div>
			<div class="col-4">{{ form_row(form.bedrooms) }}</div>
		</div>
		<div class="row">
			<div class="col-4">{{ form_row(form.floor) }}</div>
			<div class="col-4">{{ form_row(form.heat) }}</div>
			<div class="col-4">{{ form_row(form.sold) }}</div>
		</div>
		<div class="row">
			<div class="col-2">{{ form_row(form.postal_code) }}</div>
			<div class="col-2">{{ form_row(form.city) }}</div>
			<div class="col-8">{{ form_row(form.address) }}</div>
		</div>
		<button type="submit" class="btn btn-primary float-right">Enregistrer</button>
	{{ form_end(form) }}
{% endblock %}
```

Sachez également que `form_row()` peut prendre différents paramètres optionnels par exemple pourdéfinir le label précisemment, ajouter des attributs etc.

Ici j'ai volontairement choisi de ne pas afficher la valeur du champ `created_at` cependant on peut constater que Symfony l'ajoute automatiquement. Pour désactiver ce comportement nous allons devoir modifier le formulaire qui à été automatiquement généré dans la classe `PropertyType`.

### Modifier le comportement du formulaire

Nous avons plusieurs problèmatique à résoudre avec notre formulaire :

* Le champ created_at que nous ne souhaitons pas afficher.
* Avoir des labels en français.
* Restreindre les choix pour le type de chauffage et afficher correctement leur type.

Dans cette partie nous allons résoudre l'ensemble de ces problèmatique.

#### Supprimer un champ

Pour supprimer le champs de trop il nous faut simplement aller dans notre fichier `PropertyType.php` et supprimer le champ correspondant. Les champs sont ajouter avec la méthode `add()`. Supprimons donc la ligne suivante :

```php
add('created_at')
```

#### Insérer une traduction

Symfony nous facilite grandement la vie en matière de traduction et cela même pour de simple élément comme les formulaire. Nous allons donc en profiter pour découvrir comment utiliser ce système sur nos formulaires.

Dans un premier temps et toujours dans le même fichier nous pouvons voir une méthode `ConfigureOptions()` utilisant une instance de `OptionsResolver` cette classe dispose d'une méthode `setDefaults()` qui retourne un tableau associatif d'options. Nous allons ajouter l'option `translation_domain` qui sera relié au domain `forms`.

```php
    public function configureOptions(OptionsResolver $resolver)
    {
        $resolver->setDefaults([
            'data_class' => Property::class,
            'translation_domain' => 'forms'
        ]);
    }
}
```

Ensuite dans le dossier translations de Symfony, il nous faudra créer un fichier `forms.fr.yaml`. A l'intérieur il nous suffira d'entrer chacun des mots souhaitant être traduit comme ceci `Example: Exemple`.

```yaml
Title: Titre
Description: Description
Rooms: Pièces
Bedrooms: Chambres
Floor: Étage
Price: Prix
Heat: Chauffage
City: Ville
Address: Adresse
Postal code: Code postal
Sold: Vendu
Created at: Date de création
```

Pour que ceci fonctionne dans notre environnement local il nous faudra préciser que nous sommes en mode `fr` dans le fichier `config/services.yaml` et dans la variable `locale`.

```yaml
parameters:
    locale: 'fr'
```

Vous pouvez maintenant recharger votre page et voir le résultat ! C'est vraiment pas mal ce système de traduction non ?

#### Changer la forme d'un champ

Il est également possible de changer le type d'un champs dans notre builder de formulaire (*la première fonction de la classe PropertyType*). En effet, la méthode `add()` peut prendre deux paramètre optionnel, le premier sera l'opération à effectué et donc la méthode correspondante ici `ChoiceType()::class` le second sera dans ce cas le type de champ. Ici un champ `select` soit `choices` pour Symfony puis un tableau associatif contenant le choix et sont équivalent dans notre base de données. Un exemple sera plus parlant :

Voici donc comment  modifier notre méthode `add()` pour obtenir le résultat escompté :

```php
->add('heat',ChoiceType::class,[
    'choices' => ['Électricité' => 0,'Gaz' => 1]
])
```
Pour en savoir plus sur les types de champs rendez-vous sur la page ci-dessous :
[Type de champs](https://symfony.com/doc/current/reference/forms/types/choice.html).


## Envoi des formulaires

Symfony à évidemment un système d'envoi de formulaire, pour l'instant quand nous cliquons sur le bouton enregistrer on peut constater que notre formulaire s'envoie avec un requête post contenant l'ensemble des informations que nous avons saisie. (*Pour voir cela utilisez le profiler*). Mais ce que nous souhaitons c'est mettre à jour notre bien dans la base de données et pour cela nous allons voir comment traiter ces informations.


### Récupérer les données

Dans un premier temps il faut que notre controleur récupère les données de la variable `$_POST` mais nous n'avons pas à nous soucier de cela car Symfony contient une classe nous permettant de traiter ces informations. Il s'agit de la classe `Request` du namespace `Symfony\Component\HttpFoundation`. Nous allons donc injecter ceci dans notre méthode `edit` car notre formulaire pointe sur cette même page :

```php
	public function edit(int $id, Request $request): Response {

		$property = $this->_repository->findOneBy(['id' => $id]);
		$form = $this->createForm(PropertyType::class,$property);
		
		return $this->render('admin/edit.html.twig', [
			'property' => $property,
			'form' => $form->createView()
		]);
	}
```

Ensuite il nous faut controler les valeurs envoyés et pour cela notre variable `$form` contient la méthode `handleRequest()`. Cette méthode est hérité de l'interface `FormInterface` et fait deux chose, elle vérifie que le formulaire est dans un état envoyé, si c'est le cas, alors elle modifie l'instance de l'objet du formulaire, donc ici une instance de `Property`.

Maintenant nous devrions vérifier que le formulaire est valide et qu'il à bien était envoyé, pour cela notre objet `$form` et ses méthodes `isSubmited()` et `isValid()` qui renvoent toutes deux un résultat booléen.

Enfin nous pouvons utiliser la méthode `flush()` de la classe `ObjectManager()` en important bien sûr le namespace correspondant. Dernière chose, on redirigera l'utilisateur sur la liste des biens de l'interface d'administration avec la méthode `redirectToRoute()`.

Voici maintenant notre méthode que vous pouvez essayer directement avec votre formulaire :

```php
	public function edit(int $id, Request $request): Response {

		$property = $this->_repository->findOneBy(['id' => $id]);
		$form = $this->createForm(PropertyType::class,$property);

		$form->handleRequest($request);

		if ($form->isSubmitted() && $form->isValid()) {
			$this->_entityManager->flush();
			return $this->redirectToRoute('/admin/index');
		}
		
		return $this->render('admin/edit.html.twig', [
			'property' => $property,
			'form' => $form->createView()
		]);
	}
```

## Interface CRUD

Nous avons déjà fait 50% du travail pour implémenter une interface CRUD. En effet nous avons déjà Read et Update que sont l'affichage des données et leur modifications. Dans cette partie nous allons implémenter les deux item restant de l'interface c'est a dire Create et Delete.

### Exercie : Ajout de biens

Pour ajouter des biens nous allons utiliser le même principe que pour l'édition de biens à quelques petites différences prêt. Je vous laisse donc tenter de réaliser l'opération avec ce que vous avez appris jusqu'à présent. Si vous n'y arrivez pas la correction se trouve ci dessous.

#### Correction

Dans un premier temps il nous faut créer une fonction au sein de notre controleur `AdminPropertyController` que nous appelerons `new()`. Cette fonction ressemble trait pour trait à la fonction d'édition à la différence que nous devons créer un nouvel objet `Property` et non le récupérer dans la base de données. La seconde différence ce situe avant le `flush()` à effectuer sur notre base de données qui doit être précédé de `persist()` car l'item n'y existe pas encore.

```php
	public function new(Request $request): Response {

		$property = new Property();
		$form = $this->createForm(PropertyType::class,$property);
		$form->handleRequest($request);

		if ($form->isSubmitted() && $form->isValid()) {

			$this->_entityManager->persist($property);
			$this->_entityManager->flush();
			return $this->redirectToRoute('admin');

		} else {
			
			return $this->render('admin/new.html.twig',[
				'property' => $property,
				'form' => $form->createView()
			]);
		}
	}
```

Ensuite nous allons créer la route dans notre fichier `routes.yaml` :

```yaml
admin-new:
  path: /admin/new
  controller: App\Controller\Admin\AdminPropertyController::new
```

Le template de création et d'édition aurons le même code. Vous pouvez au choix faire pointer votre controller sur un nouveau template héritant de précédent ou simplement faire pointer votre controleur sur le même template.

Cependant si vous testez le code comme ceci vousvous retrouverez avec une erreur pour la bonne raison que la date n'est pas renseignée naturellement. Alors je vous propose d'initialiser sa valeur dans le constructeur de classe :

```php
    public function __construct()
    {
        $this->created_at = new \DateTime();
    }
```

Voilà votre nouvelle route et foncitonnalité est prêt à l'emploi.

### Supprimer une instance d'entité

Pour supprimer une entité il n'y a rien de compliquer, il nous suffit d'utiliser la méthode `remove()` de la classe `ObjectManager`qui prend en praramètre l'entité à supprimer. Puis de mettre à jour notre base de données avec `flush()`. Evidemment pour que cela fonctionne il nous faut une route associée.

Dans notre controleur :

```php
	public function delete(int $id): Response {
		$property = $this->_repository->findOneBy(['id' => $id]);
		$this->_entityManager->remove($property);
		$this->_entityManager->flush();
		return $this->redirectToRoute('admin');
	}
```

Dans le fichier des routes :

```yaml
admin-delete:
  path: /admin/delete-{id}
  requirements:
    id: '\d+'
  controller: App\Controller\Admin\AdminPropertyController::delete
```

Enfin je vous propose d'ajouter deux boutons dans notre template `index.html.twig` pour supprimer et ajouter de nouveaux biens :

```html
{% extends 'layouts/std-layout.html.twig' %}
{% block content %}
<table class="table table-bordered">
  <thead class="thead-light">
    <tr>
      <th scope="col">Identifiant</th>
      <th scope="col">Titre</th>
      <th scope="col">Date</th>
      <th scope="col">Prix</th>
      <th scope="col">Édition</th>
      <th scope="col">Suppression</th>
    </tr>
  </thead>
  <tbody>
{% for property in properties %}
    <tr>
      <td>{{ property.id }}</td>
      <td>{{ property.title }}</td>
      <td>{{ property.createdAt | date('d-m-Y') }}</td>
      <td>{{ property.price | number_format(0,'.',' ') ~ ' €' }}</td>
      <td><a href="{{ path('admin-edit',{'id':property.id}) }}" class="btn btn-dark">Éditer</a></td>
      <td><a href="{{ path('admin-delete',{'id':property.id}) }}" class="btn btn-danger">Supprimer</a></td>
    </tr>
{% endfor %}
	</tbody>
</table>
<div class="row">
  <div class="col">
    <a class="btn btn-primary float-right" href="{{ path('admin-new') }}">Nouveau bien</a>
  </div>
</div>
{% endblock %}
```

Le tout fonctionne bien mais... Il y a toujours un mais effectivement. Imaginons qu'un de vos utilisateurs en tant qu'admin execute la requête suivante malencontreusement en tapant l'adresse du site dans son historique, que se passerait-il ?

`monsite.fr/admin/delete-5`

Et bien si l'id 5 est défini cela le supprimerait... Nous pouvons également imaginer ce genre d'attaque sur d'autre partie du site car celle-ci pourrait-être autorisé qu'a certain utilisateurs mais nous verrons ce détail plus tard.

## La faille CSRF

Ce type de faille à un nom, on les appelle les failles CSRF pour Cross-Site Request Forgery. En effet, l'idée est la suivante si un méchant hacker vous fait cliquer sur un lien d'une application supprimant vos données par exemple et dans le cas ou vous êtes déjà authentifié, alors l'action sera éxécuté. Il faut donc nous en protéger.

### Utiliser un token

Un token ou *jeton* en français est une clé permettant d'identifier une entité (utilisateur, formulaire etc) et donc de valider la conformité des informations transmises. Symfony inclu nativement un token dans chaque formulaire même s'il est possible de les personnaliser pour renforcer la sécurité de son application. 

[voir la doc](https://symfony.com/doc/current/security/csrf.html)

Mais pour l'instant nous allons utiliser ce que nous donne Symfony par défaut. Pour effectuer une vérification dans la méthode concernée (ici `delete()`) il nous faut utiliser la méthode `isCsrfTokenValide()` qui est directement incluse dans la variable `$this`. Elle prend en paramètre la valeur de la clé attribué au token ainsi que la valeur du token reçu. Alors comment récupérer cette valeur ?

Il existe une méthode sur l'objet `Request` qui s'appelle `get()` et s'utilise sur la propriété `request`. Un bout de code sera bien plus simple à comprendre :

```php
	public function delete(int $id, Request $request): Response {
		$token = $request->request->get('_token');

		if ($this->isCsrfTokenValid('delete-item',$token)) {
			$property = $this->_repository->findOneBy(['id' => $id]);
			$this->_entityManager->remove($property);
			$this->_entityManager->flush();
		}

		return $this->redirectToRoute('admin');
	}
```

Voilà notre controleur prêt à effectué une vérification cependant il nous manque à définir le moyen de transmettre cette clé mais également de crypter l'information. Pour cela Symfony dispose d'une méthode Twig `csrf_token()` qui prend en paramètre la clé que nous souhaitons crypter. Il ne nous reste donc plus qu'à fabriquer un petit formulaire dans notre template pour le bouton supprimer qui est maintenant à remplacer :

```html
<form action="{{ path('admin-delete',{'id':property.id}) }}" method="post">
  <input type="hidden" name="_token" value="{{ csrf_token('delete-item') }}">
  <button type="submit"  class="btn btn-danger">Supprimer</button>
</form>
```

Voilà qui est beaucoup mieux niveau sécurité !

## Informer l'utilisateur

Pour aller un peu plus on peut rechercher à informer l'utilisateur que l'action demandée à bien était réalisée. En effet c'est une question de de confort mais je trouve le moment opportaint pour vous présenter une petite fonctionnalité bien sympatique de Symfony.

Pour afficher ce genre de message on pourrait faire un template et un controleur qui avec avec du javascript renverrait après X secondes sur une autre page, ce template pourrait recevoir le message de confirmation ou non et afficher ce qu'on lui demande.

Cette méthodologie est intéressante, certes mais Symfony permet de faire un système à peu de chose prêt similaire mais sans redirection programmée. En effet il existe une méthode appelée `addFlash()` elle prend en paramètre le type de message à envoyer et le message lui même. Ces deux information peuvent être récupérée via un `template`, ici `index.html.twig` et afficher avec une condition adéquate. Faisons l'expérience avec la fonction `edit()` de notre controleur :

```php
	public function edit(int $id, Request $request): Response {

		// du code....

		if ($form->isSubmitted() && $form->isValid()) {
			$this->_entityManager->flush();
			$this->addFlash('success','Les modifications ont bien été enregistrées');
			return $this->redirectToRoute('admin');
		}
		
		// du code...

	}
```

Puis dans notre template :

```html
<!-- haut du template... du code... -->
{% for message in app.flashes('success') %}
  <div class="alert alert-success">
    {{ message }}
  </div>
{% endfor %}
<!-- du code... -->
```

Les messages se récupérent dans un tableau retourné par la méthode ci-dessus. Vous pouvez passez en paramètre le type de message auquel s'applique la boucle et ainsi en afficher différente forme pour chaque type. Sinon vous pouvez créer deux boucles et matcher l'ensemble des message car chaque type de messages sont contenu dans un tableau :

```html
{% for messages in app.flashes %}
  {% for message in messages %}
    <div class="alert alert-secondary">
      {{ message }}
    </div>    
  {% endfor %}
{% endfor %}
```
Cette syntaxe affiche donc l'ensemble des message quelqu'en soit le type. Sachez qu'il est possible d'aller plus loin avec ces messages flash pour cela rendez-vous dans la doc :

[Symfony Flash message](https://symfony.com/doc/current/controller.html#flash-messages)

## Conclusion

Dans ce chapitre nous avons appris à créer une interface CRUD sans écrire une seule ligne SQL ce qui est un véritable avantage comparativement à un travail sans framework. On comprend d'ailleurs l'utilité de ceux-ci car en complément avec Bootstrap nous n'avons même pas rédigé une seule ligne de CSS. Finalement ça à du bon tout ça ! Bien sûr pour un site pro Bootstrap on pourra passer niveau design (pas grid) en revanche Symfony fait extrêmement bien son travail et nous facilite grandement la vie une fois maîtrisé. 