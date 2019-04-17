# CHAPITRE IV : LES DIRECTIVES


Les directives sont des instructions que nous pouvons inclure directement dans le DOM, il sera très fréquent de s'en servir lors de la création d'une application Angular.
Lorsque Angular lit votre template HTML et lit une directive il suis tout simplement les instructions que vous précisés et qui sont liées à celle-ci.
Notez qu'il est également possible de créer vos propres directives mais cela va pour le moment au delà de cours.

Il existe deux grands types de directives :

* Les directives structurelles
* Les directives par attributs

Nous allons voir ces deux types ci-dessous.


## Les directives structurelles

Ce sont des directives qui comme leur nom l'indique vont permettre de modifier la structure du DOM.
Dans ce sous-chapitre nous n'en utiliserons que deux, *ngIf et *ngFor qui sont les plus couramment utilisées mais il en existe d'autres.

#### *ngFor

Cette directive va permettre de répéter une balise dans laquelle elle est define suivant le contenu du tableau qui lui sera passé.
Elle fonctionne un peu comme une boucle forEach() en JavaScript à quelques différences prêt.

Prenons notre code HTML dans notre component devices, il serait bien de voir la liste à puce se générer automatiquement non ?
Pour cela on peut utiliser la directive *ngFor directement sur l'objet devicesObject et sur sa propriété devicesName qui est un tableau.

```html
<li *ngFor="let devicesName of devicesObject.devicesName; let i=index" class="list-group-item">Appareil : {{ devicesName }} -- Status : {{ devicesObject.devicesStatus[i] }}</li>
```

On voit que pour l'interpolation sur le status de notre appareil nous pouvons utiliser l'index cependant nous devons directement faire appelle à l'objet devicesObject.
 En effet dans notre boucle devicesName correspond maintenant à devicesObject.devicesName ce qui n'est pas le cas de devicesStatus.

Il est à noté que cette directive fonctionne également sur les objets comme for ... in pour le JavaScript.

#### *ngIf

Encore une directive qui porte bien son nom 😃 Cette fois ci elle nous servira à afficher une balise HTML sous certaines conditions que nous allons lui préciser.
Par exemple si nous souhaitons afficher une petite icone carré indiquant si l’appareil est éteint ou allumé on pourrait la coder en dur et faire ceci dans le HTML devicesComponent :

```html
      <li *ngFor="let devicesName of devicesObject.devicesName; let i=index" class="list-group-item">
        <span *ngIf="devicesObject.devicesStatus[i] === 'Allumé'" style="height: 20px; width: 20px; background-color:lightgreen; display: inline-block"></span>
        <span *ngIf="devicesObject.devicesStatus[i] === 'Eteint'" style="height: 20px; width: 20px; background-color:lightcoral; display: inline-block"></span>
        <span *ngIf="devicesObject.devicesStatus[i] === 'Veille'" style="height: 20px; width: 20px; background-color:lightsalmon; display: inline-block"></span>
        Appareil : {{ devicesName }} -- Status : {{ devicesObject.devicesStatus[i] }}
      </li>
```

Voici maintenant notre petite liste à puce qui nous indique plus clairement si nos appareils sont éteint ou allumés ou en veille 😃


## Les directives par attribut

Les directives par attribut vont nous permettre d'ajouter ou modifier certains attributs.
Il en existe également plusieurs et ici nous allons également nous intéresser au deux plus utilisées :

* `ngStyle` : qui va permettre d'inclure ou non des style spécifiques à notre balise.
* `ngClass` : qui elle permettra d'inclure ou non des classes sous certaines conditions.

#### ngStyle

Imaginons que nous souhaitions ajouter une petite ombre derrière chacune de nos div représentant notre icone allumé, éteint et veille.
On pourrait directement le spécifier dans le CSS mais on peut également le faire dans avec ngStyle même si pour le coup ça ne sera pas très parlant :
ngStyle va fonctionner comme ngModel dont nous nous étions servit plus tôt à la différence qu'elle prendra un objet pour appliquer ses styles sur la balise.
Cette façon de fonctionner peut être très pratique car il est possible de définir un objet pour vos style dans votre TypeScript 😃
Il prendra comme propriétés les style au format JavaScript et les valeurs de la même façon.
Sinon vous pouvez les écrire en dur comme dans le cas ci-dessous :

