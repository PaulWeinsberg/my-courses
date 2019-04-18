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

Comme tous les modules d'Angular celui-ci devra être importé dans le module principal ou racine, c'est à dire dans le module `app.module.ts` et plus précisement dans ces imports du décorateur `@NgModule`. Ainsi il est possible d'optimiser l'organisation et de séparer clairement les différentes parties de votre applications. Cela à également pour effet d'alléger les éléments à charger pour chacunes de ces parties. Il est également possible de définir des routes spécifique à ce module dans un fichier. Cela fonctionne absolument comme `app-routing.module.ts` à la différence que la méthode utiliser pour `RouterModule` n'est pas `forRoute` mais `forChild` car il s'agit de routes enfants du module nouvellement créé.