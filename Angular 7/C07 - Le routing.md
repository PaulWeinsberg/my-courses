# CHAPITRE VII : LE ROUTING

Nous en avons parlé au début de ce cours, Angular est principalement utilisé pour créer des application de type Single Page Application.
Mais dans ce cas comment fonctionne la navigation si nous souhaitons avoir une page radicalement différente d'une autre avec une URL ?
Et bien ne vous inquiété pas Angular est construit en ce sens et permet de simuler totalement ce type de navigation sans avoir besoin de recharger la page.
Dans ce chapitre nous allons étudier le module qui va nous permettre de gérer la navigation de notre application, c'est le routing.

Je vous prose dans ce chapitre de :

* Créer une page de connexion fictive
* Afficher un appareil en particulier en details
* Ne pouvoir accéder a certaines fonctionnalités qu'en étant authentifié


## Créer un router pour son app


Vous allez voir dans cette partie que le router n'a rien de bien tortueux une fois compris il s'intègre bien dans la logique Angular.


#### Création du component auth


Premièrement nous allons créer un component sur lequel nous reviendrons par la suite, ce component s'appellera auth.
Il nous servira pour l'authentification d'un utilisateur.


#### Création du fichier app-routing


Ensuite nous allons créer un fichier à la racine de notre dossier app qui s'appellera app-routing.module.ts.
Il est possible que ce fichier existe déjà suivant la configuration que vous avez sélectionné lors de la création de votre projet.


Dans ce fichier nous allons faire plusieurs choses et la première va être d'importer ces dépendances :

```typescript
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { AuthComponent } from './auth/auth.component';
import { DevicesComponent } from './devices/devices.component';
```

Nous importons ici NgModule car nous allons devoir importer et exporter des informations du RouterModule.
D'ailleurs nous importons également ce module ainsi que Routes qui va nous permettre de définir des paths pour notre application.
Ensuite nous importons différent component qui vont nous servir pour ces paths.


Maintenant que nous avons nos dépendances il va nous falloir définir ces paths et pour cela nous allons créer une constante.
Cette constante s'appellera routes et sera une instance de la classe Routes que nous venons d'importer.
Elle contiendra un tableau d'objet littéraux donc les propriétés path et component contiendrons :

path: Le chemin contenu après l'adresse de notre application web et après le slash.


Voici ce que nous ajoutons à notre fichier :

```typescript
const routes: Routes = [
  {
    path: 'auth',
    component: AuthComponent
  },
  {
    path: '',
    component: DevicesComponent
  },
      path: 'devices'    component: DevicesComponent
  }
];
```

Maintenant que nous avons défini nos différent routes nous allons les définir grâce à la méthode forRoute() qui prend en paramètre notre constante.
Ensuite nous exporterons le module RouterModule pour que celui-ci soit accessible.
Pour finir nous créerons une classe au cas ou elle nous servirait.

```
@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Voilà notre fichier contient toutes les informations dont nous avons besoin.
Avant de continuer nous allons tout de même importer la classe que nous venons de créer dans le fichier app.module.ts ainsi que le contenu du fichier  :

```typescript
import { AppRoutingModule } from './app-routing.module';
```

Puis nous allons l'ajouter aux imports du décorateur @NgModule :

```typescript  
imports: [
    BrowserModule,
    AppRoutingModule,
    FormsModule
  ],
```

Modifier le component principal

Notre component principal doit maintenant contenir une nouvelle balise qui va nous permettre d'inclure le component correspondant au paths que nous avons précisés.
Pour ce faire on utilisera la balise router-outlet, nous en profiterons pour ajouter une barre de navigation :

```html
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="#">Devices Manager</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-on"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarNavDropdown">
    <ul class="navbar-nav">
      <li class="nav-item">
        <a class="nav-link" routerLink="/">Devices</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" routerLink="/auth">Connexion</a>
      </li>
    </ul>
  </div>
</nav>
<div class="container-fluid">
  <div class="container" style="margin-top: 50px;">
    <div class="row">
      <div class="col-12">
        <router-outlet></router-outlet>
      </div>
    </div>
  </div>
</div>
```

On observera que les liens de navigation n'ont pas d'attribut href mais routerLink, pourquoi ?
En effet si nous laissons les href notre application fonctionnera mais en recharger la page à chaque changement de destination.
Ce que nous ne souhaitons pas. Nous allons donc les remplacer par routerLink qui simulera une navigation sans recharger la page, c'est beaucoup plus rapide et sympa 😃

## Ajouter un service d'authentification

Nous allons également ajouter un service d'authentification à notre application histoire que celui-ci puisse être joignable par tous les components.
Ce service ce nommera AuthService et aura comme fichier auth.service.ts.
Nous allons lui définir une propriété contenant le status de l'authentification qui par défaut sera false.
Nous inclurons deux méthodes, une pour se connecter avec un petit délais pour simuler une requête et l'autre pour se déconnecter.
Voici le contenu de notre fichier :

```typescript
export class AuthService {


    isAuth: boolean = false;


