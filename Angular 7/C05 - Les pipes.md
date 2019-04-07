# CHAPITRE V : LES PIPES


Non, il n'y aura pas de blague vaseuse dans cette partie mais bien la description de petite outils fort plaisant à utiliser.
Nous parlerons entre autre d'un pipe particulièrement utile qui va nous permettre de gérer nos données asynchrones.


## Utiliser les pipes

Des outils forts plaisant, cela ne vous parle probablement pas et sa tombe bien moi non plus.
Pour être plus clair les pipes servent essentiellement et pour ne pas dire qu'à sa à gérer l'affichage de nos données.
En effet, les pipes vont permettre entre autres de modifier ou formater des chaînes de caractère dont la plus utilisée est certainement le format date.

Pour utiliser les pipes il suffit dans une string interpolation d'insérer l'opérateur de pipes qui est tout simplement |
Imaginos que nous souhaitions maintenant ajouter la date à laquelle l'appareil vient d'être vérifié.
Pour cela nous allons créer un nouvel objet Date() dans notre devicesComponent :

`lastUpdate: object = new Date();`

Puis nous modifirons notre HTML comme ceci :

```html
      <li *ngFor="let devicesName of devicesObject.devicesName; let i=index" [ngClass]="{'list-group-item': true,
                                                                                        'list-group-item-success': devicesObject.devicesStatus[i] === 'Allumé',
                                                                                        'list-group-item-danger': devicesObject.devicesStatus[i] === 'Eteint',
                                                                                        'list-group-item-warning': devicesObject.devicesStatus[i] === 'Veille'}">
        <span>{{ lastUpdate | date }}</span>
        Appareil : {{ devicesName }} -- Status : {{ devicesObject.devicesStatus[i] }}
      </li>
```

Voilà que notre date s'affiche au format classique maintenant nous pouvons la modifier avec plus de précision.
Par exemple ceci : 

`{{ lastUpdate | date: 'dd/MM/y hh:mm' }}`

Nous donnera ce format de date pour le 4 avril 2019 à 10h12 (04/04/2019 10:12). N’hésitez jamais à aller voir la documentation.
Il est aussi possible de cumuler les pipes mais dans le bon ordre ainsi si vous aviez une date au format nombre et lettres vous pourriez la mettre en majuscule comme ceci :

`{{ lastUpdate | date 'EEEE MMMM y' | uppercase }}`



#### Le pipe async

Le pipe async est particulier et il va vous être absolument indispensable si vous utiliser réellement Angular dans l'objectif de créer une app réalisant des requêtes asynchrones.
En effet, le pipe async va nous permettre de différer l'arrivé d'un élément avec un string interpolation.
C'est a dire que l'élément sera affiché mais son contenu peut arriver en différé ou pas du tout si le serveur ne répond pas.
Finalement cela fonctionne un peut comme une requête Ajax asynchrone dont on attend le callback.
Ce que je vous propose c'est de simuler ce comportement pour notre date précédemment créée.

Pour cela nous allons devoir créer un nouvel objet appelé Promise cet objet n'est pas propre à Angular, c'est du JavaScript standard.
Il prend en paramètre une fonction anonyme avec deux paramètres :
 
Le premier est une fonction qui prendra en paramètre le résultat et l'assignera à l'objet instancié avec Promise, ici lastUpdate
Le second permet de gérer les cas d'erreurs

```typescript
  lastUpdate = new Promise(
    (resolve,reject) => {
      let date = new Date();
      setTimeout(
        () => {
          resolve(date);
        }, 2000
      )
    }
  );
```

Ensuite allons dans notre code HTML et modifiions tous simplement notre ligne ainsi :

```html
<span>{{ lastUpdate | async | date }}</span>
```

Avec ce code vous verrez votre date s'afficher au bout de deux secondes exactement 😃.
Vous avez maintenant un aperçu de la gestion de l’a-synchronicité avec Angular.


