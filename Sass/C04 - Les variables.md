# CHAPITRE IV : LES VARIABLES

À l'époque de la création de SASS la notion de variables n'existait pas encore en CSS. Aujourd'hui il est possible de déclarer des variables avec la syntaxe suivante :

```scss
:root {
	--nom-de-la-variable: valeur;
}	
```

Puis de les utiliser comme suit :

```scss
element {
	color: --var(nom-de-la-variable);
}
```

De ce fait l'utilisation des variables SASS est-elle toujours pertinente ? Nous allons voir que oui car SASS offre une syntaxe plus pratique mais également des fonctionnalités supplémentaires.

## Déclarer une variable

En SASS la déclaration des variables ressemble un peu à la syntaxe que l'on peut retrouver avec PHP. C'est à dire que lors de la déclaration et également lors de leur utilisation les variables sont précédées d'un sigle `$`. Je vous propose d'appliquer ce principe directement à notre couleur bleu qui est succeptible de revenir dans le code SCSS de notre projet :

```scss
$color-primary: #5cadd4;
// du code...
					border-top: solid 3px $color-primary;
				}
			}
		}
	}
}

%btn {
	display: inline-block;
	border: solid 1px $color-primary;
	border-radius: 3px;
	background-color: $color-primary;
	color: #fff;
	padding: 10px;
	min-width: 120px;
	text-align: center;
	&:hover {
		background-color: #fff;
		color: $color-primary;
		transition-duration: 200ms;
	}
}

.btn-standard {
	@extend %btn;
}
.btn-submit {
	@extend %btn;
	padding: 10px 20px;
}
```

Attention à bien déclarer vos variables avant leur utilisation sinon cela ne fonctionnera pas car votre compilateur retournera `undefined variable`.

Dans une variable vous pouvez utiliser également des chaines de caractères comprise entre des guillemets ou appostrophes, ce qui est très pratique dans le cas des chemins répétitifs ou encore des polices à utiliser.

## Utiliser des opérateurs

Avec les variables il est possible d'utiliser des calculateurs arithmétique. Par exemple définissons le padding de nos button dans une variable `$btn-padding` pour notre `btn-submit` nous pourrons utiliser la syntaxe suivante :

```scss
$btn-padding: 10px;
// du code...
.btn-submit {
	@extend %btn;
	padding: $btn-padding + 10;
}
```

Cependant ceci n'est pas vraiment l'idéal dans notre exemple car nous ne souhaitons augmenter que les cotés de notre padding. Mais vous comprennez le principe. Attention il n'est cependant pas possible pour le compilateurs d'additionner des unités différentes sauf dans certains rares cas que je vous laisse voir sur la documentation officielle :

[SASS Opérateurs numériques](https://sass-lang.com/documentation/operators/numeric)

## L'interpolation

L'interpolation va nous permettre de transformer une chaine de caractère en réélle instruction CSS. Concrétement c'est comme si l'on souhaitait y enlever les guillemets ou appostrophes.

Pour illustrer ce principe imaginons que vous souhaitiez encapsuler la valeur d'une media queries pour que celle-ci soit facilement réutilisable dans votre code. Nous prendrons l'exemple d'une largeur de 768px.

Pour utiliser l'interpolation on applique la syntaxe suivante `#{&varibale}` :

```scss
// du code...
			img {
				margin-top: 20px;
				@media #{$media-screen-md} {
					& {
						max-width: 100px;
					}
				}
			}
// du code...
```

Notre image appliquera maintenant les media queries définies pour cette largeur. Il est possible d'aller plus loin et d'imbriquer des variables grace à l'interpolation. Imaginons que nous souhaitions avoir clairement une valeur pour chaque largeur d'écran qu'il serait très facile de remplacer, nous pourrions faire comme ceci :

```scss
$screen-sm: 540px;
$screen-md: 720px;
$screen-lg: 960px;
$media-screen-sm: 'screen and (max-width: #{$screen-sm})';
$media-screen-md: 'screen and (max-width: #{$screen-md})';
$media-screen-lg: 'screen and (max-width: #{$screen-lg})';
```

Cependant vous souhaitez modifier cette valeur uniquement pour certain éléments. Vous pouvez utiliser plusieur fichiers et demander à SASS d'utiliser une valeur par défaut 

## Conclusion

SASS et CSS contiennent tout deux un système de variables mais SASS est bien plus puissant concernant celles-ci, c'est pourquoi malgré leur apparition SASS est loin d'être devenu obsolète sur cette feature.