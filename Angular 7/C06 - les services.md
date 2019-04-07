# CHAPITRE VI : LES SERVICES


Nous avons vu précédemment comment fonctionnaient les components et les différents intérêts d'avoir du code indépendant pour chacun d'eux.
Cependant vous allez avoir rapidement besoin de parties centralisées dont différents components peuvent avoir besoin.
Cela pourrait être le cas pour l'exemple que nous avions pris avec l'authentification.
Cependant devoir à chaque fois passer une valeur à un component puis ainsi de suite serait très compliqué lorsque l'application va grandir.
C'est pourquoi il existe ce que l'on appelle les services.
Les services sont donc des morceaux de code que nous allons spécialement créer pour qu'ils soient utilisés dans plusieurs de nos components.

## Restructuration

Avant de commencer à voir comment créer un service je vous propose de repartir sur une copie plus propre.
Pour cela nous allons supprimer notre précédent répertoire qui contient notre site et en créer un nouveau.
Retourner au début du cours et reprenez les étapes de création une à une jusqu'à intégrer bootstrap et rxjs.

Ensuite générez de nouveau le component devices.
Maintenant pour vous faciliter la vie voici la liste de ce que doivent contenir vos fichiers :

Fichier : app.component.ts
```typescript


import { Component } from '@angular/core';


@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})

export class AppComponent {


  constructor() { };


}
```
Fichier : app.component.html

```html
<div class="container-fluid">
  <div class="container" style="margin-top: 50px;">
    <div class="row">
      <app-devices class="col-12"></app-devices>
    </div>
  </div>
</div>
```

Fichier : devices.component.ts

```typescript
import { Component, OnInit } from '@angular/core';


@Component({
  selector: 'app-devices',
  templateUrl: './devices.component.html',
  styleUrls: ['./devices.component.scss']
})
export class DevicesComponent implements OnInit {


  constructor() { }


  ngOnInit() {
  }


}
```
Fichier : devices.component.html

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


Nous voilà prêt à repartir.


## Injection et instances

Pour être utilisé dans l'application un service doit être injecté dans celle-ci par différents moyens aux choix.
Suivi le choix que vous ferez votre service aura un comportement différent ou plutôt un niveau différent.

Il existe en réalité 3 niveaux différents pour l'injection d'un service et voici leurs différences :

Dans le fichier app.module.ts : En choisissant cette façon de fonctionner votre service utilisera la même instance pour tous les components et les services qui y font référence.
Dans le component AppComponent : De cette façon seulement les components auront accès à la même instance du service.
Dans un autre component : Le component ainsi que l'ensemble de ces enfants auront accès à se service mais pas le reste de l'application.

Vraiment il est important de faire la distinction entre ces trois systèmes dont aucun n'est meilleur ou moins bon que l'autre.
Il s'agit pour chaque service à implémenter d'avoir une réflexion et une cohérence entre le choix de votre injection et votre besoin.

#### Créer un service

Pour créer un service rien de bien compliqué il suffit de créer un dossier que nous appellerons services dans notre dossier app.
Dans ce dossier nous allons créer le fichier TypeScript de notre service qui également par convention s'appellera nom.service.ts.
vous pouvez également utiliser la syntaxe suivante dans le CLI :

ng generate service mon-service

Pour le nom ici nous l'appellerons également devices.

Dans ce fichier nous allons créer la classe du service de cette façon :

```typescript
export class DevicesService {

}
```

Voilà, ni plus ni moins.

#### Injecter un service

Maintenant que nous avons notre service encore faut-il l'injecter pour que notre application y est accès.
Ici nous choisirons l'injection dans app.module.ts qui nous permettra d'avoir une porté générale de notre service à l'intégralité de l'application.
Pour cela ouvrons le fichier app.module.ts à la racine du dossier app et modifions le comme ceci :

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';


import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { FormsModule } from '@angular/forms';
import { DevicesComponent } from './devices/devices.component';
import { DevicesService } from './services/devices.service';


