# CHAPITRE IX : LES FORMULAIRES RÉACTIFS

## Un peu de théorie

Les formulaires sont choses communes et plus ça va plus nous devons en réaliser avec des contrôles de plus en plus poussés.
Avec Angular il existe deux méthodes distinctes permettant de gérer les formulaires.

* Les formulaires réactifs
* Les formulaires template driven 

Les deux types de formulaires on leurs intérêts, en général il est admis que les formulaires réactifs sont :

* Plus sécurisé
* Plus performants
* Plus complexes

Cependant c'est la méthode que j'ai choisie de représenter ici car au final si vous connaissez les deux c'est celle-ci qui sera le plus modulable.
Voici un tableau comparatif des deux méthodes pour que vous puissiez mieux voir les différences :

&nbsp;			|     Reactive    											| Template driven
--------------- | --------------------------------------------------------- | ---------------
Setup 			|	More explicit, created in component class				|	Less explicit, created by directives
Data model		|	Structured												|	Unstructured
Predictability	|	Synchronous												|	Asynchronous
Form validation	|	Functions												|	Directives
Mutability		|	Immutable												|	Mutable
Scalability		|	Low-level API access									|	Abstraction on top of APIs


#### Les fondations communes des deux méthodes

Quelque soit la méthode que vous choisissez d'implémenter elles disposes de clocks communs qui sont les suivant :

* `FormControl` : Permet de tracker et de valider la valeur d'un champ unique.
* `FormGroup` : PPermet de tracker et de valider une même valeur pour une collection de champ.
* `FormArray` : Permet de tracker et de valider les même valeurs et statut pour un tableau de champ.
* `ControlValueAccessor` : Permet de créer un pont entre les instances de FormControl et les éléments du DOM.

#### Les différences

La plus grosse différence ce fait entre les manières dont sont liées les deux méthodes.
La manière réactive utilise un observable qui lie les valeur du component et du DOM de façon immutable.
Le template driven reçoit dans un premier temps les informations du DOM et met à jour ces informations dans le component.
 
##  Créer un formulaire réactif


#### Première étape

Dans cette partie du cours nous allons apprendre à utiliser un formulaire réactif au travaers d'un formulaire progressivement complexifié.
Dans un premier temps nous allons ajouter le module `ReactiveFormControl` dans notre component qui gère les module de notre app :

```TypeScript
import { FormModule, ReactiveFormsModule } from '@angular/forms';
```

Et dans l'objet des imports :

```TypeScript
  imports: [
    BrowserModule,
    AppRoutingModule,
    FormsModule,
    ReactiveFormsModule
  ],
```

Ensuite nous allons générer un component que nous appelerons profil-editor :

`ng generate component profil-ditor`

Nous voila maintenant prêt à créer un petit formulaire.
Mais avant nous allons configurer le router pour que celui-ci nous envoi sur ce component à l'adresse : /profil
Une fois que vous avez effectuer cette modifications importer le module `FormControl` depuis `@angular/forms` dans votre component nouvellement créé.
Nous allons devoir ensuite créer un objet qui correspondera à notre premier input que nous appelerons firstname.
Lors de l'instanciation d'une classe `FormControl` le premier paramètre correspond à la valeur initial du champ.
Ici elle sera vide, voici maintenant notre fichier ts :

```TypeScript
import { Component, OnInit } from '@angular/core';
import { FormControl } from '@angular/forms';

@Component({
  selector: 'app-profil-editor',
  templateUrl: './profil-editor.component.html',
  styleUrls: ['./profil-editor.component.scss']
})
export class ProfilEditorComponent implements OnInit {

  firstname: FormControl;

  constructor() { }

  ngOnInit() {
    this.firstname = new FormControl('');
  }

}
```

Maintenant nous allons créer notre template HTML avec un input text firstname qui sera lié à ce `FormControl` :

```html
<div class="col-6 offset-3">
  <form>
    <div class="form-group">
      <label for="firstname">Prénom</label>
      <input class="form-control" type="text" [formControl]="firstname">
    </div>
  </form>
</div>
```

