# CHAPITRE I - INSTALLATION

Dans ce chapitre nous allons voir comment installer SASS sur notre système afin de pouvoir commencer à l'utiliser. Ensuite nous commencerons par la partie la plus simple c'est à dire l'utilisation des variables avec SASS.

## L'installation en ligne de commande

C'est l'installation que je vous recommande car elle vous permttra de gérer entièrement votre préprocesseur et ne sera pas dépendant d'une application tierce qui n'est aps forcément toujours à jour et ne propose pas toujours l'ensemble des fonctionnalités.

### Sur Windows

Pour Windows plusieurs possibilités s'offrent à vous vous pouvez soit utiliser votre gestionnaire de paquets de paquets de nodesJS :

`npm install -g sass`

Soit utiliser le gestionnaire de paquets Chocolatey :

`choco install sass`


### Sur Linux

Sur Linux vous pouvez directement passer par NPM après avoir installé nodeJS et son gestionnaire de paquets. La syntaxe sera donc la même que celle que nous avons vu ci-dessus.

### Sur Mac

Sur Mac idem. Mais vous pouvez aussi utiliser Brew :

`brew install sass/sass/sass`

## L'installation via une application graphique

Si vous êtes absolument allergique à la ligne de commande cette option est possible et le lien ci-dessous vous permettra de voir les différentes applications disponibles à cet effet. En revanche dans ce cours les insttructions se ferons via la ligne de commande.

[Voir les logiciels disponibles](https://sass-lang.com/install)

## Découverte de la CLI

Avant de commencer à voir les foncitionnalités que propose SASS nous allons tenter une petite opération de compilation de SASS vers un fichier CSS standard. Mais avant je dois vous expliquer quelque chose. SASS dispose de deux foncitonnements. Le premier utiliser une syntaxe SCSS qui est en général le plus utiliser car elle reprend de nombreux code respectif au CSS et je la trouve plus lisible. La deuxième option est l'utilisation de la syntaxe SASS et dont les extensions de fichiers ne seront pas `.scss` mais `.sass`. Globalement la différence entre ces deux syntaxes réside dans l'utilisation des accolades qui n'existent plus avec SASS. Ici nous utiliserons la syntaxe SCSS bien plus populaire et plus lisible.

### Installation du projet

Vous trouverez dans le lien repository Github du cours un dossier zip `project.zip` celui contient la structure de notre projet qui se présente comme suit :

```
project
├── css
│   └── scss
├── img
│   ├── chevron-next.svg
│   ├── favicon.ico
│   ├── full.jpg
│   ├── icon2.png
│   ├── iconmin2.png
│   ├── iconmin.png
│   ├── icon.png
│   ├── logo.png
│   ├── pc-service.png
│   ├── port-01.jpg
│   ├── port-02.jpg
│   ├── port-03.jpg
│   ├── port-04.jpg
│   ├── port-05.jpg
│   ├── port-06.jpg
│   ├── port-07.jpg
│   ├── port-08.jpg
│   ├── slider1.jpg
│   ├── slider1min.jpg
│   └── slider2.jpg
└── index.html
```

Ceci vous évitera d'avoir à rédiger du HTML pendant ce cours ce qui n'est pas l'objectif.
Notre dossier `css` contiendra à sa racine l'ensemble des fichiers CSS de notre site web. Le dossier SCSS l'ensemble de nos fichiers SASS avant la compilation.

### La compilation

Dans cette partie nous allons voir comment fonctionne la compilation avec SASS. Pour cela il nous faut créer notre permier fichier SCSS que nous nommerons `style.scss` évidemment dans le répertoire `scss`. Dans celui-ci nous allons définir notre première règle SCSS qui conernera tous nos titres `h1` :


```scss
h1,h2,h3,h4,h5,h6 {
	font-family: Arial, Helvetica, sans-serif;
}
```

Pour l'instant rien de bien extraordinaire car tous CSS valide est également du SCSS valide.
Cependant pour le moment notre fichier ne fonctionne pas et c'est normal car nous avons un seul fichier appelé par notre page HTML et ce fichier est `style.css` qui doit ce trouver à la racine du fichier `css`.

Pour compiler notre fichier SCSS en CSS il nous faut utiliser la ligne de commande et la syntaxe suivante :

`sass fichier.scss:fichier.css`

Il est également possible de faire celà en indiquant des répertoires ce qui permet de compiler l'ensemble des fichiers SCSS s'y trouvant. Ouvez votre ligne de commende dans le dossier `css` puis compilez avec l'instruction suivante :

`sass ./scss/:./`

Cette instruction compilera l'ensemble des fichiers SCSS vers le répertoire `css`.
Cette fois-ci notre code est foncitonnel ! 

### Le débogage

Imaginons que nous ayons pleins de fichier SCSS se compilant en un seul fichier CSS, le problème que nous pourrions rapidement avoir lors du débogage via la console de chrome ou firefox est la recherche du fichier original correspondant. Heureusement SASS inclue un fichier `map` qui permet au navigateur de nous informer sur le fichier SCSS correspondant. Ouvez votre navigateur et vous verrez que le lien du fichier source indiqué est un fichier SCSS et non un fichier CSS ce qui est pourtant le cas car vous venez de le compiler. 

Il est recommandé de désactiver cette option avec le drapeau `--no-source-map` pour la production mais pour le développement ceux-ci nous seront absolument nécessaires.

### Le mode watch

Maintenant que vous savez compiler votre code SCSS en véritable CSS vous devez vous dire que vous allez devoir sans arrêt compiler et regarder ensuite le résultat dans votre navigateur ce qui ajoute une étape entre le CSS et la visualisation du résultat. Heureusement SASS permet d'autocompiler lors de chaque sauvegarde du code SCSS. Pour cela il suffit d'ajouter le drapeau `--watch`. Il ne vous restera plus qu'a recharger la page pour mettre à jour votre site avec vos nouvelles propriétés SCSS compilés. Essayez plusieurs règles et observez les résultats. Il faut biensur que votre terminal reste ouvert et le mode `watch` lancé.

Il existe d'autres drapeaux que je vous laisse découvrir avec l'utilisation du drapeau `--help`.