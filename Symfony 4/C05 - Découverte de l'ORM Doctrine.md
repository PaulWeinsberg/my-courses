# CHAPITRE V : DÉCOUVERTE DE L'ORM DOCTRINE

Doctrine est l'ORM (Object Relation Mapping) c'est à dire un système de mapping par objet relationnel. Dit ainsi ça peut paraitre très complexe mais il n'en est rien. En réalité Doctrine est là pour fournir un outil capable de faire le lien entre une base de donnée SQL et un système de fichiers PHP basé sur la programmation orientée objet.

Pourquoi utiliser un tel système ? Il y as de multiples raisons, les principales sont la couche d'abstraction générée entre la base de données et les opérations faites par nos différentes méthodes contenues dans le code PHP. La seconde raison de l'utilité de ce lien est simplement de maximiser la compréhension de nos tables SQL en implémentant l'ensemble des propriétés de ces tables au sein de classes spécifiques. Le risque d'erreur est donc diminué et les échanges entre développeurs connaissant l'ORM en question sont grandement facilités.

## Entity, Repository et Manager

On peut séquencer le fonctionnement de Doctrine en trois éléments principaux que sont les Entités (Entities), les Répertoire (Repositories) et enfin les Manager. Chacun de ces éléments ont leurs fonctionnalités propres et bien définies que je vous propose de survoler avant la mise en pratique.

* **Entity** : Elles représentent nos table SQL mais dans un format de classe PHP. Ce format permet une lecture claire et consise du contenu de la table correspondante.
* **Repository** : Les repositories correspondent eux à l'ensemble des entités contenues dans une table.
* **Manager** : Ils permettent de réaliser des opérations sur nos tables et font également le lien entre la base de donnée SQL et les fichiers PHP.

Nous avons donc ici tous les éléments nous permettant l'ensembles de intéractions nécessaire mais cela ne vous dit pas comment l'utiliser. C'est ce que nous allons voir dans la suite de ce cours.

## Objectif

Avant de continuer il est important de définir clairement notre objectif afin de ne pas naviguer à l'aveugle. Alors qu'allons nous faire et pourquoi. Premièrement nous allons créer une base de données SQL avec Doctrine qui contiendra une table de nos différents bien pour nous utiliserons l'ORM pour effectuer quelques opération sur cette table.

Cependant nous n'utiliserons pas directement MySQL ou MariaDB pour créer nos tables car il faudra passer par Symfony et sa console qui fera les chose pour nous et de manière propre. Donc avant de créer notre table avec Symfony je vous propose un petit tableau qui récapitulera ce que devra contenir notre table SQL cela sera bien plus pratique à implémenter par la suite.

Voici donc la table SQL que nous allons créer avec Symfony :

 Propriété              | Types                        | Options                              |
----------------------- | ---------------------------- | ------------------------------------ |
id                      | int                          | NOT NULL PRIMARY KEY AUTOINCREMENT   |
title                   | varchar(255)                 | NOT NULL                             |
description             | text                         | &nbsp;                               |
surface                 | smallint                     | NOT NULL                             |
rooms                   | tinyint                      | NOT NULL                             |
bedrooms                | tinyint                      | NOT NULL                             |
floor                   | int                          | NOT NULL DEFAULT 0                   |
price                   | int                          | NOT NULL                             |
heat                    | tinyint                      | NOT NULL DEFAULT 0                   |
city                    | varchar(255)                 | NOT NULL                             |
address                 | varchar(255)                 | NOT NULL                             |
postal_code             | int                          | NOT NULL                             |
sold                    | boolean	                     | NOT NULL DEFAULT 0                   |
created_at              | datetime                     | NOT NULL                             |


Nous avons maintenant une vision plus claire de ce que nous devons réaliser. 

## Création de la BDD

Symfony va nous permettre de créer notre base de données da A à Z sans avoir à ne toucher une seule ligne de SQL ce qui va être plutôt pratique. Comme nous l'avons vu dans un précédent chapitre votre fichier `.env` contient certaine information à la constante `DATABASE_URL` et les commandes que nous allons réaliser à présent s'appuient sur celles-ci.

### Database

Pour créer notre base de données rien de plus simple il nous suffit d'entrer la commande suivante et Doctrine ce chargera de l'implémentation de celle-ci dans MySQL :

`php bin/console doctrine:database:create`

Evidemment n'oubliez pas de démarrer votre serveur MySQL sans quoi cette commande provoquerait un erreur tout simplement.

### Entity (table)

Nous allons maintenant, également grâce à la console ajouter conjointement une Entity et une table SQL correspondante. Pour cela je vous invite à regarder les commandes disponible dans la console comme nous avons vu dans un chapitre précédent. Vous y retrouverez un ensemble de commande `make` ce scommandes permettent de générer du code. Dans notre cas ce que nous voulons créer est une `Entity`. En effet plutôt que de créer une table `Properties` il nous faut plutôt définir une `Entity` correspondante à 1 élément de la table car c'est comme cela que fonctionne Doctrine. Le nom n'a en soit pas d'importance, c'est plutôt la façon dont vous intellectualisé votre base de données qui est important ici.