Voilà nos deux valeurs sont liées, si vous souhaitez le vérifier rien de plus simple, il suffit d'utiliser une string interpolation dans un paragraphe portant le valeur de firstname :

```html

    <p>
      {{ firstname.value }}
    </p>
```

A chaque entré dans le champs input le paragraph se complète =).
Maintenant compliquons un peu la chose en ajoutant une méthode capable de changer la valeur de notre input.
Pour cela nous allons ajouter un bouton dans notre code html :

```html
    <button (click)="onUpdate()" class="btn btn-primary">Mettre à jour</button>
```

Maintenant nous devons créer la méthode dans notre component.
Celle-ci utilisera une autre méthode de la classe `FormControl` appelée `setValue` elle prend en paramètre la valeur du champ ;

```typescript
  onUpdate(): void {
    this.firstname.setValue('Marc');
  }
```

Maintenant vous pouvez retourner dans votre component et essayer cette méthode.

#### Créer un groupe de FormControl

Nous avons créer un simple input mais dans l'avenir nos formulaires seront bien plus complexes.
Nous allons donc devoir regrouper nos champs du formulaires dans un groupe appartenant à la classe `FormGroup`.
Pour cela même pincipe nous devons importer cette classe toujours depuis `@angular/forms`
Ensuite nous allons créer notre FormGroup depuis notre component dans le fichier type script :

```typescript
import { Component, OnInit } from '@angular/core';
import { FormControl, FormGroup } from '@angular/forms';

@Component({
  selector: 'app-profil-editor',
  templateUrl: './profil-editor.component.html',
  styleUrls: ['./profil-editor.component.scss']
})
export class ProfilEditorComponent implements OnInit {

  profilForm: FormGroup;

  constructor() { }

  ngOnInit() {
    this.profilForm = new FormGroup({
      firstname: new FormControl('firstname'),
      lastname: new FormControl(''),
      address: new FormControl('')
    });
  }


}
```
Maintenant si nous souhaitons accéder à la valeur d'un élément il faudra utiliser la syntaxe suivante `profilForm.value.nomDeLaPropriété`.
Mais avant cela nous allons modifier notre template html. La propriété `formGroup` devrai être appliquée à la balise `form` en respectant la  syntaxe property binding puis les propriétés suivant auront simplement besoin d'être indiqué comme une simple propriété HTML qui comportera le nom `formControlName="nomDuChamp"`. 
Voici donc notre template HTML modifié :

```html
<div class="col-6 offset-3">
  <form [formGroup]="profilForm">
    <div class="form-group">
      <label for="firstname">Prénom</label>
      <input class="form-control" type="text" formControlName="firstname">
    </div>
    <div class="form-group">
      <label for="lastname">Nom de famille</label>
      <input class="form-control" type="text" formControlName="lastname">
    </div>
    <div class="form-group">
      <label for="address">Adresse</label>
      <input class="form-control" type="text" formControlName="address">
    </div>
    <p>
      {{ profilForm.value.address }}
    </p>
    <button (click)="onUpdate()" class="btn btn-primary">Mettre à jour</button>
  </form>
</div>
```

Que se passe t'il pour notre méthode `onUpdate()` ? On serait tenté de penser que `this.profilForm.value = 'Marc'` pourait fonctionner mais ce n'est pas le cas pour cela nous allons devoir réutiliser `setValue()` et l'appliquer sur l'ensemble du formulaire :

```typescript
  onUpdate() {
    this.profilForm.setValue({
      firstname: 'Paul',
      lastname: this.profilForm.value.lastname,
      address: this.profilForm.value.address,
    });
  }
```

De cette façon seulement la propriété firstname changera de valeur mais ce n'est vraiment pas pratique !
Imaginez seulement un formulaire long de 40 inputs vous allez devenir dingue !
Alors nous avons une solution (j'ai l'impression de vendre un micro-onde là) ne vous inquiétez pas.
Pour cela nous utiliserons `patchValue()` qui est une méthode qui fera le travail à notre place.
Elle fonctionne exactement comme `setValue()` à l'exeption qu'elle peut ne prendre que les valeur à changer.

