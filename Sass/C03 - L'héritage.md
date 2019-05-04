# CHAPITRE III : L'HÉRITAGE

Vous savez utiliser l'imbrication mais comme nous l'avons vu celle-ci à ces limite en terme de lisibilité. Nous allons voir comment utiliser l'héritage de SASS pour généré des règles CSS sans avoir à nous répéter et defaçon très propre.

## Définition d'une classe standard

Avant d'utiliser le principe d'héritage sur un élément CSS il nous faut en définir la classe qui le permettra. Je vous propose d'utiliser ce principe sur les boutons. Imaginons la classe suivante

```scss
.btn {
	display: inline-block;
	border: solid 1px #5cadd4;
	border-radius: 3px;
	background-color: #5cadd4;
	color: #fff;
	padding: 10px;
	min-width: 120px;
	text-align: center;
	&:hover {
		background-color: #fff;
		color: #5cadd4;
		transition-duration: 200ms;
	}
}
```
Notre class `.btn` fonctionne belle et bien nous allons pouvoir utiliser l'héritage.

## Uiliser l'héritage

Imaginons que le bouton submit de notre formulaire doit être différent des autres sous certains points seulement comme la largeur. Nous pourrions redéfinir une classe compléte, mais cela serait une véritable perte de temps. De plus si nous devions redéfinir l'ensemble de nos boutons cela deviendrait très long. Alors pourquoi ne pas tous simplement utiliser l'héritage ?

Voici comment nous pourrions faire pour notre bouton :

```scss
.btn-submit {
	@extend .btn;
	padding: 10px 20px;
}
```

Notre bouton reprend donc l'ensemble des propriétés de la classe `.btn` et écrase les valeurs modifiées. Là vous vous dites que SASS va recréer encore une classe supplémentaire et ajouter du poids au fichier ? Et bien vous auriez raison si tel était le cas ! Cependant SASS comprend le principe d'héritage, il appliquera une série de règles identique au deux classe ce qui n'ajoute pas de code inutile ! Voici ce qui se passe dans notre fichier CSS généré :

```scss
.btn, .btn-submit {
  display: inline-block;
  border: solid 1px #5cadd4;
  border-radius: 3px;
  background-color: #5cadd4;
  color: #fff;
  padding: 10px;
  min-width: 120px;
  text-align: center;
}
.btn:hover, .btn-submit:hover {
  background-color: #fff;
  color: #5cadd4;
  transition-duration: 200ms;
}

.btn-submit {
  padding: 10px 20px;
}
```

On peut voir que la classe `.btn-submit` à tout simplement était ajouter aux règles de la classe `.btn` et de même pour le `:hover`.