    signIn() {
        return new Promise(
            (resolve,reject) => {
                setTimeout(
                    () => {
                        this.isAuth = true;
                        resolve(true);
                    }, 2000
                );
            }
        );
    }


    signOut() {
        this.isAuth = false;
    }
}
```

Nous allons maintenant ajouter ce service au component auth qui nous servira comme vous l'avez compris de page de connexion :
Dans le fichier auth.component.ts :

```typescript
import { Component, OnInit } from '@angular/core';
import { AuthService } from '../services/auth.service';


@Component({
  selector: 'app-auth',
  templateUrl: './auth.component.html',
  styleUrls: ['./auth.component.scss']
})
export class AuthComponent implements OnInit {


  authStatus: boolean;


  constructor(private AuthService: AuthService) { 
    this.authStatus = AuthService.isAuth;
 }


  signIn() {
    this.AuthService.signIn().then(
      () => {
        this.authStatus = true;
        console.log('test')
      }
    );
  }


  signOut() {
    this.AuthService.signOut();
    this.authStatus = false;
  }


  ngOnInit() {
  }


}

```

Je pense que ce fichier vous rappel les chapitres précédents. Si ce n'est pas le cas retournez y je ne l’expliquerai pas en détail sauf une chose :
Pour mettre à jour sa propriété authStatus nous utilisons la méthode then() qui est une méthode de l'objet Promise pensez donc bien à créer une méthode qui retourne cette objet comme nous l'avons fait.

Dans le HTML de notre component :

```html
<div class="col-6 offset-3 text-center">
  <h1>Connexion utilisateur</h1>
</div>
<div class="text-center">
  <button class="btn btn-primary" (click)="signIn()" *ngIf="!authStatus">Connexion</button>
  <button class="btn btn-danger" (click)="signOut()" *ngIf="authStatus">Déconnexion</button>
</div>
```
Nous avons maintenant nos fichier nous allons aussi ajouter à notre component devices les boutons lui permettant de tout allumer que nous avons supprimer dans le componentprincipal.

```html
<div class="row">
  <div class="col-12 text-center mt-3 mb-3 centered">
    <button class="btn btn-danger mr-3" (click)="onSwitchOffAll()">Tout éteindre</button>
    <button class="btn btn-success" (click)="onSwitchOnAll()">Tout allumer</button>
  </div>
</div>
```

Vous pouvez maintenant actualiser votre application et voir le résultat de votre travail 😃


## Redirection et erreur 404

Comme avec tout site web il va nous falloir gérer les erreurs de navigation dans les url.
Pour cela nous allons préciser quelques paths supplémentaire à notre app-routing.module.ts.
Le path ** devra ce trouver en dernière position, si aucun liens précédent n'a était trouvé il renverra à la page qu'on lui indique.
Ici nous allons donc créer dans un premier temps un component d'erreur 404 dont nous compléterons uniquement le code HTML :

```html
<h2>Erreur 404</h2>
<p>La page que vous recherchez n'existe pas ou l'url n'a pas été trouvée.</p>
```

 Ensuite comme convenu nous allons modifier nos paths en conséquence :

```typescript
const routes: Routes = [
  {
    path: 'auth',
    component: AuthComponent
  },
  {
    path: '',
    component: DevicesComponent
  },
  {
    path: 'devices',
    component: DevicesComponent
  },
  {
    path: 'not-found',
    component: NotFoundComponent
  },
  {
    path: '**',
    redirectTo: '/not-found'
  }
];
```

Voilà vous pouvez dorénavant mettre les url à l'épreuve des chemins inexistants 😃


## Passage de paramètres

Imaginons maintenant que vous souhaitiez créer un component qui affiche pour chacun de vos appareil son status.
Pour ce faire nous allons pouvoir utiliser le passage de paramètre. Plusieurs étape sont nécessaire pour le mettre en place.
Pour commencer créons notre component single-device qui sera chargé de n'afficher qu'un seul et unique device.

Entre la commande que vous connaissez déjà et rendez-vous dans votre component et plus exactement dans sa partie HTML :

```html
<ul class="list-group">
  <li class="list-group-item">Nom de l'appreil : {{ device.deviceName }}</li>
  <li class="list-group-item">Status actuel : {{ device.deviceStatus }}</li>
</ul>
```

Oui nous allons faire quelque chose de simple 😃
Alors maintenant nous allons devoir modifier notre module de routing pour pouvoir accepter les paramètres.
Par exemple si nous souhaitons utiliser le paramètre id nous allons devoir le placer après la page qui devra le recevoir et précéder de deux points.
Un exemple sera plus parlant, dans notre app-routing.module.ts :

```typescript
  {
    path: 'single-device/:id',
    component: SingleDeviceComponent
  },
```

Maintenant nous allons pouvoir récupérer ce paramètre dans notre module single-device, d'ailleurs allons-y et dans son fichier TypeScript dans les imports :

```typescript
import { Component, OnInit } from '@angular/core';
import { DevicesService } from '../services/devices.service';
import { ActivatedRoute } from '@angular/router';
```

Le module DevicesService est evident, il va nous servir à récupérer les infos sur notre appareil.
Le module ActivatedRoute permet lui deux choses, récupérer le path courant ainsi que son paramètre grace à sa méthode snapshot.params.
Il est également possible d'utiliser les paramètres classiques classique et de les récupérer dans snapshot.queryParams.
Voici comment créer notre constructeur et notre classe :

```typescript
export class SingleDeviceComponent implements OnInit {


