# CHAPITRE III : GÉRER DES DONNÉES DYNAMIQUE


Nous avons donc vu comment créer notre component maintenant nous allons voir comment le faire interagir avec le DOM.
Pour cela nous allons utiliser la liaison de données ou databinding en anglais. 

Le databinding représente donc la liaison entre votre code TypeScript et le DOM de votre application.
Il existe donc deux formes de direction pour cette communication :

La première lorsque les éléments présent dans votre code qui viennent d'être calculé ou qui y sont stocké ou encore récupérés sur un serveur. Deux catégories :
`String interpolation` ou interpolation
`Property binding` ou liaison de propriété
La seconde quand les informations doivent être récupéré par votre TypeScript depuis le DOM.
Event binding ou liaison d’événement

Il peut également y avoir une communication dans les deux sens dans ce cas on nommera cette communication `two-way-binding`.
Nous allons donc voir ces différentes formes de communication plus en détails.


## Préparation

Premièrement nous allons supprimer notre component nouvellement créé.
En effet celui-ci servait d'exemple et n'est pas le plus approprié à la suite du cours.
Pour cela :

* Supprimer le dossier du component
* Rendez-vous dans votre fichier app.module.ts et supprimer les ligne qui se rapport à celui-ci

Ensuite nous allons créer un nouveau component que nous appellerons devices car nous allons nous imaginer une app pour gérer nos appareils de la maisons.
Une fois ce component créer aller dans le component principal et remplacez le code du fichier HTML par celui-ci :

```html
<div class="container-fluid">
  <div class="container" style="margin-top: 50px;">
    <div class="row">
      <app-devices class="col-12"></app-devices>
    </div>
  </div>
</div>
```

Puis allons dans notre component devices et faisons de même :

```html
<div class="card">
  <div class="card-header">
    Liste des appareils
  </div>
  <ul class="list-group list-group-flush">
      <li class="list-group-item">Appareil : truck | Status : </li>
      <li class="list-group-item">Appareil : truck | Status : </li>
      <li class="list-group-item">Appareil : truck | Status : </li>
      <li class="list-group-item">Appareil : truck | Status : </li>
  </ul>
</div>
```

Voilà notre petite liste d’appareils nouvellement créée nous allons enfin pouvoir jouer un peu avec.


## String interpolation

C'est la manière la plus basique d’émettre des données depuis notre code TypeScript vers le DOM.
Nous allons créer deux propriété dans notre classe DevicesComponent qui se trouve dans le fichier TypeScript du component.

```typescript
  DevicesName: string = 'Frigo';
  DevicesStatus: string = 'Allumé';
```

Ensuite retournons dans notre fichier HTML et modifions le comme ceci en utilisant les doubles accolades {{ }} qui représente la syntaxe d'interpolation.

```html
      <li class="list-group-item">Appareil : {{ DevicesName }} -- Status : {{ DevicesStatus }}</li>
      <li class="list-group-item">Appareil : {{ DevicesName }} -- Status : {{ DevicesStatus }}</li>
      <li class="list-group-item">Appareil : {{ DevicesName }} -- Status : {{ DevicesStatus }}</li>
      <li class="list-group-item">Appareil : {{ DevicesName }} -- Status : {{ DevicesStatus }}</li>
```

Actualisez votre page, que ce passe-t-il ? Vous voyez apparaître Frigo et Allumé à la place du nom des propriété 😃
Maintenant imaginons que vous ayez plusieurs appareils nous allons transformer nos propriétés en tableaux.

Dans notre fichier TypeScript :

```typescript
  devicesName: (string)[] = ['Frigo','Lumières','Sèche cheveux','Chauffe-eau'];
  devicesStatus: (string)[] = ['Allumé','Allumé','Eteint','Veille'];
```

Dans notre fichier HTML :

```html
      <li class="list-group-item">Appareil : {{ devicesName[0] }} -- Status : {{ devicesStatus[0] }}</li>
      <li class="list-group-item">Appareil : {{ devicesName[1] }} -- Status : {{ devicesStatus[1] }}</li>
      <li class="list-group-item">Appareil : {{ devicesName[2] }} -- Status : {{ devicesStatus[2] }}</li>
      <li class="list-group-item">Appareil : {{ devicesName[3] }} -- Status : {{ devicesStatus[3] }}</li>
```

Voilà qui est mieux !
On peut utiliser toutes expression TypeScript valable pour ce qui est des interpolations pour faire un example concret nous allons utiliser une méthode.
Pour cela nous allons simplement créer une méthode pour récupérer les informations de nos appareils dans la classe de notre component devices.

