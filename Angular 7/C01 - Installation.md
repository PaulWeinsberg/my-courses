# CHAPITRE I : INSTALLATION D'ANGULAR

Dans ce cours nous considérerons que vous travaillez sur un environnement Linux Arch et que vous utiliser la version 7.
Si vous utiliser Debian les choses ne devraient pas être trop différentes si vous remplacez pacman par apt-get les gestionnaires de paquets respectifs aux distributions.
Dans le cas contraire vous devrez adapter les commandes pour Windows ou Mac.


## Préparation de l'environnement

#### Installation de nodeJS et npm

Pour installer Angular nous allons devoir commencer par installer le gestionnaire de paquets de nodeJS.
Pour cela rien de plus simple :

`pacman -S nodejs`

ensuite le gestionnaire :

`pacman -S npm`

Ceci devrez vous installer les dernières versions.

#### Installation d'Angular via npm

Passons à l'installation d'Angular via notre nouveau gestionnaire avec l'instruction suivante :

`npm install -g @angular/cli`

CLI est l'abréviation de Command Line Interface. C'est concrètement le système qui va nous permettre de créer nos applications avec des instructions en ligne de commande.


## Notre premier projet

Nous allons donc créer notre premier projet et vérifier par la même occasion que toute l'installation c'est bien déroulée.
Si ce n'est pas le cas la commande ng ne fonctionnera pas.

Pour créer un projet avec Angular rien de plus facile il suffit de vous placer dans le dossier qui contiendra le dossier de votre projet.
Puis de taper l'instruction suivante dans votre ligne de commande :

`ng new nom-du-projet --skipTests=true`

Ainsi va vous demander si vous souhaitez ajouter un router, dites oui (y) puis va vous demander quel format de feuilles de style vous souhaitez utiliser.
Ici nous utiliserons SCSS pour notre CSS. Notez qu'il est également possible d'indiquer directement le style en utilisant le drapeau --style=scss dans notre cas.

Il est possible d'ajouter --skipTests=true comme option dans notre instruction de création. Ce qui nous évitera certains tests unitaires qui vont au de la de la limite de ce cours. Il y a aussi l'instruction --minimal=true qui est encore plus restrictive sur les tests, en effet elle retire les fichiers de notre répertoire concernant les tests. Nous utiliserons donc la première.

Vous constaterez qu'un nouveau répertoire portant le nom de votre projet vient d'être créer, maintenant plaçons-nous dans celui-ci et démarrons notre serveur.

`ng serve --open`

La commande sans l'instruction --open fonctionne également.
Si tous ce passe bien vous devriez être redirigé vers localhost:4200 dans votre navigateur. La page d'Angular apparaît. It's works !

#### Utiliser une librairie CSS comme bootstrap

Pour cela rendons nous dans le répertoire de notre projet avec notre ligne de command et tapons l'instruction suivante :

`npm install bootstrap@latest --save`

Le drapeau --save nous sert à ajouter bootstrap aux packages du projet.
S'il vous réclame des dépenses pensez à les installer en premier, en général JQuery et et popper.js.
En revanche si ce n'est que pour les style CSS de bootstrap l'installation sera suffisante.

Pour l'inclure dans le chargement de votre page ou simplement ajouter vos feuilles de style.
Rendez-vous dans la fichier angular.json qui contient de nombreuses options pour votre projet angular et rendez vous à la ligne suivante :


```TypeScript
            "styles": [
              "src/styles.scss"
            ],
```


Cette ligne fait partie de l'objet architect puis build et enfin options. Ce n'est pas le cas dans la version que vous utilisez cherchez simplement la ligne correspondante.

On ajoutera donc au tableau le fichier bootstrap. scss :

```TypeScript
            "styles": [
              "node_modules/bootstrap/scss/bootstrap.scss",
              "src/styles.scss"
            ],
```

 
#### Le package rxjs-compat
 
Nous allons également ajouter un package nommé rxjs-compat qui nous servira dans un chapitre ultérieur.
Il nous servira à assurer la compatibilité entre les observables. Nous y reviendrons.
 
Idem que pour bootstrap dans notre ligne de commande :
 
`npm install rxjs-compat --save`

Voilà le paquet est installé.


 