```typescript
  onUpdate() {
    this.profilForm.patchValue({
      firstname: 'Paul'
    });
  }
```

Voilà qui est beaucoup plus pratique tout de même ! =)
Je voulais aussi vous présenter quelque chose qui pourrait bien vous être utile, la possibilité de groupé des instances de `FormControl` dans un `FormControl` lui même.
L'exemple parfait est celui de l'adresse alors créons un champ adresse qui comportera 3 champs dont ville, rue et numéro.
Dans notre code HTML :

```html
<div class="col-6 offset-3">
  <form [formGroup]="profilForm">
    <div class="form-group">
      <label for="firstname">Prénom</label>
      <input class="form-control" type="text" formControlName="firstname">
    </div>
    <div class="form-group">
      <label for="lastname">Nom de famille</label>
      <input class="form-control" type="text" formControlName="lastname">
    </div>
    <h3>Adresse</h3>
    <div formGroupName="address">
          <div class="form-group">
            <label for="city">Ville</label>
            <input class="form-control" type="text" formControlName="city">
          </div>
          <div class="form-group">
            <label for="postalcode">Code postal</label>
            <input class="form-control" type="number" formControlName="postalcode">
          </div>
          <div class="form-group">
            <label for="country">Country</label>
            <input class="form-control" type="text" formControlName="country">
          </div>
    </div>
    <p>
      {{ profilForm.value.address.city }}
    </p>
    <button (click)="onUpdate()" class="btn btn-primary">Mettre à jour</button>
  </form>
</div>
```

Comme vous pouvez le constater la principale modification se fait sur la propriété `formGroupName` qui n'existait pas avant.
Les enfants de cette balise porte les même propriété `formControlName` quand dans un cas simple comme précédemment.
Mais que ce passe-t'il dans notre component ? Rien de bien compliqué !
Il nous suffit de créer un nouvel objet `FormGroup` dans notre objet parent :

```typescript
  ngOnInit() {
    this.profilForm = new FormGroup({
      firstname: new FormControl(''),
      lastname: new FormControl(''),
      address: new FormGroup({
        city: new FormControl(''),
        postalcode: new FormControl(''),
        country: new FormControl(''),
      })
    });
  }
```

Finalement on n'a là qu'un objet dans un autre objet, rien de bien époustouflant =)
Du côté de notre méthode `onUpdate()` il en va de même :

```typescript
  onUpdate() {
    this.profilForm.patchValue({
      firstname: 'Paul',
      address: {
        city: 'france'
      }
    });
```

Simple comme bonjour, cette méthode changera la valeur du champ city et celle du champ firstname.

## Contrôler la validité des données

Comme tout formulaire qui se respect une validation des données doit être effectué côté client ET côté serveur.
En effet la vérification côté client permet à l'utilisateur de renseigner correctement le's données et ainsi d'éviter les erreurs.
Cependant cette méthode améliorant considérablement l'expérience utilisateur ne peut être effectué pour remplacer une vérification côté serveur.
En effet tous ce qui vient du côté client peut être modifié et ainsi cela mettrait en péril la sécurité et l'intégrité des données de l'application.

Bref après ce cours rappel je vous propose de voir comment la vérification des données des formulaires se passe avec Angular.

#### Utiliser la classe Validators

Dans un premier temps nous allons importer la classe `validators` au sein de notre component.
Celle-ci se retrouve également dans le dossier `@angular/forms`.

```TypeScript
import { Validators } from '@angular/forms'
```

Ensuite nous allons demander à nos différents `FormControl` d'effectuer ou non une vérification sur l'intégrité de leur valeur.
Pour cela il suffit de d'utilisé la classe `Validators` en second argument de la classe `FormControl` de l'élément.
Par exemple si nous souhaitons que notre formulaire soit invalidé si le champs n'est pas rempli alors :

