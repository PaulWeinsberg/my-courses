# CHAPITRE XII : Aller plus loin avec Angular

Dans ce chapitre nous n'aborderons pas avec précision les différentes fonctionnalités présentées. En effet pour réellement maîtriser Angular cela demande de la pratique et donc de l'expérience mais aussi une certain autodidactie. Ici je vous propose principalement de survoller quelques unes de ces fonctionnalités intéressantes.

## Les modules personnalisés

Comme vous avez pu le constater au fur et à mesure du projet notre fichier `app.module.ts` commence à être particulièrement bien rempli, ce qui peut devenir complexe à gérer si l'application évolue. Pour des questions organisationnelles il est parfaitement possible et même recommandé de créer vos sous modules. Par exemple nous pourrions créer un répertoire nommé `devices` qui contiendrait tout les éléments et components liés à la gestion des `devices` de notre app. Dans ces repertoire on créerait un module que nous pourrions nommé `devices.module.ts` ce module devra contenir obligatoirement les éléments suivants pour être fonctionnel :

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

@NgModule({
	imports: [
		CommonModule
	],
	declarations: [

	],
	providers: [

	]
})
export class DevicesModule { }
```

Comme tous les modules d'Angular celui-ci devra être importé dans le module principal ou racine, c'est à dire dans le module `app.module.ts` et plus précisement dans ces imports du décorateur `@NgModule`. Ainsi il est possible d'optimiser l'organisation et de séparer clairement les différentes parties de votre applications. Cela à également pour effet d'alléger les éléments à charger pour chacunes de ces parties. Il est également possible de définir des routes spécifique à ce module dans un fichier. Cela fonctionne absolument comme `app-routing.module.ts` à la différence que la méthode utiliser pour `RouterModule` n'est pas `forRoute` mais `forChild` car il s'agit de routes enfants du module nouvellement créé. Autre point important et peut intuitif il est important de charger les différents modules **en amont** du module racine car sinon celles-ci seront introuvables par celui-ci et renverrons donc une erreur. Ce qui donnerai ceci dans notre module racine pour les imports :

```typescript
  imports: [
	BrowserModule,
	DevicesModule,
    AppRoutingModule
  ],
```


## Déploiement de l'application

#### Pseudo-déploiement

Pour déployer votre application en cours de développement cela n'est pas bien compliqué. Dans un premier temps je vous conseille d'utiliser la commande `ng build --watch` d'angular CLI. L'ensemble des fichiers de votre application se retrouverons dans le dossier `dist` à la racine de votre projet. Il s'agit là d'une compilaiton rapide de votre projet proche de sa forme une fois compiler réellement. Cette compilation est plus rapide mais n'est pas exactement la compilation finale de déploiement. Vous pouvez placer l'ensemble du dossier contenu dans `dist/votre-pojet` à la racine d'un serveur comme Apache en local d'aller sur l'adresse HTTP correspondante pour vérifier le fonctionnement de celle-ci en cours de développement. 

#### Mise en production

Ca y est la première version de vore projet est fin prêt pour le déploiement ! Il ne vous reste qu'à compiler votre projet et pour cela encore une fois rien de bien complexe à effectuer, il vous suffit de taper la commande suivante `ng build --prod` cette commande effectue plus de vérifications que la précédente et compile d'une façon différente, plus poussée. Ainsi une fois la compilation terminée il ne vous reste plus qu'à copier le contenu du repertoire `dist/votre-projet` à l'url que vous souhaitez et sur le serveur de votre choix. =)