# INTRODUCTION

Dans ce nours je vous propose d'apprendre à utiliser un préprocesseur CSS et plus précisemment le préprocesseur SASS. Nous aborderons dans ce chapitre les grandes lignes de ce qu'est et ce à quoi peut servir un préoprocesseur.

## Qu'est-ce qu'un préprocesseur CSS

Les préprocesseurs CSS sont des utilitaires permettant d'utiliser une syntaxe différente en ajoutant de nombreuses foncitonnalités au CSS conventionnel, standard. En effet, depuis ses début le CSS n'est pas très apprécié par les développeurs notamment pour les faibles possibilités qu'il offre et la multiplication des lignes de code se répétant inlassablement de fichier en fichier et de classe en classe.

Malgré ces nombreux défauts évidents, le CSS est un outil devenu absolument indispensable pour réaliser n'importe quel projet web digne de ce nom et pour utiliser syntaxe des préprocesseurs que nous allons voir dans ce cours. Une bonne connaissance du CSS est donc indispensable.

## Les problèmes du CSS

Je vous propose avant de démarrer l'installation de SASS dans le prochgain chapitre d'énumérer les problématiques rencontrées souvent avec le CSS et auquel le préprocesseur SASS va nous permettre de répondre :

* **Les variables** : Bien qu'existantes maintenant en CSS leur utilisation reste limité car tous les navigateurs ne supportent pas cette fonctionnalité. Les variables permettent par exemples de définir une couleur dans une variable et de l'utiliser comme telle. Ainsi en changeant la valeur de la variable c'est l'intégralité de nos propriétés CSS concernées qui se modifies.

* **L'imbrication** : Elle permet de réduire considérablement la taille des fichiers CSS et ainsi d'en améliorer la lisibilité tout en accélérant le temps de développement.

* **Les partiales** : Permet de diviser un même fichier SASS en sous parties, ce qui est bien plus simple à géré ensuite.

* **Les imports** : Les imports sont également présent dans le CSS mais ne foncitonnent pas de la même manière. Avec le CSS il faudra plusieurs requêtes au navigateur pour obtenir l'ensemble des parties. Avec SASS ses différentes parties seront incluse dans un seul et même fichiers.

* **Les fonctions** : Les mixins permettent de créer un style de fonciton propre au préprocesseur SASS. Cette fonction permettent d'éviter les répétitions tout en limitant le risque d'erreur. Elle permettent également bien d'autre chose. La pratique sera plus parlante.

* **Les classes** : Avec SASS il est possible d'utiliser également des classes ou plutot un genre de classe car la fonctionnalité *extends* permet d'utiliser le principe d'héritage au sein de notre code CSS.

C'est déjà pas mal non ?

## Pourquoi SASS ?

Pour utiliser SASS sachant qu'il existe d'autre préprocesseurs ? Premièrement SASS est le plus utilisé ce qui offre une maintenance régulière du préprocesseur, d'autre part il jouit d'une grande communauté et trouver des réponses à ces questions sera plus simple. Enfin j'ajouterai qu'ilest également embarqué dans beaucoup de framework web ce qui est encore une raison supplémentaire de le choisir.