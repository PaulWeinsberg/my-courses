# CHAPITRE VIII - LA CLI

La CLI ou `commande line interface` est l'interface par laquelle vous compilez vos fichiers depuis le départ de ce cours c'est à dire votre terminal. Cette interface dispose de plusieurs paramètres ou options dont vous avez déjà connaissance, notamment de l'option `--watch` nous permettant de compiler automatiquement nos fichiers SCSS en fichier CSS à chaque sauvegarde de l'un d'eux.

## Les commandes les plus utiles

Je vous propose pour conclure ce cours de voir quelques options supplémentaire qui pourraient vous être utile lors de vos futurs développements. Voici donc une petite liste descriptive de ces options :

* `--style= | -s=` : La valeur compressed permet de générer un fichier de style sur une seule ligne et de façon à être minimiser en terme de poids au maximum.
* `--no-source-map` : Enlève la production du fichier `.map` lors de la compilation ce qui retire donc la possibilité de voir dans quel fichier SCSS les propriétés d'un éléments ont été définies. Ceci réduit le poids de vos requêtes en production.
* `--no-stop-on-error` : Permet d'activer la compilation même en cas d'erreurs rencontrées.
* `--no-color` : Désactive l'affichage des couleurs dans le terminal, peut intéressant sauf dans le cas d'un terminal qui ne le supporterait pas. 
* `--quiet` : Désactive l'affichage des warning dans le terminal. 
* `--help | -h` : Permet d'activer l'ensemble des options disponibles avec le compilateur SASS. 


Voilà c'est à peu prêt tout ce  qu'il y a à dire sur ce petit bijoux qui va améliorer votre quotidient de développeur web.

## Passer à la pratique

Dans le fichier `project.zip` vous retrouverez de quoi travailler avec SASS et créer un site web à partir d'une maquette rudimentaire. C'est un bon moyen d'apprendre à utiliser le préprocesseur.

Go work !