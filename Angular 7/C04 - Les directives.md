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




