# CHAPITRE XIII : RXJS ET LES OBSERVABLES


## Introduction

Pour réagir à des données ou à des événements de manières asynchrone il y a eu plusieurs techniques qui ont étaient développées.
Nous avons vu arrivé dans un premier temps un système de Callback puis dans un second les Promise que nous avont précédemment utilisé.
Avec l'API RxJS nous allons pouvoir utiliser de nombreuses fonctionnalitées asynchrone pour gérer notre application de manière réactive.
La méthode que propose RxJS est celle de la gestion par observables.

#### Mais pourquoi est-ce si important ?

Vous êtes entrainde développer une application dont aucuns rechargement de page ne sera nécessaire en temps normal.
Imaginez que vous ayes un tas de requête et de fonctionnalités à gérer sans pouvoir mettre directement à jour des données via l'url.
Il peut y avoir des erreurs avec le serveur ou tout autre problèmatiques qu'il serait bien plus facile de gérer avec un rechargement de données par URL.
C'est là que prennent leur sens les observables.

## Les observables

#### Qu'est-ce qu'un observable

Très simplement un observable est un objet emettant des informations auxquels nous pouvons réagir.
Ces informations peuvent être multiples on pourrait imaginer un formulaire, une communication avec le serveur, un chargement de fichier et sa progression etc...

RxJS nous permet d'inclure ses observables dans notre projet Angular de la même façon que nous avont importer différent module.
RxJS est véritablement un outil complet et parfois un peu complexe à maitriser c'est pourquoi ne ne verrons qu'une infime partie ici.
Je vous conseille de faire un tour sur sa documentation qui est plutôt bien fournie.

Pour chaque observable on associe un observer c'est à dire un bloc de code qui sera exécuté à chaque fois que l'observable fournit une information.
Un Observable fournit trois types d'informations :

* Des données
* Une erreur
* Une fin

Tout observer peut donc avoir trois fonctions pour réagir à chaque type d'informations.
Pour avoir un cas concret nous allons créer un Observable dans notre AppComponent qui est la racine de notre site.
Nous allons donc inclure les modules qui vont nous être utiles :

Le module `interval` de rxjs qui est un observable :

```TypeScript
import { interval } from 'rxjs';
```

#### Créer un observable

Puis nous allons créer notre première observable qui sera donc un interval de RxJS à entrer dans NgOnInit() :

```TypeScript
    const timer = interval(1000);
    const observer = {
      next: (value: number) => {
        console.log(value)
      },
      error: (err) => {
        console.error(err);
      },
      complete: () => {

      }
    }
    timer.subscribe(observer);
```

Alors nous allons découper ce bout de code :

Dans un premier temps nous créons une constante ou une variable qui va contenir notre interval.
Ensuite nous créons un objet litéral nommé observer qui comme nous l'avons vu doit contenir trois propriétés :

* next: A chaque fois que notre observable revérra une donnée c'est cette propriété qui sera appellée
* error: En cas d'erreur le contenu de cette propropriété sera appellée
* complete: on peut faire appelle à cette propriété une fois le travail de l'observable terminé.

Dans chacune des propriété on définie une fonction anonyme qui sera exécuter au moment ou la propriété sera appelée.
Attention néanmoins ! la fonction complete n'arrête pas l'éxécution de l'observable.
Pour cela nous allons devoir procéder autrement et assigner la souscription à l'observer dans une variable.

Pour cela vous devez importer la Classe correspondante aux souscription appelée Subscription :
```TypeScript
import { interval, Subscription } from 'rxjs';
```

Puis dans notre classe la propriété suivante :

```TypeScript
timerSubscription: Subscription;
```

Enfin il nous faut assigner la souscription à l'observable :

```TypeScript
this.timerSubscription = timer.subscribe(observer);
```

La vous ne voyez pas encore l'intérêt d'avoir effectué cette action mais vous allez vite comprendre.
En effet pour mettre fin à l'observable il nous faut appeler une méthode nommée unsubscrribe().
Pour ce faire si nous n'avons pas créer de variable associée a l'observable ça risque d'être compliqué !
Mais rassurez-vous nous venons de le faire, nous allons limer le timer à 5 secondes puis nous allons donc ajouter un message de fin à notre propriété complete puis elle appelera unsubscribe().

```TypeScript
    const timer = interval(1000);
    const observer = {
      next: (value: number) => {
        console.log(value);
        (value === 5) ? observer.complete():null;
      },
      error: (err) => {
        console.error(err);
      },
      complete: () => {
        console.log('Travail terminé !');
        this.timerSubscription.unsubscribe();
      }
    }
    this.timerSubscription = timer.subscribe(observer);
  }
```

Voilà qui est fonctionnel vous savez maintenant comment gérer un observable !
Sachez qu'il est possible de créer vos propre observables.
Nous allons justement recréer un timer et en profiter pour revoir les bases de nos cheres closures =)

```TypeScript
    const timer = new Observable(
      (observer) => {
        function counter() {
          let a:number = 0;
          return ():number => {
            return a++
          }
        }
        let b = counter();
        setInterval(
          ():void => {
            observer.next(b());
          },1000
        );
      }
    );
    const observer = {
      next: (value) => {
        console.log(value);
      },
      error: (err) => {
        console.error(err);
      },
      complete: () => {
        console.log('Travail terminé !');
      }
    }
    timer.subscribe(observer);
```

Voilà de prime abord notre observable fonctionne de la même façon que l'interval RxJS.
Cependant notre code ne gère ni les erreurs ni la fin de notre interval.
Ce qu'il faudrait implémenter mais je vous laisse lire la documentation d'Angular à ce sujet, elle reste complète.

