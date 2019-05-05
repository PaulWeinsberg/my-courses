# CHAPITRE VI : LES FONCTIONS

Les fonctions marchent de la même façon que les mixins à l'éxéption qu'elle utilisent une valeur de retour ce qui permet donc d'effectuer certaines opérations et d'en retourner uniquement le résultat.

## Déclarer une fonction

Pour déclarer une fonction on utilise la syntaxe suivante :

```scss
@function ma-fonction($parametre1,$parametre2) {
	// du code...
	@return ma-valeur-de-retour;
}
```

Rien de plus facile n'est-ce pas ? Ces fonctions peuvent vous servire à de nombreuses choses, par exemple renvoyer le résultat en `rem` d'une taille en `px`. Pour illustrer cet exemple je vous propose de déclarer dans votre fichier `style.scss` et dans le selecteur `:root` la propriété `font-size` à `10px` laquelle sera définie par une variable `$rem-base` dans notre fichier `_variables.scss`.

Nous créerons notre fonctions dans un fichier `_function.scss` qu'il faudra donc importer dans notre fichier `style.scss`. Pour que ce soit plus clair voici donc notre fichier `style.scss` :

```scss
@import '_variables';
@import '_mixins';
@import '_functions';
:root {
	font-size: $rem-base;
}
```

Dans notre fichier `_variable.scss` :


```scss
// du code...
$rem-base: 10px;
```

Nous avonc donc tous les incrédients pour créer notre function. Je rappelle le fonctionnement des `rem`. Les `rem` se basent donc sur la taille de police de l'élément racine de notre document, ici nous leurs avons donné une valeur de 10px. Ce que nous souhaitons c'est envoyer une taille de police et que la fonction retourne sont équivalent en `rem`. La calcul est donc :

```
rem = taille-en-px / valeur-de-base
```

Voici donc comment pourrait être notre fonction :

```scss
@function get-rem($px,$base: $rem-base) {
	$result: $px / $base;
	@return $result;
}
```

Cependant nous avons un problème car c'est une valeur en `px` que retourne notre fonction. Pour régler cette problèmatique nous pouvons utiliser différents moyens, la première étant d'utiliser l'interpolation comme ceci :

```scss
@function get-rem($px,$base: $rem-base) {
	$result: $px / $base;
	@return #{$result} + 'rem';
}
```

La seconde, plus pratique mais moins lisible mais beaucoup plus pratique est de multiplié de résultat obtenu par `1rem` :

```scss
@function get-rem($px,$base: $rem-base) {
	$result: $px / $base;
	@return $result * 1rem;
}
```

## Utiliser une fonction

Quelque soit votre choix vous pouvez maintenant utiliser cette fonction dans votre code, par exemple pour la taille de la police de nos liens de navigation :

```scss
		.navmenu {
			float: right;
			margin: 38px 130px 0px 0px;
			a {
				display: inline;
				font-size: get-rem(16px);
				font-weight: 400;
				margin: 0 10px;
				padding: 35px 10px 10px 10px;
				color: #333;
				&:hover {
					color: #5cadd4;
					border-top: solid 3px $color-primary;
				}
			}
		}
```

Pensez à cette fonctionnalité lorsque vous intégrez une maquette dont la taille en px est souvent bien plus facile à utiliser que de faire le calcul sans cesse. =)


## Conclusion

On peut imaginez un nombre très important de fonctions en CSS comme retourner une couleur par rapport à une autre etc. Cependant trop charger son code de fonction peut rapidement le rendre illisible encore une fois il faut savoir ne pas abuser des bonnes choses.

Les mixins servent donc principalement à éviter la répétition quand les fonctions permettent elles déeffectuer de véritables calculs.