  deviceId: number;
  device: object;


  constructor(private DevicesService: DevicesService,
              private Route: ActivatedRoute) {
    this.deviceId = Route.snapshot.params['id'];
    this.device = DevicesService.getDeviceById(this.deviceId);
    console.log(Route);
  }


  ngOnInit() {
  }


}
```

Nous allons maintenant devoir créer la méthode getDeviceById qui renverra l'id correspondant au paramètre dans le service DevicesService :

```typescript
    getDeviceById(id: number): object {
        const device = this.devices.find(
            (device) => {
                return device.id == id;
            }
        )
        return device;
    }
```

Nous utiliserons la méthode find qui peut s'utiliser sur un array. Vous pouvez également utiliser une condition dans une boucle for.
La méthode find est à connaitre car elle peut souvent nous simplifier la tâche.

Maintenant votre application est prête pour afficher ses appareils un à un 😃



## Les Guards


Les guards peuvent se traduire littéralement par les gardiens et nous allons comprendre pourquoi, ça à tous sont sens.
Les guards sont des services Angular présents nativement dans le frameworks il en existe plusieurs dont :


* `CanActivate` : Permet d'autoriser ou non une route.
* `CanActivateChild` : Permet d'autoriser ou non les enfants d'une route.
* `CanLoad` :  Permet ou non de charger certain éléments d'une route.
* `CanDesactivate` : Permet d'autoriser ou non un changement de route depuis l'application, non depuis l'url du navigateur.


Le principal Guard que vous allez utiliser dans la quasi totalité de vos application est CanActivate qui va vous permettre de gérer vos authentifications.
C'est d'ailleurs de celui-ci dont je vous propose de faire l'expérimentation avec notre système d'authentification précédemment créé.


On pourrait gérer les accès de chaque composant en utilisant une fonction spécifique à chaque création d'un component avec la fonction ngOnInit().
Cependant ce n'est pas la meilleure solution car si vous avez de nombreux composant à protéger cela vous fera répéter du code.
L'autre désavantage et surement le plus important et que le fait d'utiliser un système d'authentification, décentralisé multiplie les risque d'erreur et donc de failles.


CanActivate et une interface qui implémente une fonction CanActivate qui prend en paramètre deux classes d'Angular :

* `ActivatedRouteSnapshot` : Elle va nous permettre de préciser la route à laquelle notre utilisateur tente d'accéder.
* `RouterStateSnapshot` : Elle limite l'état dans lequel peut être la demande d'authentification.


La méthode que nous allons devoir créer est canActivate ne peut prendre que trois options différentes pour notre valeur de retour :
Un booléen, un objet Promise retournant un booléen,  un observable retournant également un résultat booléen.
Nous allons donc créer un service complémentaire pour ce guard que nous appellerons auth-guard-service.
Et dans la classe de ce service nous allons implémenter l'interface que nous venons de décrire :

```typescript
export class AuthGuard implements CanActivate {


    constructor(private AuthService: AuthService, private Router: Router) {};


    canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {

        if (this.AuthService.isAuth === true) {

            return true;

        } else {

            this.Router.navigate(['auth']);
            return false;
        }
    }
}
```

Cependant nous injectons un service dans un autre service et dans ce cas précis nous allons avoir besoin du décorateur @Injectable() avant notre classe.
Vous remarquerez que j'ai choisi de rediriger l'utilisateur vers la page d'authentification plutôt que de renvoyer un simple false et de bloquer la page cela est plus ludique.
Il nous faudra également ajouter l'ensemble des dépendance auxquelles la classe fait référence on ajoutera en haut de page :

```typescript
import { CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot, Router } from '@angular/router'
import { Observable } from 'rxjs';
import { Injectable } from '@angular/core';
import { AuthService } from './auth.service';

@Injectable()
```

Nous avons maintenant une méthode prête à l'emploi et vous allez voir qu'elle va être simple à utiliser.
Rendons nous dans notre module app-routing.module.ts qui contient l'ensemble des paths que nous avons définis précédemment.
Nous allons préciser pour chacun de ces paths lesquels doivent être contrôlés avec ce système que nous avons créé.
Pour cela il nous suffira d'ajouter CanActivate: [AuthGuardService] juste avant le nom du component.
Bien sûr n'oubliez pas d'inclure les dépendances dans vos imports 😃
Je vous propose que nous bloquions l'accès aux page devices et single-devices :

```typescript
  {
    path: '',
    canActivate: [AuthGuard],
    component: DevicesComponent
  },
  {
    path: 'devices',
    canActivate: [AuthGuard],
    component: DevicesComponent
  },
  {
    path: 'single-device/:id',
    canActivate: [AuthGuard],
    component: SingleDeviceComponent
  },
```

N'oubliez pas d'ajouter AuthGuard aux providers sans quoi votre service ne peut fonctionner.