```html
      <li *ngFor="let devicesName of devicesObject.devicesName; let i=index" class="list-group-item">
        <span [ngStyle]="{boxShadow: '0 0 5px lightgreen'}" *ngIf="devicesObject.devicesStatus[i] === 'Allumé'" style="height: 20px; width: 20px; background-                        color:lightgreen; display: inline-block"></span>
        <span [ngStyle]="{boxShadow: '0 0 5px lightcoral'}" *ngIf="devicesObject.devicesStatus[i] === 'Eteint'" style="height: 20px; width: 20px; background-                        color:lightcoral; display: inline-block"></span>
        <span [ngStyle]="{boxShadow: '0 0 5px lightsalmon'}" *ngIf="devicesObject.devicesStatus[i] === 'Veille'" style="height: 20px; width: 20px; background-                       color:lightsalmon; display: inline-block"></span>
        Appareil : {{ devicesName }} -- Status : {{ devicesObject.devicesStatus[i] }}
      </li>
```


#### ngClass

ngClass va fonctionner différemment mais prend également un objet comme expression.
Cependant cet objet prendra comme propriétés le nom des classes à appliquer et comme valeur true ou false ou toute autre valeur renvoyer un tel résultat.
Par exemple si nous souhaitons utiliser les classes bootstrap pour afficher nos listes d'une couleur spécifique à leur état :

```html
      <li *ngFor="let devicesName of devicesObject.devicesName; let i=index" [ngClass]="{'list-group-item': true,
                                                                                        'list-group-item-success': devicesObject.devicesStatus[i] === 'Allumé',
                                                                                        'list-group-item-danger': devicesObject.devicesStatus[i] === 'Eteint',
                                                                                        'list-group-item-warning': devicesObject.devicesStatus[i] === 'Veille'}">
        Appareil : {{ devicesName }} -- Status : {{ devicesObject.devicesStatus[i] }}
      </li>
```

Mine de rien cela doit vous paraitre beaucoup plus facile qu'au début de notre aventure et si vous regardez bien notre petit élément li contient maintenant :

* Une directive structurelle avec *ngFor
* Une directive par attribut avec ngClass
* Deux String interpolation pour son contenu

Sans compter le fait que notre component devicesComponent fait appelle à une propriété personnalisée de l'élément parent en utilisant le property binding.
Vous êtes enfin de le game Angular et j'espère que ça commence à vous plaire mais nous n'avons pas fini ! Oh non ! 😃

## Créer sa propre directive

Avec Angular vous pouvez également créer vos propres directives. C'est d'ailleurs ce que nous allons faire dans ce chapitre et vous allez voir cela n'a rien de bien compliqué à mettre en oeuvre. Je vous propose de créer une directive qui colorera les bordures des éléments sur lesquels nous passons la souris.

#### La mise en place

Dans un premier temps nous allons organiser notre répertoire app pour ajouter correctement une directive, je vous conseille de suivre ces quelques étapes assez simple :

* Créer dans le dossier app un dossier nommé `directives`, il contiendra l'ensemble de nos futur directives personnalisées.
* Ensuite créer un fichier dans ce dossier nouvellement créé que nous nommerons `get-focus.directive.ts`

Nous sommes fin prêt à démarrer notre aventure.

#### La création de la classe

Nous allons maintenant éditer notre classe `GetFocusDirective` pour cela rendons nous dans notre fichier et ajoutons les lignes suivante :


```typescript
import { Directive } from '@angular/core';

@Directive({
	selector: '[getFocus]'
})
export class GetFocusDirective {
	constructor() {	}
}
```

Nous avons ici les éléments de bases permettant la création d'un directive personnalisée :

* `@Directive` : Ce décorateur prend en paramètre un objet avec la propriété `selector`.
* `selector` : Cette propriété doit être entre crochet et indique la balise à insérrer dans notre template HTML, nous choisirons `getFocus` pour le nom de directive.
* `import { Directive } from...` : Essentiel pour se servire du décorateur `@Directive`.

Avec ces quelques éléments nous allons pouvoir commencer à travailler. La premiere chose que je vous proprose de faire et de colorer nos bordure en bleu de façon non dynamique dans un premier temps. Cela va nous permettre de comprendre le fonctionnement d'une directive lorsqu'elle est liée à un élément du DOM. Lorsque notre template sera initialisé par Angular les directives présentent sur les éléments appelerons les constructeurs des classes correspondantes, nous allons donc utiliser ce constructeur pour y définir notre élément et pouvoir ensuite effectuer des actions sur celui-ci. Pour cela il existe un module appelé `ElementRef` à importer depuis `@angular/core`, tout comme le décorateur précédant, notre classe et dorénavant comme suit :