Nous allons adonc maintenant générer cette `Entity` pour cela il nous suffit d'indiquer dans la console :

`php bin/console make:entity`

Dans un premier temps la console vous renvoie une demande sur le nom de votre `Entity` ici nous indiquerons `Property` car il s'agit des biens de notre agence.

En suite la commande va vous demander si vous demander si vous souhaitez ajouter un champs, indiquez le nom du champs à ajouter puis son type enfin la console vous demande si vous souhaitez que ce champs puisse être NULL. Je vous laisse donc renseigner chacun des champs dans la console que nous avons vu plus haut **sauf l'id** et ne vous inquiétez pas il est normal que vous ne puissiez pas définir l'ensemble des options dès maintenant, d'ailleurs vous constaterez également que `varchar` correspond à `string` et que `tinyint` n'est pas disponible, dans ce cas nous choisirons `smallint`.

Une fois l'opération réalisée vous pouvez vous rendre dans le dossier `src/Entity` et le dossier `src/Repository` vous y consterez deux nouveaux fichiers. Je vous laisse observer leur contenu et vous pourrez d'ailleurs constater que l'id est bien renseigné car c'est un indispensable de Doctrine. Il est donc automatiquement généré.

### Migration

Doctrine contient un système de migration, d'ailleurs à la fin de votre création d'entité Symfony vous informe que si vous êtes satisfait vous pouvez utiliser la commande `make:migration`. Cette commande effectue deux opérations :

* **Comparaison** : Elle compare votre base de données actuelle avec la base de données que vous comptez générer.
* **Versionning** : Elle créer un fichier permettant de versionner (possibilité de revenir en arrière) pour votre BDD SQL ou d'appliquer ces changements.

Nous allons donc effectuer cette commande et voir le fichier généré qui nous en dira plus. Allez donc dans le dossier `src/Migrations` et ouvrez le seul fichier qu'il contient. Vous remarquerez les instructions SQL correspondante à la BDD que vous souhaitez générer dans une fonction appelée `up()` la fonction `down()` permet, elle, de revenir en arrière.

Nous pouvons donc maintenant lancer la commande nous permettant d'appliquer cette migration à notre base de données SQL. Pour cela il nous suffit d'utiliser la commande suivante :

`php bin/console doctrine:migration:migrate`

Connectez-vous à votre base de données MySQL avec votre console et faites un coup de `DESCRIBE property` dans la nouvelle base de données. Vous verrez que les changements ont bien étés appliqués. Voici d'ailleurs ce que la console affiche :

```
+-------------+--------------+------+-----+---------+----------------+
| Field       | Type         | Null | Key | Default | Extra          |
+-------------+--------------+------+-----+---------+----------------+
| id          | int(11)      | NO   | PRI | NULL    | auto_increment |
| title       | varchar(255) | NO   |     | NULL    |                |
| description | longtext     | YES  |     | NULL    |                |
| surface     | smallint(6)  | NO   |     | NULL    |                |
| rooms       | smallint(6)  | NO   |     | NULL    |                |
| bedrooms    | smallint(6)  | NO   |     | NULL    |                |
| floor       | int(11)      | NO   |     | NULL    |                |
| price       | int(11)      | NO   |     | NULL    |                |
| heat        | smallint(6)  | NO   |     | NULL    |                |
| city        | varchar(255) | NO   |     | NULL    |                |
| address     | varchar(255) | NO   |     | NULL    |                |
| postal_code | int(11)      | NO   |     | NULL    |                |
| sold        | tinyint(1)   | NO   |     | NULL    |                |
| created_at  | datetime     | NO   |     | NULL    |                |
+-------------+--------------+------+-----+---------+----------------+
```

Comme nous pouvons le voir il nous manque certaine informations que nous souhaitions renseigner plus tôt. Notamment les valeurs par défaut. Pour cela nous allons modifier les annotations du fichier `src/Entity/PropertyEntity.php`. On peut y ajouter des options au format JSON ce qui nous donnera ceci pour la colonne `sold` et il ne faut pas non plus oublier d'assigner la valeur pas défaut à la propriété elle-même. Je vous laisse faire les deux autres :

```php
    /**
     * @ORM\Column(type="boolean", options={"default":0})
     */
		private $sold = false;
```
*0 en SQL === false*

Nous pouvons maintenant mettre à jour notre base de données. Dans un premier temps on créer le fichier de migration avec la commande `make:migration`, ensuite on vérifie le fichier généré pour éviter les erreurs puis on applique le contenu de la fonciton `up()` avec la commande `doctrine:migration:migrate`. Voilà notre base de données est maintenant comme nous le désirions à l'exeption des types non pris en compte avec Symfony.

Vous avez maintenant les bases pour créer et modifier une base de données avec Symfony. Dans le prochain chapitre nous allons interagir avec cette nouvelle base et commencer à réellement exploiter la puissance de Symfony =)