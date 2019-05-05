# CHAPITRE V : IMPORTS ET MIXINS

Dans ce chapitre nous allons découvrir deux notions supplémentaires, les imports et les mixins. Ceux-ci n'ont rien en commun mais le volet sur les imports étant relativement court j'ai trouvé plus intéressant de la placer ici.

## Les imports

Les imports comme leur nom l'indique permettent d'importer des fichiers SCSS dans d'autres. Là vous vous dites comme pour les variables que cette fonctionnalité est déjà présente en CSS alors pourquoi réinventer la roue ?

En effet cette fonctionnalité est présente, son résultat est identique mais son fonctionnement différent. Les import en SASS permettent après compilation d'intégrer le code dans un seul et même fichier alors qu'avec CSS, les fichiers restent bien séparés ce qui ralentit le chargement de notre page web car le navigateur doit effectuer une requête HTTP supplémentaire pour obtenir le fichier demandé.

Comme en CSS la syntaxe des imports avec SASS est la suivante :

`@import 'nom-du-fichier'`

Attention néanmoins, le lien vers le fichier est absolue mais ne comporte pas l'extension de celui-ci même s'il est possible de l'indiquer. Il est aussi à savoir que les fichiers préfixé d'un underscore `_` dans leur nom son ignorés lors de la compilation, ce qui est pratique dans de nombreux cas.

Par exemple séparons nos variables dans un fichier `_variables.scss` puis importons dans notre fichier `style.scss` :

```scss
@import '_variables';
```

Nous pouvons voir dans le fichier CSS généré que l'import à bien eu lieu cependant aucun fichier `_variables.css` n'a été généré.

## Les mixins

Les mixins s'apparentent clairement à de petites fonctions sauf qu'elles retournent l'intégrallité de leur contenu. Elles peuvent prendre des paramètres ce qui peut être très pratique dans de nombreux cas. Le cas souvent utilisé pour l'exemple est le cas des préfixes spécifiques à certains navigateurs. Prenons la propriété `transform` dont de nombreux préfixes existent, nous pourrions vouloir utiliser une mixins permettant d'ajouter l'ensemble des préfixes directement sans avoir à préciser l'ensemble.

### Déclarer une mixin

Pour réaliser notre exemple il nous faut donc créer notre mixin avec la syntaxe suivante :

```scss
@mixin nom-de-la-mixin($parametre1,$parametre2) {
	propriété: $parametre1;
// du code...
}
```
Nous allons donc créer un nouveau fichier `_mixins.scss` que nous importerons dans le fichier `style.scss`. Enfin nous allons définir notre mixin, voici ce que cela pourrait donner :

```scss
@mixin transform($value) {
	transform: #{$value};
	-webkit-transform: #{$value};
	-ms-transform: #{$value};
}
```

### Appeler une mixin

Puis nous allons pouvoir utiliser directement cette mixin dans notre code avec l'instruction `@include`, par exemple pour retourner notre image nous servant de logo, bien que cela nous soit particulièrement peut utile :

```scss
// du code...
			img {
				margin-top: 20px;
				@include transform('rotate(90deg)');
				@media #{$media-screen-md} {
					& {
						max-width: 100px;
					}
				}
			}
// du code...
```

Voilà notre image qui applique la transformation voulue et quelque soit le préfixe utilisé. Notez qu'il est tout à fait possible de ne pas utilisé l'interpolation dans votre mixin, dans ce cas il ne faudra pas passer une chaine de caractère en paramètre mais des instructions classique dans appostrophes ou guillemets.

### Utiliser des paramètre optionnels

Il est tout à fait possible de définir dans paramètres optionnels et des valeurs par défaut. Pour cela il suffit d'utiliser `:` dans la déclaration de ceux-ci :

```scss
@mixin transform($value: 'rotate(30deg)') {
	transform: #{$value};
	-webkit-transform: #{$value};
	-ms-transform: #{$value};
}
```

Maintenant nous pouvons appeler notre mixin en ne précisant aucun paramètre :

```scss
				@include transform;
```

Evidemment si vous en précisez un, celui ecrasera la valeur par défaut.