* Il faudra dans un premier temps ajouter l'attribut `required` dans le template HTML.
* Dans un second temps ajouter la classe `Validators` puis sa propriété `required`.

Nous allons utiliser cette propriété et ce même attribut pour notre champ firstname :

```TypeScript
    this.profilForm = new FormGroup({
      firstname: new FormControl('', Validators.required),
      lastname: new FormControl(''),
      address: new FormGroup({
        city: new FormControl(''),
        postalcode: new FormControl(''),
        country: new FormControl(''),
      })
    });
  }
```

Ensuite nous allons ajouter l'attribut `required` sur notre input et enfin modifier le bouton en bas de page qui appellera une nouvelle methode appelé `onSubmit()`  :

```html
<div class="col-6 offset-3">
  <form [formGroup]="profilForm">
    <div class="form-group">
      <label for="firstname">Prénom</label>
      <input class="form-control" type="text" formControlName="firstname" required>
    </div>
    <div class="form-group">
      <label for="lastname">Nom de famille</label>
      <input class="form-control" type="text" formControlName="lastname">
    </div>
    <h3>Adresse</h3>
    <div formGroupName="address">
          <div class="form-group">
            <label for="city">Ville</label>
            <input class="form-control" type="text" formControlName="city">
          </div>
          <div class="form-group">
            <label for="postalcode">Code postal</label>
            <input class="form-control" type="number" formControlName="postalcode">
          </div>
          <div class="form-group">
            <label for="country">Country</label>
            <input class="form-control" type="text" formControlName="country">
          </div>
    </div>
    <p>
      {{ profilForm.value.address.city }}
    </p>
    <button (click)="onSubmit()" class="btn btn-primary">Envoyer</button>
  </form>
</div>
```

Et la méthode onSubmit() va être chargée de nouys afficher quelque chose d'important, le status de notre formulaire, c'est à dire sa validité ou non :

```TypeScript
  onSubmit() {
    console.log(this.profilForm.status)
  }
```

Si vous tentez cliquez sur le bouton sans remplir le champ sur lequel s'applique notre attribut et notre propriété required alors celui-ci `INVALID` retourne , dans le cas contraire c'est `VALID` qui sera retourné.
Sachez maintenant que vous pouvez faire de nombreuses vérifications de cette façon !
En effet HTML5 et Angular implémentent :

* required : La valeur est requise
* pattern : Permet d'inserrer une regexp et de verifier si la valeur entrée y correspond
* minlength : Attribut une longueur minimum
* maxlength : attribut une longueur maximum

Plutot que de beaucoup parler pour ne finalement pas dire grand chose autant vous montrer que vous pouvez inclure plusieur validateurs dans un même tableau :

```TypeScript
export class ProfilEditorComponent implements OnInit {

  profilForm: FormGroup;
  firstnameRegexp: string;

  constructor() { }

  ngOnInit() {
    this.firstnameRegexp = '[a-zA-Zéèçà]{4,}';
    this.profilForm = new FormGroup({
      firstname: new FormControl('', [
        Validators.required,
        Validators.pattern(this.firstnameRegexp)
      ]),
      lastname: new FormControl(''),
      address: new FormGroup({
        city: new FormControl(''),
        postalcode: new FormControl(''),
        country: new FormControl(''),
      })
    });
  }
```

Vous pouvez d'ailleurs constater que pour éviter les erreurs entre le pattern HTML et le pattern du component j'ai décidé de les stocker dans une propriété de la classe.

```html
      <input class="form-control" type="text" formControlName="firstname" required pattern="{{ firstnameRegexp }}">
```

**Attention aux RegExp**

Je vous conseille d'utiliser la meme valeur pour l'élément HTML et la methode pattern mais cela va vous demander de créer une string car la RegExp ne pourra être correctement transmise au HTML dans la forme souhaitée.
Il est aussi bon de savoir que le match pattern passé à la méthode pattern dans le cas d'une string aura ajouter ^ et $ respectivement au début et à la fin de la chaîne.

