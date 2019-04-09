# CHAPITRE II : STRUCTURE ET COMPONENTS


## Qu'est-ce que les components ?

Une application angular est basée sur les components mais que sont-ils ?
Les components peuvent être vu comme de petites partie de notre application, on peut visualisé une application comme un puzzle modulable.
Chaque pièce de ce puzzle est en partie indépendante, dans notre cas une pièce correspondrait à un components.

Pour y voir plus clair prenons la structure d'une page web standard, nous avons un component principal qui est notre app elle-même.
Ce component est fait d'un ensemble de sous-component comme une sidebar, le header etc qui peuvent eux même contenir des sous component.
Nous avons donc des components emboîtes ou "nested" dans d'autres components.

Alors comment bien choisir quel item correspondrons à un component ? Nous y reviendrons plus tard, je vous aiderez à définir ce qui doit l'être et ce qui ne l'est pas.
La pratique vous permettra de le déduire assez facilement, vous verrez.


## La structure d'angular

Lorsque l'on créer un nouveau projet avec Angular une masse importante de fichiers est créée. Je vous propose ici de voir un peut ce qui les compose.
Le premier dossier e2e est généré pour ce qui correspond au tests end to end sur lesquels nous ne nous pencherons pas dans ce cours mais c'est bon à savoir.
Vous pouvez vous renseigner sur ce que sont ces tests une fois Angular à peu prêt maîtrisé du moins en surface. Pour cela je vous conseil dans un premier temps de lire cette page.

Nous avons également le dossier node_modules qui contient d'ailleurs notre dossier bootstrap comme nous l'avons vu précédemment.
En effet ce dossier va contenir l'ensemble des dépendances installées nativement par Angular mais également nos dépendances installée personnellement.

Maintenant voyons le dossier src celui ci va contenir l'ensemble des fichiers source de notre application.
D'ailleurs on y retrouve le fichier index.html à sa racine. Ouvrons le :

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Website</title>
  <base href="/">


  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
</body>
</html>
```

Rien de choquant ici, un fichier assez classique mise à part une chose un peu bizarre.. On ne voit pas le contenu affiché dans notre navigateur !
Regardons d'un peu plus prêt, une balise app-root semble être l'unique balise de notre body et ne contient rien, étrange.
En effet cette balise n'est pas un standard HTML, elle permet à Angular de remplacer cette balise par le component en question.

Allons maintenant voir ce que contient ce component pour cela rien de plus facile nous allons ouvrir le fichier src/app qui contient nos components.
Pour l'instant nous pouvons voir plusieurs fichiers dont :

* **app.component.ts** : Ce fichier contient le TypeScript de notre component app.
* **app.component.scss** : Ce fichier contient le CSS au format SCSS de notre component.
* **app.component.html** : Ce fichier contient la structure HTML de notre component.

Enfin on y retrouve également un fichier appelé app.module.ts ainsi qu'un fichier app-routing.module.ts.
Nous n'expliquerons pas l'intérêt de ces fichiers pour le moment sachez simplement que le routing permet d'avoir une application de plusieurs pages tout en restant une Single Page Application.

Maintenant ouvrons justement le fichier HTML de notre component app que y retrouvons nous ?
Et bien c'est effectivement le code que l'on voit apparaître sur notre page d'accueil, essayez de le modifier vous verrez 😃

Maintenant au tour du fichier TypeScript que contient t'il ? Ceci :

```typescript
import { Component } from '@angular/core';


@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  title = 'website';
}
```

A quoi correspondent ces quelques lignes me direz-vous ? Nous allons les décrire :

@Component : C'est un décorateur TypeScript, les décorateurs permettent d'utiliser des fonctions et de leur passer des paramètres.
Jusqu'ici rien de bien différent d'une fonction classique, en effet. Mais les décorateurs ont deux atouts ;

Ils rendent le code bien plus lisible et compréhensible.
Ils permettent d'étendre le comportement d'une fonction sans altérer la fonction à laquelle ils font référence.

Ici notre décorateur @Component prend donc un objet en paramètre qui contient 3 propriétés dont :

* **selector: 'app-root'** : On comprend que c'est le nom de la balise qui sera indiqué dans notre code HTML. Et par conséquent le selecteur dont Angular se servira.
* **templateUrl: './app.component.html'** : Le lien vers le template HTML de notre component.
* **styleUrls: ['app.component.scss']**: Le style de notre CSS sous forme de tableau ce qui permet d'en ajouter plusieurs.

Finalement rien de bien compliqué dans notre fichier. Ensuite nous avons export class AppComponent qui contient une propriété title dont la valeur est le nom de votre projet.
Remplacer la valeur de title par ce que vous souhaitez et rechargez la page d'accueil d'Angular.
Le titre h1 à changer selon la valeur que vous avez indiqué 😃. Nous analyserons cela plus tard


## Créer son premier component

Pour créer un component il nous suffit d'utiliser l'instruction ng generate component à la racine de notre site.
Cette instruction s'utilise de cette façon :

`ng generate component mon-component`

Il est également possible de raccourcir la commande en utilisant g et c pour generate et component.
Personnellement je vais appeler ce component header pour que cela reste crédible 😃
Notre console affiche ceci :

```
CREATE src/app/header/header.component.scss (0 bytes)
CREATE src/app/header/header.component.html (25 bytes)
CREATE src/app/header/header.component.ts (270 bytes)
UPDATE src/app/app.module.ts (475 bytes)
```

Un nouveau component est créé dans un nouveau répertoire portant son nom.
Dans ce répertoire nous retrouvons les fichiers que nous avons vu précédemment.
Nous voyons également que le fichier app.module.ts a été mis à jour, en réalité si on ouvre ce fichier nous pouvons voir qu'il contient la liste de nos components.
Cette ligne vient d'être ajoutée :

```typescript
import { HeaderComponent } from './header/header.component';
```

et celle-ci dans le décorateur @NgModule :

```typescript
    HeaderComponent
```

Ce sont des étapes nécessaires pour pouvoir utiliser notre component dans notre app Angular.
Allons voir du côté du fichier TypeScript de notre nouveau component.

Pour le décorateur @Component voici les valeurs crées par Angular :

```typescript
@Component({
  selector: 'app-header',
  templateUrl: './header.component.html',
  styleUrls: ['./header.component.scss']
})
```

Donc notre selecteur contient le nom de notre component préfixé de app- ce qui est pratique car cela ne rentrera pas en confusion avec une balise header HTML.
Maintenant allons voir du côté du fichier HTML :
```html
<p>
  header works!
</p>
```

Ok rien de bien compliqué dans celui-ci.
Je vous propose de vérifier si notre component fonctionne et pour cela voici ce que nous allons faire.
Allez dans votre component principal et dans le fichier app.component.html et ajoutez la balise `<app-header><app-header/>`.
Rechargez la page d'accueil de votre app et voyez ce qu'il se passe, votre composant est inséré à l'endroit de la balise 😃
It's works !