@NgModule({
  declarations: [
    AppComponent,
    DevicesComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    FormsModule
  ],
  providers: [
    DevicesService
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
Les lignes en gras sont à ajouter.
Pour expliquer un peu ce que nous venons de faire c'est très simple, nous avons importé la class DevicesService depuis notre fichier TypeScript.
Ensuite nous l'avons ajouté aux providers de notre app.module. Providers en anglais veut dire fournisseurs. Notre classe fournit un service.

Nous allons d'ailleurs retourner au fichier contenant la classe de notre service et y ajouter un tableau de nos appareils :

```typescript
export class DevicesService {
    devices: [
        {
            deviceName; 'Frigo',
            deviceStatus: 'Off'
        },
        {
            deviceName; 'Machine à laver',
            deviceStatus: 'On'
        },
        {
            deviceName; 'Télévision',
            deviceStatus: 'Off'
        },
        {
            deviceName; 'Radio',
            deviceStatus: 'Off'
        },
        {
            deviceName; 'Ordinateur',
            deviceStatus: 'On'
        }
    ]
}
```

Ensuite nous allons importer le service dans nos deux components app et devices en ajouter les deux lignes suivantes dans leur fichiers TS.
Dans le haut de notre fichier :

`import { DevicesService } from './services/devices.service';`

Dans le constructeur de la classe de notre component :

`constructor(private DevicesService: DevicesService) { };`

Voilà notre service implémenté dans nos deux components.
Maintenant servons nous en ! Notre mission va être de créer une app qui affiche de la même façon que toute à l'heure les informations de nos appareils.
Puis nous ajouterons des méthodes permettant d'éteindre un ou tous les appareils en même temps.
Commençons par créer nos méthodes dans notre service :

```typescript
    onSwitchOn(deviceIndex: number) {
        this.devices[deviceIndex].deviceStatus = 'On';
    }


    onSwitchOff(deviceIndex: number) {
        this.devices[deviceIndex].deviceStatus = 'Off';
    }


    onSwitchOnAll() {
        this.devices.forEach(element => {
            element.deviceStatus = 'On';
        });
    }


    onSwitchOffAll() {
        this.devices.forEach(element => {
            element.deviceStatus = 'Off';
        });
    }
```

 Ensuite implémentons ses méthodes dans nos deux components comme suis :

```typescript
  onSwitchOn(i) {
    this.DevicesService.onSwitchOn(i);
  }


  onSwitchOff(i) {
    this.DevicesService.onSwitchOff(i);
  }


  onSwitchOnAll() {
    this.DevicesService.onSwitchOnAll();
  }


  onSwitchOffAll() {
    this.DevicesService.onSwitchOffAll();
  }
```

Nos méthodes pointes vers celle du service, c'est donc le service qui agit sur lui même.
Nous allons maintenant créer notre liste d'appareils dans notre component devices comme suis :

```html
  <ul class="list-group list-group-flush">
      <li *ngFor="let device of devices; let i=index"
          [ngClass]="{'list-group-item-success': device.deviceStatus === 'On', 'list-group-item-danger': device.deviceStatus === 'Off'}"
          class="list-group-item">Appareil : {{ device.deviceName }}
        <span class="float-right">Status : {{ device.deviceStatus }}</span>
        <div class="mt-3">
            <button class="btn btn-success" (click)="onSwitchOn(i)">Allumer</button>
            <button class="btn btn-danger ml-1" (click)="onSwitchOff(i)">Éteindre</button>
        </div>
      </li>
  </ul>
```

Nous avons ajouter nos appareils avec ngFor et une gestion de la couleur avec ngClass ce que vous connaissez déjà 😃
Ensuite nous avons ajouté deux boutons pointant vers les méthodes de notre component qui lui-même appelle les méthodes du service.
Essayez ces boutons vous verrez que la valeur de deviceStatus change comme nous le souhaitions.
Ensuite nous allons ajouter deux boutons en bas de page mais cette fois-ci dans le component app qui nous permettrons d'éteindre ou allumer l'ensemble.
Voici le code HTML correspondant :

```html
<div class="container-fluid">
  <div class="container" style="margin-top: 50px;">
    <div class="row">
      <app-devices class="col-12"></app-devices>
    </div>
    <div class="row">      
      <div class="col-12 text-center mt-3 mb-3 centered">
        <button class="btn btn-danger mr-3" (click)="onSwitchOffAll()">Tout éteindre</button>
        <button class="btn btn-success" (click)="onSwitchOnAll()">Tout allumer</button>
      </div>
    </div>
  </div>
</div>
```

Nous avons maintenant deux component qui agissent tout deux vers un service commun.
Le point positif et que nos component n'ont pas besoin d'interagir entre-eux pour que l'un est une action sur l'autre.
En effet c'est le service qui se chargera de faire l'intermédiaire et de garantir l'intégrité des valeurs à un instant T entre nos deux components.

