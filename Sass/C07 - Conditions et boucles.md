# CHAPITRE XII : CONDITIONS ET BOUCLES

SASS permet également l'utilisation de conditions et de boucles. Nous allons dans ce court chapitre voir comment les utiliser et surtout dans quels cas celles-ci peuvent nous être utiles.


## Les conditions

Commençons par être clair, les conditions vont très rarement vous être utile car vos fichier CSS sont statiques et ne dépendent pas d'intéractions avec l'utilisateur. Ainsi les conditions s'utilisent uniquement pour vous dédouaner d'une réflexion trop lourde comme un calcul à répétition etc. Malheureusement il est particulièrement difficile de trouver un exemple concret car dans les faist les conditions sont très peu utilisées. Quand c'est le cas c'est au coeur de fonctions.

Leur syntaxe est la suivante :

```scss
@if (condition) {
	// code si vérifiée;
} @else {
	// du code...
}
```

Vous pouvez utiliser des opérateurs de comparaison (`>`, `<`, `==`, `!=`, `<=`, `>=`), logique (`and`, `not`, `or`) etc. Il existe meme les booléen `true` et `false`.

Pour illustrer le principe imaginons que nous refusions une taille plus petite que `0.8rem` en valeur de retour de notre fonction `get-rem`. Nous pouvons donc indiquer la condition suivante :


```scss
@function get-rem($px,$base: $rem-base) {
	$result: $px / $base;
	$result: $result * 1rem;
	@if ($result < 0.8rem) {
		@return 0.8rem;
	} @else {
		@return $result;
	}
}
```

Maintenant notre fonction ne retournera jamais de valeur inférieure à `0.8rem`.



## Les boucles

Les boucles en revanche peuvent être utilent dans bien des cas. Il en existe deux types, les boucles de type `each` qui s'utilisent sur des listes (qui sont àpeut prêt la meme chose que des tableaux) et des boucles de type `for`.

### La boucle `for`

```scss
@for $i from 1 through 10 {
	// du code...
}
```

Ici la boucle effectuera 10 itérations. Nous pouvons immaginez des classes permettant d'ajouter des marges comme avec bootstrap pour cela nous pourrions utiliser l'instruction suivantes pour les créer en une seule fois :

```scss
@for $i from 1 through 10 {
	.m-#{$i} {
		margin: #{$i} * $rem-base * 1rem;
	}
	.mt-#{$i} {
		margin-top: #{$i} * $rem-base * 1rem;
	}
	.mb-#{$i} {
		margin-bottom: #{$i} * $rem-base * 1rem;
	}
	.ml-#{$i} {
		margin-left: #{$i} * $rem-base * 1rem;
	}
	.mr-#{$i} {
		margin-right: #{$i} * $rem-base * 1rem;
	}
}
```

Nous aurions à l'issue 50 classes classes prêtes à l'emploi. Essayez ce code et observez son résultat en l'appliquant à un élément HTML.

## La boucle `each`

La boucle each s'utilise donc sur une liste et peut être utile dans de nombreux cas et le plus souvent dans le cas des classes d'icones ayant les même propriétés mais des background différents. Imaginons que vous ayez une bibliothèque d'icones au format svg prête à l'emploi. Vous pouriez utiliser ce type de boucle pour générer vos icones :

```scss
$icons: mail, contact, phone, list;
@each $icon in $icons {
	.icon-#{$icon} {
		background: url(asset/icon-#{$icon}.svg);
	}
}
```

Nous avons maintenant une classe par icone. Il faudra bien sûr penser à créer la liste et à nommer correctement ses icones.

## Conclusion

Les conditions sont rarement utilent sauf dans certains cas. En revanche les boucles peuvent clairement vous servir car les répétitions en CSS sont monnaie courrante.