Maintenant notre formulaire est valide seulement si firstname contient plus de 3 lettre et ne contient que les caractère présents dans l'expression régulière.
Vous pouvez également créer vos propres fonctions de validations, un exemple vous est donné à cette adresse dans la documentation d'Angular : 
[Créer sa propre fonction de validation](https://angular.io/guide/form-validation#custom-validators)

#### Informer l'utilisateur

Pour informer l'utilisateur Angular emploi des classes CSS inclus directement sur les balises de champs de notre formulaire. On peut y retrouver ng-valid ng-invalid qui respectivement indique si le champ est bien renseigné ou non mais pour créer réellement des formulaires dynamique il en existe d'autres?
Ce que je vous propose c'est de commencer par voir en détail quelles classes sont appliquées et sous quelles conditions :

* `ng-valid` : Indique que le champ contient des informations valides.
* `ng-invalid` : Indique que les informations du champ ne sont pas valides.
* `ng-pending` : Indique que le champ est en attente.
* `ng-pristine` : Indique que le champ est vierge.
* `ng-dirty` : Indique que le champ a été modifier.
* `ng-untouched` : Indique que le champ n'a pas reçu de focus.
* `ng-touched` : Indique que le champ a reçu le focus puis l'utilisateur en est sorti.
* `ng-submitted` : Indique que le formulaire a été envoyé.

Les classes si dessus s'appliquent aux champ de notre formulaire mais également directement sur la balise form.
Maintenant que vous avez ces éléments en votre connaissance vous allez pouvoir appliquer de réels effets dynamiques dans vos formulaires.
Par exemple si nous voulons :

* Que les bordures s'affichent en rouges si le champ est invalide et qu'il n'est plus vierge.
* Que les éléments dont l'utilisateur a tenté ou a saisi quelque chose et que le champ soit valide s'affiche en vert.

Nous pourrions entrer les classe suivante dans la feuille de style de notre component :

```scss
form .ng-invalid:not(.ng-pristine) {
  border-color: red;
}

form .ng-valid.ng-touched {
  border-color: green;
}
```

Maintenant vous avez le comportement attendu.
Il est également possible d'accéder aux valeurs des classes CSS via les propriété de chaque champ dans notre objet `profilForm`. Pour cela il suffit d'utiliser l'objet `controls`.
Ajoutons un message dans notre template HTML qui ne s'affichera que si l'utilisateur à modifier le champ est que celui-ci est invalide :

```html
    <div class="form-group">
      <label for="firstname">Prénom</label>
      <input class="form-control" type="text" formControlName="firstname" required pattern="{{ firstnameRegexp }}">
      <div class="alert alert-danger" *ngIf="profilForm.controls.firstname.touched && profilForm.controls.firstname.status == 'INVALID'">
        Votre prénom doit comporter minimum 4 lettres et ne être composé de chiffres ou caractères spéciaux
      </div>
    </div>
```

Notre div ne s'affichera qu'uniquement suivant ce que nous venons de dénir dans la directive `*ngIf`.
Vous savez maintenant vous servir des formulaires réactifs pour créer de véritables formulaires. =)

#### Best pratices

Dorénavant vous avez les connaissances nécessaires pour développer votre formulaire mais cependant comment créer un formulaire en respectant des règles qui vous permettrons d'éviter au maximum les erreurs et produire un code lisible et maintenable.
Pour cela je souhaite vous donner quelques conseils d'implémentation de vos formulaires :

* Attribuer toujours une classe à un formulaire, ceci vous permettra d'éviter de poluer votre component.
* Cette classe devra être un model qu'il est communement admis d'ajouter au dossier models (comme pour les services).
* Les méthodes et propriétés doivent être typé et être bien sûr dans la classe correspondante à votre formulaire.
* Un formulaire === un component.

Je vous conseille également de regrouper l'ensemble de vos formulaires dans un dossier forms mais ceci n'est pas obligatoire.
Vous remarquerez que pour un soucis de lisibilité je n'ai pas appliqué ces différentes règles dans notre application car j'ai jugé que vous seriez en capacité d'appliquer ces règles en aval de ce cours.