```typescript
  getStatus(deviceNumber: number): string {
    let deviceStatus = this.devicesStatus[deviceNumber];
    return deviceStatus;
  }
```

Et dans notre fichier HTML

```html
      <li class="list-group-item">Appareil : {{ devicesName[0] }} -- Status : {{ getStatus(0) }}</li>
      <li class="list-group-item">Appareil : {{ devicesName[1] }} -- Status : {{ getStatus(1) }}</li>
      <li class="list-group-item">Appareil : {{ devicesName[2] }} -- Status : {{ getStatus(2) }}</li>
      <li class="list-group-item">Appareil : {{ devicesName[3] }} -- Status : {{ getStatus(3) }}</li>
```

Le résultat est identique 😃
Bien sûr il faut vous projeter, vous pouvez imaginer une méthode qui communiquerez les résultats d'une API.

#### Rappel de syntaxe :

`{{ String ou lien vers une variable ou méthode retournant une String }}`

 
## Property binding

Avec le property binding ce n'est pas l'objectif d'afficher la valeur d'un élément de notre TypeScript mais plutôt de modifier notre DOM en fonction de la valeur de notre TypeScript.
Imaginons que vous souhaitiez que votre application puisse pouvoir éteindre les appareils en fonction de l'utilisateur.
En gros si celui-ci est authentifier il pourra éteindre les appareils sinon il ne le pourra pas.

Dans un premier temps créons notre bouton dans notre component principal :

```html
<button class="btn btn-primary" style="margin-top: 10px" disabled>Éteindre les appareils</button>
```

Evidemment nous n'allons pas entrer dans la création d'un réel système d'authentification pour cet exemple. Nous allons créer une propriété isAuth.
En effet l'authentification peut être intéressante pour de nombreux component ainsi elle doit être définie en amont du component devices.

Dans notre fichier TypeScript de notre component principal voilà la ligne ajoutée à notre classe :

```typescript
  isAuth: boolean = true;
```

Maintenant allons de le HTML de notre component et remplaçons la propriété disabled du bouton par ceci :

```html
<button class="btn btn-primary" style="margin-top: 10px" [disabled]="isAuth">Éteindre les appareils</button>
```

Nous avons mis entre crochets [] notre attribut disabled puis celui-ci est suivi de ="" et le nom pointant vers notre booléen.
En réalité ceci fonctionne comme une condition, SI isAuth = true ALORS disabled sinon RIEN.
Cependant nous souhaitons quelques chose de différent car nous voulons que le bouton soit désactivé lorsque nous ne sommes pas connectés.
Ainsi il suffit de d'ajouter un point d'exclamation à isAuth ce qui nous donnera finalement ceci :

```html
<button class="btn btn-primary" style="margin-top: 10px" [disabled]="!isAuth">Éteindre les appareils</button>
```

Si vous avez suivi jusqu'ici vous commencer à comprendre que tout ce qui se trouve entre les crochets doit être une expression TypeScript valide.
Il est donc possible d'insérer des expression plus complexes.

Maintenant rendons notre code un peu plus dynamique et faisons en sorte que notre bouton apparaisse dans un premier temps désactivé puis activé au bout de 3 secondes.
Pour cela ce que nous pouvons faire c'est modifier notre classe de notre AppComponent et y insérer un timer dans le constructor :

```typescript
export class AppComponent {
  isAuth = false;
  constructor() {
    setTimeout(() => {
      this.isAuth = true;
    },3000);
  }
}
```

#### Rappel de syntaxe :

`[propriété liées]="code TypeScript valide"`


## Event binding

Avec le Property binding et la String interpolation nous avons vu deux moyens de faire communiquer notre TypeScript vers notre DOM.
Maintenant nous allons voir le cas inverse avec l'Event binding qui contrairement aux deux précédent transfert des données du DOM vers le TypeScript en fonction d'événements.

Nous allons donc créer une méthode qui enverra vers la console le message 'On allume tout !'.
Pour cela modifions encore notre class AppComponent mais cette fois ci en créant une méthode nommée turnOnAll() :

```typescript
  onTurnOnAll(): void {
    console.log('On allume tout !');
  }
```

Notez qu'ici le nom de la méthode commence par on, c'est une convention pour les méthodes liées au DOM.

Et dans notre fichier HTML on ajoutera aux attributs du bouton :

`(click)="onTurnOnAll()"`

