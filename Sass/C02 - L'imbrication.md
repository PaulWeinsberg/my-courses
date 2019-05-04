# CHAPITRE II : L'IMBRICATION

L'imbrication permet donc d'éviter les répétions et de rendre notre code plus lisible. Nous allons rapidement voir comment cela fonctionne avec quelque exemples concrets. Pour mieux comprendre ces exemple je vous recommande fortement d'ouvrir le fichier `index.html` car il contient les différentes classes et éléments sur lesquels nous allons appliquer des styles.
## Syntaxe

La syntaxe de l'imbrication est simple est un exemple sera plus parlant qu'un long discours. Pour le code CSS suivant :

```scss
a {
	text-decoration: none;
	color: #5cadd4;
}
.main-container .topbar {
  position: fixed;
  top: 0;
  width: 100%;
  height: 100px;
  background-color: #fff;
  border-bottom: solid 1px #ddd;
  z-index: 999;
}
.main-container .topbar .logoheader {
  position: relative;
  display: inline-block;
  margin-left: 100px;
  height: 100px;
  vertical-align: middle;
}
.main-container .topbar .logoheader img {
  margin-top: 20px;
}
.main-container .topbar .navmenu {
  float: right;
  margin: 38px 130px 0px 0px;
}
.main-container .topbar .navmenu a {
  display: inline;
  font-size: 15px;
  font-weight: 400;
  margin: 0 10px;
  padding: 35px 10px 10px 10px;
  color: #333;
}
```

Le code SCSS correspondant serait le suivant :

```scss
a {
	text-decoration: none;
	color: #5cadd4;
}
.main-container {
	.topbar {
		position: fixed;
		top: 0;
		width: 100%;
		height: 100px;
		background-color: #fff;
		border-bottom: solid 1px #ddd;
		z-index: 999;
		.logoheader {
			position: relative;
			display: inline-block;
			margin-left: 100px;
			height: 100px;
			vertical-align: middle;
			img {
				margin-top: 20px;
			}
		}
		.navmenu {
			float: right;
			margin: 38px 130px 0px 0px;
			a {
				display: inline;
				font-size: 15px;
				font-weight: 400;
				margin: 0 10px;
				padding: 35px 10px 10px 10px;
				color: #333;
			}
		}
	}
}
```

Il suffit donc d'utiliser une arborescence similaire à celle qu'on retrouve en HTML ! Le code est plus clair et nous évite considérablement les répétitions à outrance ! Cependant trop d'imbrication tue l'imbrication et retire la lisibilité gagnée. On considère qu'un niveau de trois est à ne pas dépasser mais il s'agit d'un avis personnel et souvent recommandé.

Vous pouvez également ouvrir le fichier CSS généré et voir comment SASS à compilé votre fichier SCSS.

## L'opérateur &

Maintenant que vous savez utilisé l'imbrication nous allons voir comment pousser la machine un peut plus loin et comprendre le fonctionnement d'un opérateur simple, l'opérateur `&`. Celui permet de faire référence à l'élément parent. Pronons un exemple avec nos boutons de navigation, imaginons que nous souhaitons créer un `hover`, ce qui pourrais donner ceci en CSS :

```scss
// du code...
.main-container .topbar .navmenu a:hover {
  color: #5cadd4;
  border-top: solid 3px #5cadd4;
}
// du code...
```

Avec SCSS et l'opérateur `&` cela devient considérablement plus simple :

```scss
// du code...
			a {
				display: inline;
				font-size: 15px;
				font-weight: 400;
				margin: 0 10px;
				padding: 35px 10px 10px 10px;
				color: #333;
				&:hover {
					color: #5cadd4;
					border-top: solid 3px #5cadd4;
				}
			}
// du code...
```

Attention aux espaces avec l'opérateurs `&` cela fonctionne comme en CSS classique. Il est également possible d'utiliser l'opérateur `&` dans un ordre différent. Par exemple (*ne copiez pas ce code dans votre fichier*) si vous utiliser la syntaxe suivante :

```scss
.btn {
	.green & {
		background: green;
	}
}
```

Alors le code de sortie ne sera pas `.btn .green .btn {}` mais simplement `.green .btn {}` ce qui permet donc de limité encore une fois les répétition et dans notre exemple de définir toutes les variates de la classe `.btn` dans une meme imbrication.