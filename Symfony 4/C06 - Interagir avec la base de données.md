# CHAPITRE VI : INTERAGIR AVEC LA BASE DE DONNÉES

Dans ce chapitre nous allons commencer les chose serieuses ! Enfin ! Oui nous allons commencer à récupérer et envoyer des informations à notre base de données ce qui va nous permettre de donner vie à notre fameuse agence en vours de création.


## Enregistrer des données

Pour commencer nous allons créer un premier bien de façon peu conventionnel, l'objectif va être de comprendre comment fonctionne l'insertion de données dans notre table. Pour commencer je vous propose de descendre en bas de votre fichier `Property` vous y retrouverez des méthodes `get` et `set` pour chacune de vos colonnes. Ces méthodes permettent de modifier l'état de notre objet mais ne l'enregistre pas dans la base de données. C'est ce que nous allons faire pour le moment et pour ne pas perdre de temps car nous supprimerons ce code par la suite je vous propose de définir cette méthode dans la méthode `index` de notre route `home`.

### Création de notre objet

Il va donc nous falloir importer le namespace correspondant à `Property` et créer une instance de cette classe puis nous utiliserons les méthodes dont nous venons de parler :

```php
		$property = new Property();
		$property->setTitle('Belle maison à Bordeaux')
							->setDescription('Une ravisante maison sur les bords de la Garonne')
							->setSurface(120)
							->setRooms(5)
							->setBedrooms(2)
							->setPrice(245000)
							->setCity('Bordeaux')
							->setAddress('15 avenue du Général de Gaule')
							->setPostalCode(33000)
							->setCreatedAt(new \DateTime());
```

Comme vous pourrez le constaté je n'ai pas défini les propriété qui ont une valeur par défaut et évidemment l'id n'est pas à préciser car il s'agit de la clé primaire qui est automatiquement incrémenté par notre BDD.

Avant de recharger notre page je vous propose d'utiliser une fonctionnalité de Symfony, il s'agit de la fonction `dump()` qui marche de façon similaire à `var_dump()` mais qui ne s'affiche pas sur la page mais dans la barre du profiler ou directement dans celui-ci. L'affichage est beaucoup plus sympa ! En bas de notre méthode `index` juste avant la route `dump($property)`.

### Insérer notre objet dans la base de données

Pour enregistrer notre objet dans la BDD nous allons devoir utiliser le manager de Doctrine, de sa classe `ManagerRegistry`. Elle fait d'ailleur parti des services disponibles avec l'autowiring et se trouve dans le namespace `Doctrine\Common\Persistence\ManagerRegistry`. Il existe deux méthodes utilisables depuis la variable `$this` permettant d'utiliser le `ManagerRegistry` de doctrine qui hérite lui même de la classe `AbstractManagerRegistry` de Doctrine, la première est `getDoctrine()` et doit être suivi par `getManager()` pour qu'il nous renvoie une instance de `ObjectManager`. 

Nous allons utiliser ces deux méthodes et assigner la manager dans un variable que nous appelerons `$entityManager`.

```php
		$entityManager = $this->getDoctrine()->getManager();
```

Mais il est également possible de définir la classe `ObjectManager` en l'important depuis `Doctrine\Common\Persistence\ObjectManager` et en l'initialisant dans le constructeur. Personnelement je préfère cette méthodologie car elle donne une bonne lisibilité dans à notre code.

Deux méthodes sont à appelée de façon consécutives pour enregistrer notre objet. La première est `persist($objet)` qui prévien en quelques sorte Doctrine que nous comptons probablement enregistrer cet objet dans notre BDD. Puis finalement nous l'enregistrerons avec `flush($objet)`.

Chargez maintenant votre page d'accueil **une seule fois** et ne la fermez pas pour le moment. Puis allez dans votre base de données depuis votre terminal et affichez le contenu de celle-ci :

`SELECT * FROM property`

Vous pouvez constater que les enregistrements ont bien été réalisés. Maintenant retournez sur votre page sans la recharger et cliquez sur l'icone de la base de données du Profiler.

On peut voir que trois requête ont été réalisées, la première est `COMMIT`, elle indique à la base de données qu'une transaction va avoir lieu, puis `INSERT INTO...` la transaction qui par ailleurs est au format `prepare/execute` et enfin `START TRANSACTION` qui réalise les changements reçus.