#### Détruire un observable

Il est fortement recommandé d'implémenter la destruction de l'observable dès que vous commencez à le créer.
En effet, si vous ne détruisez pas votre observable celui ci continuera à exister même s'il n'emet plus d'information à votre observer.
Cette façon de fonctionner peut vous créer bien des ralentissement dans votre application alors comment être sur de détruire vos observables correctement ?
Nous allons utiliser la méthode ngOnDestroy qui permet de gérer les événement liés à la fin de vie d'un component.
Il faudra ajouter la méthode unsubscribe() dans celle-ci, même si vous avez déjà implémenté unsubscribe() ailleur.
Imaginez un timer qui detruit l'observable à partir de 10 mais que l'utilisateur quite le component à 5 ?
Vous voyez il faut prevenir ce type de comportement avec nos observables alors pensez-y.

## Les opérateurs et Pipes

Avec RxJS et les observables nous allons pouvoir utiliser des opérateurs pour modifier nos données emises par notre observable vers notre observer. Imaginez que l'observable nous transmet des données dans un tuyaux et que ce tuyaux comporte des méthodes pour les modifier comme nous lui demandons. 
C'est exactement ce que fais RxJS avec les opérateurs.


Les opérateur sont classés en plusieurs catégories on retrouve les opérateurs de :

* Creation : Permet de déclencher l'envoie de la donnée suivant le ou les opérateurs attribués.
* Combinaison : Permet de combiner plusieurs sources d'envoie de données au sein d'un même observable.
* Transformation : Permet de transformer les données emises par l'observable.
* Filtre : Permet de filtrer les données émise par l'observable.
* Mathématique : Permet d'appliquer des fonction mathématique aux données emises.
* Utilitaires : Permet de nombreuses choses utile... Difficile de trouver une explication pour celui-ci.

Il en existe d'autres mais les principaux dont vous aurez besoin se retrouvent dans 90% des cas dans ces catégories.
Vous vous rappelez le tuyaux dont je vous ai parlé ? 
C'est un pipe ce pipe va donc nous permettre d'insérrer des opérateurs à nos observables.
Pour cela nous allons utiliser la méthode pipe commune à tous les observables, elle contiendra nos opérateurs.
Je vous propose d'utiliser l'opérateur take qui nous permettra d'appeler la méthode complete() une fois le nombres de valeurs renvoyées égales à son paramètre.

Dans un premier temps on importe take depuis nos dépendances :

```TypeScript
import { take } from 'rxjs/operators';
```

Ensuite nous allons ajouter une constante qui contiendra notre timer plus avec le pipe que nous souhaitons.

```TypeScript
    const timer = interval(1000);
    
    const observer = {
      next: (value) => {
        console.log(value);
      },
      error: (err) => {
        console.error(err);
      },
      complete: () => {
        console.log('Travail terminé !');
        this.myObservableSubscription.unsubscribe();
      }
    };

    const myObservable = timer.pipe(
      take(10)
    );

    this.myObservableSubscription = myObservable.subscribe(observer);
```

Le comportement de notre observable est dréravant le suivant :

* Il initie un timer qui renvoie une donnée toutes les secondes
* L'opérateur take vérifie que la valeur est égale à 10
* Si ce n'est pas le cas aucunes données n'est transmise à l'observer
* Si c'est le cas la donnée est transmise et l'observable appelle la méthode complete de l'observer.

Maintenant compliquons un peut les choses en ajoutant deux filtre à notre pipe.

* filter : Qui prend en paramètre une fonction imposant des conditions sur notre valeur retournée
* merge : Qui va nous permettre d'assembler deux observables.

Nous allons donc créer un second observable dont le but sera de nous envoyer les informations d'un événement.
Pour cela nous allons utiliser l'observable fromEvent qu'il nous faudra importer comme interval.
Nous le limiterons à deux itérations maximum. Nons réagirons à un click sur le DOM.

```TypeScript
    const clickDoc = fromEvent(document, 'click');
```

Nous allons maintenant utiliser le pipe filter et le pipe merge :

```TypeScript
    const mySecondObservable = clickDoc.pipe(
      take(2)
    );

    const myObservable = timer.pipe(
      take(10),
      filter(n => (n === 2) || (n === 3) || (typeof n === 'object')),
      merge(mySecondObservable)
    );
```

Voilà le reste de notre code ne change pas.
Maintenant nous avons deux conditions de take qui doivent être complétées pour que nos deux observables appellent la méthodes complete().
Chacun des observables s'arrête une fois take complété mais notre observable qui merge les deux non.
Vous pouvez tester ce fonctionnement en ajoutant cette condition au filtre :

```TypeScript
      filter(n => (n === 2) || (n === 3) || (n === 11) || (typeof n === 'object')),
```

Vous verrez que l'itération 11 ne se produira jamais même si vous attendez éternellement sans cliquer sur le DOM.


#### Promosse ou observable

Vous allez régulièrement vous demander si vous devez utiliser une promesse avec l'objet Promise ou un Observable lorsque vous souhaiterez traiter des données de façon asynchrone, notamment lors de requêtes HTTP.
Alors que choisir et comment ?
Globalement le système des observables fait qu'ils continuent d'envoyer des données tant que la souscription n'est pas annulé. Ils sont donc très pratique dans le cas de la gestion d'un ensemble de requêtes.
Cependant les promesses sont globalement plus simple à implémenter ce qui facilite le travail du développeur et reste largement suffisant en terme de performance dans la cas de requête simple.