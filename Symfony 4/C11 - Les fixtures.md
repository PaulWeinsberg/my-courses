# CHAPITRE XI : LES FIXTURES

Depuis le début de notre projet nous n'avons créé ensemble qu'un seul et unique bien. Peut être que de votre côté vous en avez déjà renseigné plusieurs ? Quoi qu'il en soit cela ne doit pas dépasser la dixaine je suppose. Alors quel est le rapport avec les fixtures ? Les fixtures vont nous permettent de créer des entités facilement pour compléter un peu notre base de données et de cette façon nous pourrons travailler sur des bases plus équivalentes au conditions réelles de production de notre application.

Je vous propose ainsi tout simplement de compléter notre base de données d'une façon automatisée afin de générer une petite centaine de biens.

## Créer une fixture

Pour créer une fixture nous pouvons directement nous servire de la console et taper l'instruction suivante :

`composer require --dev doctrine/doctrine-fixtures-bundle`

Celle-ci va tout simplement nous installer le package nécessaire à leur utilisation. Nous pourrons ensuite exécuter l'instruction de création suivante :

`php bin/console make:fixtures`

Nous appelerons notre fixture `PropertyFixture`, en toute logique. Une fois terminé vous verrez apparaitre une fichier dans le repertoire suivant `src/DataFixtures/PropertyFixture.php`. On peut aussi constater que le fichier `config/Bundles.php` à été mis à jour.

## Compléter notre fixture

Dans la classe nouvellement créée, la méthode `load` sera exécuter lorsque nous le souhaiterons via la CLI (console) de Symfony.

Pour remplir notre base de données de 100 nouveaux bien nous allons créer une boucle de 100 itérations qui lors de chacunes d'entre-elles instancierons un objet de la classe `Property` et assignerons des valeurs aléatoires aux différentes propriétés de notre objet.

Votre question à ce moment précis devrait être : Tout cela à l'air intéressant mais comment vais-je faire pour que les villes ressemblent à de vrais noms de villes etc. ? Et bien ne vous inquiété pas quelqu'un à déjà rencontré cette problématique et à créer un petit package composer prêt à l'emploi. Il s'agit de : `Faker` et nous allons nous servir. La librairie et la documentation du package se trouve sur le repo Github à l'adresse suivante :

[Faker](https://github.com/fzaninotto/Faker)

Comme nous le signal la documentation nous pouvons l'installer grace à composer et la commande suivante :

`composer require fzaninotto/faker --dev`

Avec le drapeau adéquate car il s'agit bien sûr d'un package inutile en production. Nous pouvons maintenant utiliser les méthode et propriétés de `Faker` pour générer aléatoirement nos différentes propriétés de notre classe tout en respectant un minimum une syntaxe proche du réel. En effet ce petit package permet entre autre de générer des noms de villes existantes, des codes postaux, des noms, prénoms etc. Et même en français ! Je vous laisse lire la documentation de `Faker` afin de mieux comprendre son fonctionnement pour le moment attardons nous sur notre méthode `load` qui devrait ressembler à ceci une fois complétée :

```php
<?php

namespace App\DataFixtures;

use Doctrine\Bundle\FixturesBundle\Fixture;
use Doctrine\Common\Persistence\ObjectManager;
use Faker\Factory;
use App\Entity\Property;

class PropertyFixture extends Fixture
{
    public function load(ObjectManager $manager)
    {
        $faker = Factory::create('fr_FR');

        for ($i=0; $i < 100; $i++) { 
            $property = new Property();
            $property
                ->setDescription($faker->paragraph(3,true))
                ->setSurface($faker->numberBetween(40,240))
                ->setRooms($faker->numberBetween(2,11))
                ->setBedrooms($faker->numberBetween(1,7))
                ->setFloor($faker->numberBetween(1,5))
                ->setPrice($faker->numberBetween(100000,2800000))
                ->setHeat($faker->randomElement([0,1]))
                ->setCity($faker->city)
                ->setTitle($faker->randomElement(['Maison','Appartement']).' à '.$property->getCity())
                ->setAddress($faker->address)
                ->setPostalCode((int) $faker->postcode)
                ->setSold($faker->boolean());
            $manager->persist($property);
        }
        
        $manager->flush();
    }
}
```

A chaque tout de la boucle nous créons donc une nouvelle instance de `Property` et nous définissons ces différentes propriété grâce aux méthodes de `Faker`. Une fois réalisé nous persistons les données car il s'agit d'une entité encore inconnue de notre base de données. Puis une fois la boucle terminé nous pouvons enregistrer les changements apportés dans notre base de données.

## Exécuter notre fixture

Pour exécuter le code de notre fixture il n'y a pas plus simple il suffit d'utiliser l'instruction suivante dans notre console :

`php bin/console doctrine:fixtures:load`

Et l'ensemble des fixtures seront exécutées. Vous n'avez plus qu'à recharger votre page web et le miracle est accompli =)

## Conclusion

Les fixtures représentent un moyen simple et efficace pour générer des données pour notre application lors du développement. Couplé au package `Faker` de composer cela facilite le travail de façon considérable et plus proche de ce que l'on pourrait retrouver dans la réalité.