Vous venez de créer votre première requête SQL avec Symfony. Maintenant il nous faut absolument supprimer le code de notre méthode `index()` dans notre controleur car mise à part pour l'exemple ceci n'est vraiment pas à faire et encore moins intéressant.

### Récupérer des données

Maintenant faisons le chemin inverse, essayons de récupérer les informations mises précédemment en base de données. Pour cela nous allons nous servire du `PropertyRepository` qui sert à effectuer non pas des actions sur un élément de la classe `Property` mais sur l'ensemble de la classe correspondante.

Nous allons donc créer une instance de notre `PropertyRepository` avec le constructeur, pour cela nous devons indiquer le namespace `App\Repository\PropertyRepository` puis instancier un objet à partir de celle-ci :

```php
	private $_repository;

	public function __construct(PropertyRepository $repository) {
		$this->_repository = $repository;
	}
```

Maintenant nous sommes prêt à effectuer des actions avec notre nouvelle propriété.

#### La méthode `find()`

La méthode `find()` permet de rechercher une entité via son `id`. Nous allons tester son fonctionnement avec l'id 1 puis faire un `dump()` et voir ce que renvoie cette méthode.

```php
	public function index(): Response {

		$property = $this->_repository->find(1);
		dump($property);
```

Super ! En quelques lignes de code nous venons de récupérer notre entité ! Voyons à présent comment récupérer l'ensemble ddes éléments contenus dans notre table.

#### La méthode `findAll()`

Cette méthode va nous permettre de récupérer l'ensemble de nos biens présents dans notre table. Bien sûr nous n'en avons qu'un pour le moment mais faisons quand même la démarche pour vérifier sont fonctionnement : 

```php
	public function index(): Response {

		$property = $this->_repository->findAll();
		dump($property);
```

Cette fois ci c'est un tableau d'objet qui nous est renvoyé. On peut d'ailleurs voir notre bien à l'index 0.

#### Les méthode `findBy()` et `findByOne()`

Chacun des deux méthodes prend en paramètre un tableau associatif dont la clé est égale au nom d'une colonne et sa valeur à la valeur à rechercher. La différence entre ces deux méthodes se situe sur le nombre et la forme des objets renvoyés. `findByOne()` ne renvoie que la première occurence alors que `findBy()` renvoie un tableau associatif de l'ensemble des occurences existentes. Par exemple si nous voulons afficher les biens sur bordeaux :

```php
	public function index(): Response {

		$property = $this->_repository->findBy([
			'city' => 'Bordeaux'
		]);
		dump($property);
```

On peut voir notre tableau correctement retourné. Vous pouvez avec cette méthode également préciser des paramètres comme `orderBy`, `limitTo`, `$offset`. Vous trouverez de plus amples informations dans la documentation.

### Créer ses propres requêtes

Avec Doctrine et Symfony vous pouvez créer facilement vos propres requêtes. Par exemple si nous souhaitons récupérer tous les éléments qui sont dans la ville de Bordeaux nous pourrions créer la fonction suivante dans la classe `PropertyRepository` :


```php
    public function findByCity($city): array {
        return $this->createQueryBuilder('p')
                    ->andwhere('p.city = :city')
                    ->setParameter(':city', $city)
                    ->getQuery()
                    ->getResult();
    }
```

Ce schéma est mis en exemple dans le code de cotre `PropertyRepository` en commentaire mais je trouve la syntaxe très proche d'une requête SQL standard alors pas besoin de vous faire de longues explications. Vous pouvez également multiplier les paramètre, si par exemple nous voulons que notre fonciton retourne également les biens inférieur à 300 000 nous pouvons l'ajuster comme suit :

```php
    public function findByCity($city,$price): array {
        return $this->createQueryBuilder('p')
                    ->andwhere('p.city = :city')
                    ->setParameter(':city', $city)
                    ->andwhere('p.price < :price')
                    ->setParameter(':price', $price)
                    ->getQuery()
                    ->getResult();
		}
```

#### Des requêtes plus complexes

Il est également possible de créer des requêtes plus complexes comme vous pouvez en avoir besoin je vous laisse lire la documentation en ce sens avec DQL (Doctrine Wuery Language) à l'adresse suivante :

[DQL Syntaxe](https://www.doctrine-project.org/projects/doctrine-orm/en/latest/reference/dql-doctrine-query-language.html)

## Les échanges BDD - Fichiers

#### La priorité aux fichiers

Le

#### Mise à jour avec `flush()`