Essayez de clicker sur le bouton et voyez comment votre code réagit dans la console 😃
Et oui le message s'affiche comme nous l'attendions.
La syntaxe n'est pas bien compliquée en réalité nous avons deux parenthèses () entre lesquelles nous insérons le noms de n'importe quel événement du DOM.
Ensuite vient le sigle = puis les crochets "" entre lesquels il suffit de placer n’importe quelle expression typescript valide 😃

#### Rappel de syntaxe :

`(événement du DOM)="code TypeScript valide"`


## Two-way binding

Le liaison two-way binding est une liaison à double sens comme nom l'indique. Elle utilise la liaison par événement et par propriété en même temps.
Elle peut être utile dans de nombreux cas mais son utilisation de loin la plus fréquente est dans la gestion des formulaires.

Pour pouvoir utiliser ce type de liaison il va falloir importer dans notre application le module FormsModule qui se trouve dans  @angular/forms
Pour cela il suffit de placer la ligne suivante dans le fichier app.module.ts :

```typescript
import { FormsModule } from '@angular/forms';
```

Puis dans le même fichier et dans le décorateur @NgModule dans le tableau de la propriété imports :

```typescript
    FormsModule
```

Voilà vous avez importé un module qui va maintenant pouvoir nous servir.
La syntaxe du two-way binding reprenant celle des deux liaisons qu'elle implémente, la voici :

`[(directive)]="élément lié"`

Nous allons créer un input en dessous de notre liste d'appareils dans notre HTML du component devices.
Dans cet input nous allons lier deux chose le nom d'un appareil du tableau devicesNameavec la valeur de notre input.
Pour cela nous allons placer dans notre input un two-way binding avec la directive ngModel qui nous permettra de lier les deux éléments.

Voici notre input nouvellement créé :

```html
      <input type="text" class="form-control" [(ngModel)]="devicesName[1]">
```

Maintenant essayez d'entrer ce que vous souhaitez dans cet input et voyez ce qu'il se passe.
Premièrement la valeur de l'input correspond déjà à la valeur du nom de notre appareil deuxièmement lorsqu'on la modifie les élément constitué de cette valeur ce modifient également.
Vous venez de créer une liaison dite Two-way binding 😃



## Les propriétés personnalisées

Les propriétés personnalisés vont permettre de transférer des données d'un component à un autre grace au décorateur @Input()
Pour réaliser une opération de ce type nous allons commencer créer un objet comportant nos deux tableaux devicesName et devicesStatus dans le component root.

```typescript
  devices = {
    devicesName: ['Frigo','Lumières','Sèche cheveux','Chauffe-eau'],
    devicesStatus: ['Allumé','Allumé','Eteint','Veille']
  }
```

Pour continuer avec ce component nous allons nous rendre dans sa partie HTML qui appelle le component devicesComponent.
Dans cette balise nous allons insérer un nom différent de l'objet que nous souhaitons transférer par exemple devicesObject.
En effet si nous donnons un nom identique cela va créer une confusion dans le code JavaScript imaginez devices = devices...
Enfin nous allons réaliser un property binding entre cette propriété personnalisée et et notre object devices :

```html
<app-devices [devicesObject]="devices" class="col-12"></app-devices>
```

Nous allons maintenant utiliser le décorateur @Input() et pour que celui-ci fonctionne il nous faut l'inclure dans la première ligne de notre fichier TypeScript :

```typescript
import { Component, OnInit, Input } from '@angular/core';
```

Ensuite nous allons justement nous occuper de ce décorateur et lui demander de récupérer notre propriété personnalisée :

```typescript
  @Input() devicesObject: object;
```

Voilà notre object est importé maintenant modifions notre code HTML pour que celui-ci affiche ce que l'on souhaite :

```html
      <li class="list-group-item">Appareil : {{ devicesObject.devicesName[0] }} -- Status : {{ devicesObject.devicesStatus[0] }}</li>
      <li class="list-group-item">Appareil : {{ devicesObject.devicesName[1] }} -- Status : {{ devicesObject.devicesStatus[1] }}</li>
      <li class="list-group-item">Appareil : {{ devicesObject.devicesName[2] }} -- Status : {{ devicesObject.devicesStatus[2] }}</li>
      <li class="list-group-item">Appareil : {{ devicesObject.devicesName[3] }} -- Status : {{ devicesObject.devicesStatus[3] }}</li>
      <input type="text" class="form-control" [(ngModel)]="devicesObject.devicesName[1]">
```

Voilà nous venons de créer notre première propriété personnalisée 😃