```typescript
import { Directive, ElementRef } from '@angular/core';

@Directive({
	selector: '[getFocus]'
})
export class GetFocusDirective {

	constructor(private element: ElementRef) { 	}

}
```

Notre classe contient maintenant une propriété `element`, c'est un objet qui contient une propriété `nativeElement` qui fera référence à l'élément du DOM. Nous avons maintenant de quoi créer une méthode permettant de modifier la couleur de nos bordures et pour les exercice nous ne ferons pas du grand design alors j'ai choisi la couleur bleu des variables Bootstrap. Nous appelerons cette méthode `changeColor()` elle prendra en paramètre une string `color` avec la valeur de la  couleur à appliquer. D'ailleur nous appelerons cette méthode avec le construteur pour appliquer notre couleur :

```typescript
	constructor(private element: ElementRef) { 
		this.changeColor('var(--blue)');
	}

	changeColor(color: string) {
		this.element.nativeElement.style.border = '1px solid ' + color;
	}
```

#### La déclaration

Notre directive est prête à être utilisée mais il nous faut la déclarer dans le fichier `app.module.ts` sinon elle sera inutilisable par Angular qui ne la retrouvera pas. Pour cela ouvrons le fichier et ajoutons ax imports la ligne suivante :

```typescript
import { GetFocusDirective } from './directives/get-focus.directive.ts';
```

Puis dans le tableau des déclarations :

```typescript
@NgModule({
  declarations: [
    AppComponent,
    DevicesComponent,
    GetFocusDirective,
```

Maintenant testons notre classe en appliquant la directive `getFocus` dans notre template HTML de notre component devices :

```html
      <li getFocus *ngFor="let devicesName of devicesObject.devicesName; let i=index" [ngClass]="{'list-group-item': true,
                                                                                        'list-group-item-success': devicesObject.devicesStatus[i] === 'Allumé',
                                                                                        'list-group-item-danger': devicesObject.devicesStatus[i] === 'Eteint',
                                                                                        'list-group-item-warning': devicesObject.devicesStatus[i] === 'Veille'}">
        Appareil : {{ devicesName }} -- Status : {{ devicesObject.devicesStatus[i] }}
      </li>
```

On peut voir nos éléments protant la directive getFocus se colorer comme nous le souhations. Vous savez maintenant comment créer des directives et les liée aux éléments de votre choix maintenant allons un peu plus loin en découvrant comment réagir à des événements du DOM.


#### Réagir aux événement du DOM

Angular met à notre disposition un décorateur permettant de faciliter la réaction aux événement du DOM, il s'agit de `@HostListener()`. Il prend en paramètre le nom de l'événement auquel nous souhaitons réagir. Ici ce sera `mouseenter` et `mouseleave` qui indiquent respectivement l'entré et la sortie de la souris sur un élément du DOM. Ce décorateur est à importer depuis `@angular/core` également. La première fonction suivant la déclaration du décorateur sera appelée lorsque l'événement ciblé aura lieu. Voici ce que nous pourrions faire pour coloré nos bordures en bleu uniquement lors du survol de la souris :


```typescript
import { Directive, ElementRef, HostListener } from '@angular/core';

@Directive({
	selector: '[getFocus]'
})
export class GetFocusDirective {

	constructor(private element: ElementRef) { }

	@HostListener('mouseenter')
	onMouseEnter() {
		this.changeColor('var(--blue)')
	}

	@HostListener('mouseleave')
	onMouseleave() {
		this.removeColor();
	}

	changeColor(color: string) {
		this.element.nativeElement.style.border = '1px solid ' + color;
	}

	removeColor() {
		this.element.nativeElement.removeAttribute('style');
	}

}
```
Nous avons maitenant deux méthodes, la première définies une bordure et passe par l'élément style sur notre élément du DOM la seconde supprime simplement cet attribut et donc la couleur précédemment appliquée. On peut voir en haut de notre classe nos deux décourateur faisant chacun appel à des méthodes appelant les précédentes. Allez voir votre page et vous constaterez que l'ensemble fonctionne parfaitement =).

Vous savez maintenant créer vos propres directives.