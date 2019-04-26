# CHAPITRE IX : LA VALIDATION DES DONNÉES

Nous avons créer un formulaire fonctionnel grâce aux outils de Symfony. Je vous propose d'aller un peu plus loin dans ce chapitre en regardant comment fonctionne la validation des données avec saisies avec le framework.

## Les vérifications natives

Symfony dispose d'un système de vérification automatique pour les champs basés sur ce que nous avons renseigné pour notre entité. Cette vérification n'est pas optimale mais permet de sécuriser un minimum les données transmises.

Pour vous donner un exemple nous allons créer un nouveau bien et modifier le contenu de notre page HTML via la console du navigateur avant de le soumettre. Et plus précisement nous modifierons le champ code postal dans lequel on remplacera le type `number` par le type `text` et nous y entrerons n'importe quel petit texte.

Je vous laisse faire cette manipulation et envoyer le formulaire. Que se passe-t-il ? Et bien nous sommes directement redirigé sur le formulaire et un champs d'erreur est affiché ! Ce qui est pas mal du tout car nous n'avons même pas eu besoin de créer un rendu pour les erreurs, le theme Bootstrap selectionné met tous ça en forme =).

Cependant si nous ne trafiquons pas notre page le code postal pourrait très bien être `42` ou n'importe quel autre nombre alors que nous souhaitons précisemment un nombre à 5 chiffres dans notre cas ! 

## Les vérifications personnalisée

Il est bien évidemment possible de créer nos propres règles de validation. Ces règles sont à définir via des contraintes que propose le frameworks. Il est également possible de créer ses propres contraintes mais ce n'est en générale pas nécessaire ou rarement car les contraintes proposées sont nombreuses et la contrainte `regexp` permet de définir une expression régulière ce qui en général s'avère suffisant lorsque l'on ne trouve pas de contrainte plus appropriée.

L'ensemble des contraintes disponibles peuvent être visualisées sur la documentation à la page suivante :

[Symfony Constraints](https://symfony.com/doc/current/validation.html#validator-constraint-targets)

### Ajouter une contrainte

Pour ajouter une contrainte à un champ il existe différentes manières de procéder comme souvent avec le framework. Concrétement vous pouvez utiliser :

* Le format avec annotations
* Le format YAML
* Le format XML
* Le format PHP

Vu que nous sommes parti sur un système utilisant les fichiers Yaml je vous propose de continuer en ce sens pour garder une certaine logique structurelle dans notre application.

Pour ajouter une contrainte il nous faut dans un premier temps activer la validation dans le fichier `config/packages/framework.yaml` et ajouter les instructions de configuration suivantes :

```yaml
framework:
    validation: { enabled: true }
```

Dans le cas des annotations le format est légèrement différent et prendra cette forme :

```yaml
framework:
    validation: { enable_annotations: true }
```

Ensuite il nous faut créer un dossier `validator` qui contiendra un fichier `validation.yaml` dans lequel nous pourrons préciser les contrainte à appliquer pour chacune des propriétés de notre entité. Voici comment appliquer par exemple la contrainte `NotBlank` que propose Symfony sur la propriété `description` de notre entité `property` :

```yaml
App\Entity\Property:
  properties:
    description:
      - NotBlank: ~
```

Toutes les contraintes commence par un tiret `-` et dans le cas de `NotBlank` il faut ajouter un `~` à la fin, un alis de `null`. Maintenant ajoutons une contrainte pour la propriété `postal_code`. Je vous propose les contraintes suivante :

* `Range` : Qui peut prendre une valeur minimum et maximum.
* `Length` : Qui nous permettra de préciser le nombre minimum de chiffres à renseigner.

Voilà ce que cela donne pour notre propriété :

```yaml
    postal_code:
      - Range:
          min: 1000
          max: 99999
      - Length:
          min: 5
```

Ici j'ai choisi le nombre 1000 en valeur minimum car si vous entrez la valeur 01000 Symfony convertira ce nombre en 1000... En effet cela est logique car mathématiquement parlant cette valeur serait juste ! Nous ne nous attarderons pas sur cette problèmatique mais sachez que dans le cas des codes postaux il aurait était intéressant d'utiliser une chaine de caractère.

Attention car la syntaxe est très précise avec Yaml et les erreurs sont courantes ! Il suffit d'un espace manquant, une mauvaise indentation pour que les instructions ne fonctionnent pas ! C'est pourquoi il est tout à fait possible d'utiliser la syntaxe suivante qui prend également moins de place :

```yaml
App\Entity\Property:
  properties:
    description:
      - NotBlank: ~
    postal_code:
      - Range: { min: 1000, max: 99999 }
      - Length: { min: 5 }
```

Si vous testez maintenant d'envoyer votre formulaire en ne respectant pas ces contraintes, vous aurez le droit à un message vous indiquant que le contenu des champs concernés. Ces messages sont prédéfinis par Symfony et sont en générale assez précis ce qui est une bonne chose. Mais vous pouvez également indiquer vos propres messages suivant les contraintes.

### Ajouter un message

Pour ajouter un message associé à une contrainte il vous suffit de l'indiquer directement dans votre fichier `Yaml` voici par exemple deux messsages personnalisés pour un nombre trop grand ou trop petit :

```yaml
App\Entity\Property:
  properties:
    description:
      - NotBlank: null
    postal_code:
      - Range: { min: 0, max: 99999, minMessage: "C'est trop petit voyons", maxMessage: "La valeur est trop grande pour un code postal" }
      - Length: { min: 5 }
```

Comme vous pouvez le constater les message s'affichent comme attendus. Notez également la syntaxe des chaines de charactère, je n'ai pas utilisé d'apostrophes mais des crochets car pour échapper un apostrophe le backslash est inutile il faut utiliser sont équivalent html soit `&apos;` ce qui rend notre chaine de charactères peu lisible.


## Conclusion

Finalement comment fonctionne Symfony sur la gestion des contraintes ? Eh bien Symfony ajoute des attributs HTML lorsque cela est rendu possible. Par exemple si vous utilisez une expression régulière avec la contrainte `regexp` alors l'attribut pattern sera ajouté ce qui ajoute une gestion côté front-end de notre application. Parfois, même souvent, il n'existe pas d'attributs HTML pour la contrainte en question alors Symfony ajoutera un message après vérification. Si l'utilisateur modifie la valeur du pettern un message d'erreur sera tout de même transmit et le formulaire invalide car Symfony ne compte bien sûr pas sur le code HTML peu fiable de notre page mais bien sur les informations stockés côté backend de notre applications qui elles peuvent être considérées comme fiables.

Il est possible d'aller plus loin avec la gestion des erreurs et l'interface de validation de Symfony. Il est également possible de créer vos propres contraintes mais ça ne sera pas le sujet de ce cours. Vous pouvez vous rendre dans la documentation citée ci-dessus si vous souhaitez dès à présent aller plus loin.

Nous ne sommes pas allez bien loin avec ce système car l'important était de vous montrer comment cela fonctionnait et sachant que notre site n'est pas destiné à une véritable agence nous perdrions simplement du temps à implémenter ces contraintes.