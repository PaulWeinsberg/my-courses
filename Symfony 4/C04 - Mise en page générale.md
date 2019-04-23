# CHAPITRE IV : MISE EN PAGE GÉNÉRALE

Dans ce chapitre nous allons essentiellement mettre en page notre application pour que celle-ci soit prête aux différentes opérations que nous réaliserons par la suite.

## Le layout de base

Comme vous le savez déjà le moteur de templates Twig nous permet d'utiliser un système de layout. Part ailleur Symfony propose une base dans le dossier `templates` intelligemment nommée `base.html.twig`. Avez-vous remarqué la dispartition de la barre d'état du profiler de Symfony pour notre page `home` ? Cette disparition est dû au fait que Symfony l'inject dans des block Twig spécifiques. Notamment dans les blocks `stylesheets` et `javascripts`. Nous allons modifier cette base pour construire notre layout principal et y ajouter également Bootstrap.

Plutôt que de vous faire bêtement recopier du code qui vous en apprendra peut voici les fichier à générer :

* Un dossier `layouts` dans le dossier `templates`
* Un fichier `std-layout.html.twig` dans le dossier `layout`

Ensuite voici les différents codes à appliquer aux différents éléments :

#### base.html.twig

```html
<!DOCTYPE html>
<html lang="fr-FR">
    <head>
        <meta charset="UTF-8">
        <title>{% block title %}{% endblock %}</title>
        {% block head %}{% endblock %}
        {% block stylesheets %}{% endblock %}
    </head>
    <body>
        {% block body %}{% endblock %}
        {% block javascripts %}{% endblock %}
    </body>
</html>
```

#### std-layout.html.twig

```html
{% extends 'base.html.twig' %}
{% block title %}{{ 'Symfony Agency' }}{% endblock %}
{% block head %}{% endblock %}
{% block stylesheets %}
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
{% endblock %}
{% block body %}
	{% block navbar %}
	<nav class="navbar navbar-expand-lg navbar-dark bg-dark border-bottom">
		<div class="container">
			<a class="navbar-brand" href="#">Symfony Agency</a>
			<button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
				<span class="navbar-toggler-icon"></span>
			</button>
			<div class="collapse navbar-collapse" id="navbarNav">
				<ul class="navbar-nav">
					<li class="nav-item">
						<a class="nav-link" href="#">Accueil</a>
					</li>
					<li class="nav-item">
						<a class="nav-link" href="#">Les biens</a>
					</li>
				</ul>
			</div>
		</div>
	</nav>
	{% endblock %}
	<div class="container mt-4">
		{% block content %}{% endblock %}
	</div>
{% endblock %}
{% block javascripts %}
<script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
{% endblock %}
```

#### home.html.twig

```html
{% extends 'layouts/std-layout.html.twig' %}
{% block content %}
	<h1>Hello world !</h1>
{% endblock %}
```

Voilà nos différents élements créés. Nous disposons maintenant du nécessaire pour bien continuer en toute quiétude. Attention lors de la copie du code il est possible que les liens pour Bootstrap est changés dans ce cas faites un tour sur la doc et chargez les avec les URL qui correspondent.

Encore une petite chose à modifier qui vous donnera l'ocasion d'utiliser une des fonction twig spécifique à Symfony. En effet, la fonction `path()` permet entre autre d'indiquer le nom d'un controleur et d'en récupérer l'URL relative, c'est donc ce que nous allons indiquer pour notre lien `Accueil` et le titre de notre barre de navigation. Pour cela il suffit de passer en paramètre à la fonction `path()` le nom de la route correspondante, dans notre cas `'home'`. Voici les ligne à modifier en conséquence :

```html
<a class="navbar-brand" href="{{ path('home') }}">Symfony Agency</a>
```

```html
<a class="nav-link" href="{{ path('home') }}">Home</a>
```

## La pages de biens

Une agence immobilière à tous de même pour vacotion principale la mise en vente de différents biens. Nous allons donc créer une page qui affichera ces biens que nous implémenterons dans le chapitre suivant. Pour le moment créons justement cette fameuse page qui pour l'instant n'affichera rien excepté la barre de navigation.

Le nom du fichier de notre page sera `properties.html.twig` et le controleur reprendra ce terme qui est l'équivalent anglais de biens au pluriel. Commençons donc par ce controleur :

#### Le controleur

Le contrôleur sera pour le moment très similaire à celui de notre page home.

```php
<?php

namespace App\Controller;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Twig\Environment;

class PropertiesController extends AbstractController {

	public function index(): Response {
		return $this->render('pages/properties.html.twig');
	}

}
```

#### La route

Il nous faut maintenant définir la route afin que Symfony sache comment naviguer avec notre application comme nous l'avons vu dans le chapitre précédent.

```yaml
properties:
  path: /properties
  controller: App\Controller\PropertiesController::index
```

#### Le template

Créons maintenant un template simple qui nous permettra de vérifier le fonctionnement de l'ensemble.

```html
{% extends 'layouts/std-layout.html.twig' %}
{% block content %}
	<h1>Les biens</h1>
{% endblock %}
```

#### Mise à jour du layout

Enfin il nous faudra mettre à jour le layout pour que la barre de navigation prenne en compte le lien :

```html
<a class="nav-link" href="{{ path('properties') }}">Les biens</a>
```

Actualiser votre navigateur et vérifiez le fonctionnement de l'ensemble. Normalement c'est tous bon et nous pouvons passer à la suite de ce cours. Dans le cas contraire vérifier les erreurs éventuelles, de toute façon Symfony vous précise de façon clair d'ou elles peuvent provenir en général.

#### Rendre la navbar dynamique

Comme vous le savez la classe `active` de bootstrap permet de colorer le lien en cours d'affichage nous allons donc créer un petit système nous permettant d'obtenir ce résultat. Pour cela nous allons dire dans nos deux controleur de passer à nos templates le nom de leur route dans un tableau associatif dont la clé sera `current_page`.

Voici ce que doivent retourner nos controleurs je vous laisse faire le second :

```php
return $this->render('pages/home.html.twig', [
	'current_page' => 'home'
]));
```

Et voici la condition à implémenter mon notre layout et l'ensemble de ses liens :

```html
<a class="nav-link {% if current_page is defined and current_page == 'home' %}{{ 'active' }}{% endif %}" href="{{ path('home') }}">Home</a>
```

Maintenant vos liens se colorent en fonction de la page en cours d'affiche. C'est tout même beacoup plus sympa.
