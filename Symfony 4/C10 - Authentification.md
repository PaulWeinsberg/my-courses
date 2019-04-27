# CHAPITRE X : AUTHENTIFICATION

Nous avons vu dans les précédents chapitre comment créer une petite interface d'administration simple mais assez efficace. Nous y avons découvert de nombreux principes de Symfony qui nous permettent d'ors et déjà d'avoir une vue plus globale du framework. Ce que je vous propose de réaliser maintenant pour que notre application soit plus proche d'une application réelle, c'est bien evidemment de rendre notre interface d'administration accessible qu'à certain utilisateurs.

Pour cela nous allons découvrir le service `Security` de Symfony mais dans un premier temps nous allons déjà réaliser ce que nous savons faire afin de préparer le terrain. Ce dont nous allons avoir besoin :

* Une entité pour sauvegarder nos utilisateurs en base de données.
* Un formulaire d'enregistrement pour chacun des utilisateurs.
* Un formulaire pour nous connecter.
* Définir des droits à ces utilisateurs.
* Proteger les routes de l'interface d'administration.

Voilà grosso-modo ce que nous savons dès à présent alors nous allons mettre en place ce qu'il est pour les moment de notre ressort.

## Mise en place des prérequis

Dans ce sous-chapitre nous allons donc mettre en place :

* Un entité `User` comportant les informations de chacun des utilisateurs.
* Un formulaire d'enregistrement.
* Un formulaire de connexion.

### L'entité User

Notre entité devra comporter : 

* `username` : Le nom d'utilisateur
* `mail` : Le email
* `password` : Le mot de passe

Vous savez maintenant comment créer une entité alors rendez-vous dans votre console et taper la ligne suivante :

`php bin/console make:entity`

Pour ce qui est du type de champ et de leur valeur je vous fait confiance vous devriez faire des choix simples et logique. Un fois notre entité créée il nous faut la sauvegarder dans notre base de données :

`php bin/console make:migration`

Vérifier si votre entité et votre repository contiennent bien ce que vous attendez et vérifier également le contenu de la migration puis exécutez cette migration avec la commande :

`php bin/console doctrine:migration:migrate`

Voilà notre entité créée dans notre base de données et dans nos fichiers. Il nous faut maintenant créer les route correspondantes au login ainsi qu'à l'enregistrement d'un nouvel utilisateur.

### Les routes

La première route à créer sera celle pour créer un utilisateur nous l'appelerons `registration`. La seconde sera pour nous connecter et nous l'appellerons `auth`. Ces deux routes renverrons vers le controleur `App\Controller\Authentification`.

Dans le fichier `routes.yaml`

```yaml
registration:
  path: /registration
  controller: App\Controller\AuthentificationController::registration

auth:
  path: /auth
  controller: App\Controller\AuthentificationController::auth
```

### Le formulaire d'inscription

Avant de créer nos controleurs et nos templates nous allons créer nos formulaires. Rendons nous directement dans notre terminal et tapons la commande suivante :

`php bin/console make:form`

Evidement le formulaire s'appellera `UserType` et se basera sur l'entité `User`.
Une fois créé nous pouvons allez dans notre controleur et définir la fonciton permettant de générer notre formulaire ce qui nous donnera le code suivante :

```php
<?php
namespace App\Controller;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use App\Entity\User;
use App\Form\UserType;
use Doctrine\Common\Persistence\ObjectManager;
use Symfony\Component\HttpFoundation\Request;

class AuthentificationController extends AbstractController {

	private $_entityManager;

	public function __construct(ObjectManager $entityManager) {
		$this->_entityManager = $entityManager;
	}	

	public function registration(Request $request): Response {

		$user = new User();
		$form = $this->createForm(UserType::class,$user);
		$form->handleRequest($request);
		
		if ($form->isSubmitted() && $form->isValid()) {

			$this->_entityManager->persist($user);
			$this->_entityManager->flush();
			$this->addFlash('success','Vous pouvez dès à présent vous connecter.');
			return $this->redirectToRoute('auth');

		} else {

			return $this->render('pages/registration.html.twig', [
				'form' => $form->createView()
			]);

		}

	}
```

Maintenant que nous avons ceci c'est au tour du template, pour l'afficher nous allons utiliser exactement le même theme et les memes principe que ceux que nous avions utilisés dans notre template pour l'édition d'un bien :

```html
{% extends 'layouts/std-layout.html.twig' %}
{% form_theme form with ['bootstrap_4_layout.html.twig'] %}
{% block content %}
	{{ form_start(form) }}
		{{ form_row(form.username) }}
		{{ form_row(form.mail) }}
		{{ form_row(form.password) }}
		<button type="submit" class="btn btn-primary float-right">S'enregistrer</button>
	{{ form_end(form) }}
{% endblock %}
```

Vous pouvez dorénavant rafraichir votre page web et voir ce que cela donne. Il nous manque encore deux petites opération à réaliser même si techniquement cela fonctionnerai ainsi. Ce que nous aimerons ajouter :

* Une vérification du mot de passe
* Un traduction

Pour la traduction il nous suffit de le préciser dans la fonction `configureOptions()` comme nous l'avions fait précédemment et nous nous servirons du même fichier car il d'agît également d'un formulaire :

```php
    public function configureOptions(OptionsResolver $resolver)
    {
        $resolver->setDefaults([
            'data_class' => User::class,
            'translation_domain' => 'forms'
        ]);
    }
```

Maintenant c'est au tour de la vérification de l'entrée du mot de passe. Pour cela nous allons ajouter une propriété à notre classe `User` et dans le formulaire `UserType`. Mais qui ne sera pas annoté pour être prise en charge par l'ORM ainsi elle ne sera disponible que pour notre formulaire et ne sera en aucun cas enregistré dans la base de données.

Dans la classe `User` du fichier `User.php` :

```php
// du code...

		private $password_confirm;
		
// du code...

	    public function getPasswordConfirm(): ?string
    {
        return $this->password_confirm;
    }

    public function setPasswordConfirm(string $password_confirm): self
    {
        $this->password_confirm = $password_confirm;

        return $this;
    }
```

Dans le fichier `UserType.php` :

```php
        $builder
            ->add('username')
            ->add('mail')
            ->add('password')
            ->add('password_confirm')
        ;
```

Et enfin dans notre template `registration.html.twig` :

```html
		{{ form_row(form.password_confirm) }}
```

Vous pouvez maintenant rafraichir votre page et observer les modifications apportées. Pensez à ajouter la traduction également. Mais il nous manque encore quelques petits points à régler :

* Le password qui s'affiche en clair
* Les contraintes à appliquer

Pour le mot de passe nous avons deux solutions, soit nous ajoutons un paramètre dans la fonction `form_row()` pour indiquer qu'il s'agit d'un champ de type mot de passe soit nous ajoutons cecis directement dans le builder du formulaire et donc dans la classe `UserType`. Nous choisirons la seconde solution, car tous les formulaires devrons appliquer cette règle et nous n'aurons plus à répéter cela dans les templates utilisant cette classe. De plus cela nous évite les erreurs d'inatention. De plus il existe pour cela des extensions toutes faites comme `PasswordType` ou `EmailType` que nous pouvons ajouter en second paramètre de la méthode `add()` :

```php
	// du code...
    ->add('mail',EmailType::class)
    ->add('password',PasswordType::class)
    ->add('password_confirm',PasswordType::class)
	// du code...
```

Notre formulaire affiche maintenant les champs tel que nous le souhaitions. Il ne nous reste plus qu'à appliquer des contraintes pour :

* Que le nom d'utilisateur ne contienne que des lettre majuscule ou minuscule et minimum 3 et soit unique à chaque utilisateur.
* Que le mot de passe contienne au moins 1 chiffre, 1 lettre et 1 caractère spécial et soit égal au mot de passe de confirmation.
* Que l'adresse email soit valide.

En effet l'ajout des type comme nous venons de le faire ajoute des attributs côté navigation mais ne nous garantit en rien l'intégrité des données. Je vous laisse chercher les contraintes associciées à ce que nous avons défini sinon vous avez une version juste ci-dessous :

```yaml
App\Entity\User:
  constraints:
    - Symfony\Bridge\Doctrine\Validator\Constraints\UniqueEntity: mail
    - Symfony\Bridge\Doctrine\Validator\Constraints\UniqueEntity: username
  properties:
    username:
      - Length: { min: 3, max: 40 }
      - Type: alpha
    mail:
      - Email: { mode: html5 }
    password:
      - Regex: { pattern: "/(?=.*[a-z])(?=.*[A-Z])(?=.*[0-9])(?=.*[^a-zA-Z0-9]).{8,}/", match: true, message: 'Le mot de passe doit contenir au moins une lettre minuscule, un chiffre, un caractère spécial et minimum 8 caractères' }
    password_confirm:
      - EqualTo: { propertyPath: password, message: 'Les deux valeurs des champs mots de passe doivent être identiques' }
```

Nous avons ajouté nos vérifications ainsi que nos contraintes d'unicité sur les deux champs nécessaires. Comme vous pouvez le voir les contraintes d'unicité ne s'ajoutent pas directement dans la base de données mais directement au niveau du code PHP. Est-ce une bonne ou mauvaise chose ? Je vous laisse en juger par vous même.

### Sécuriser le mot de passe

Vous voyez le problème ? Nos mots de passe apparaissent pour le moment en clair dans notre base de données et ça c'est clairement interdit ! Vous l'avez compris si quelqu'un volle votre base de données il aura surement accès à de nombreux comptes utilisateurs, de plus, les utilisateurs se servent souvent des même mots de passe d'un compte à l'autre ce qui agrave le danger potentiel. Il nous faut donc crypter le mot de passe stocké dans notre base de données et nous allons voir comment procéder avec Symfony.

#### Définir un encodage

Avant d'utiliser le service nous permettant d'encoder le mot de passe il va nous falloir au préalable préciser l'encodage à utiliser. Avec Symfony on peut utiliser un encodage par entité ce qui est souple certes mais demande à être préciser à chaque fois que cela est nécéssaire.

Pour cela rendons-nous dans le fichier `config/packages/security.yaml`. Pour définir un encoder on utilise la syntaxe suivante :

```yaml
    encoders:
        App\Entity\User:
            algorithm: bcrypt
            cost: 12
```

Nous n'avons plus qu'à utiliser les méthodes de l'interface `UserPasswordEncoderInterface` du namespace `Symfony\Component\Security\Core\Encoder\UserPasswordEncoderInterface` pour encoder notre mot de passe dans notre controleur. Cette interface utilise une méthode `encodePassword()` qui prend en paramètre l'entité qui doit implémenter l'interface `UserInterface` ainsi que le mot de passe à crypter. Enfin, elle retourne le mot de passe crypté. Pour réaliser cette opération rendez-vous dans notre controleur et modifions notre méthode `registration()` en ajoutant l'interface en question:

```php
use Symfony\Component\Security\Core\Encoder\UserPasswordEncoderInterface;
// du code...
public function registration(Request $request, UserPasswordEncoderInterface $encoder): Response {
// du code...
```

Puis il nous faut dans un premier temps récupérer le mot de passe entré par l'utilisateur et le passer en second paramètre de la méthode `encodePassword()` puis mettre à jour la propriété `password` :

```php
// du code...
$encodedPassword = $encoder->encodePassword($user,$user->getPassword());
$user->setPassword($encodedPassword);
// du code...
```

Voilà qui est fait ! Notre controleur est prêt mais nous avions dit que notre classe User doit implémenter l'interface `UserInterface` ce qui pour l'instant n'est pas le cas. Rendons nous donc dans la classe de notre entité `User` et implémentons celle-ci.

#### Implémenter UserInterface

L'UserInterface permet avec Symfony de réellement créer un système d'utilisateurs fiable et contraint de contenir certaines méthodes dont :

* `getUsername` : Renvoie le nom de l'utilisateur. (Déjà implémenté)
* `getPassword` : Renvoie le mot de passe de l'utilisateur.(Déjà implémenté)
* `getSalt` : Renvoie le salt, c'est à dire une chaine de caractères à ajouter lors du cryptage.
* `getRoles` : Renvoie un tableau de d'objet héritant de Role ou de chaine de caractères correspondant aux différents rôles des utilisateurs.
* `eraseCredentials` : Renvoie l'entité après avoir enlevé certain informations confidentielles ou sensible. Ce qui peut être pratique dans le cas d'utilisation de fonciton type Ajax etc...

Vous commencer à voir venir le système d'authentification n'est-ce pas ? Mais pour l'instant ne nous attardons pas sur son fonctionnement et ajoutons simplement ces différentes méthodes dans notre `User` ainsi que l'interface correspondante :

```php
    public function getSalt() {}
    
    public function getRoles() {
        return ['ROLE_USER'];
    }

    public function eraseCredentials() {
        return $this;
    }
```

Pour l'instant nos fonction ne font vraiment pas grand chose mais nous verrons cela dans la suite de ce cours et retournons à notre formulaire pour voir si notre mot de passe s'enregistre maintenant comme nous le souhaitons c'est à dire au format encrypté.

C'est effectivement le cas ! Super on peut passer à la suite et realiser le formulaire de connexion de la route `auth`.

### Formulaire de connexion

Comme tout formulaire de connexion qui se respect, deux champs pas plus ! C'est une question d'expérience utilisateur =). Là vous avez le choix entre deux solutions car nous avons deux clés uniques. Soit nous utilisons l'adresse email soit le nom d'utilisateur. L'email est courante et facile à retenir cependant elle est longue à taper. Le nom d'utilisateur est moin long mais moins utiliser donc moins souvent retenu. Faites votre choix ici je pars pour le nom d'utilisateur.

Nous avons déjà la route pour `auth`il ne nous reste donc qu'à créer le template et la méthode du controleur pour que celui-ci s'affiche. Dans un premier temps nous allons simplement créer un nouvel utilisateur dans notre méthode sans ne récupérer aucune données mais rendre la route fonctionnelle. Nous n'aurons besoin que de deux champs et il faudra le préciser directement en créant un formulaire sur-mesure avec la méthode `createFormBuilder()`, elle prend en paramètre la classe du type de formulaire et en second paramètre optionnel les options puis dispose d'une méthode `add()` pour ajouter nos champs, `getForm()` pour renvoyer le formulaire et les méthode `setAction()` et `getForm()` qui respectivement indique l'action à effectuer et la méthode à utiliser.

```php
	public function auth(): Response {
		$user = new User();
		$form = $this->createFormBuilder($user,[
			'translation_domain' => 'forms'
		])
			->setAction($this->generateUrl('auth'))
			->setMethod('POST')
			->add('username')
			->add('password',PasswordType::class)
			->getForm();

		return $this->render('pages/auth.html.twig', [
				'form' => $form->createView()
			]);
	}
```

Et pour notre template :

```html
{% extends 'layouts/std-layout.html.twig' %}
{% form_theme form with ['bootstrap_4_layout.html.twig'] %}
{% block content %}
	{{ form_start(form) }}
		{{ form_row(form.username) }}
		{{ form_row(form.password) }}
		<button type="submit" class="btn btn-primary float-right">Se connecter</button>
	{{ form_end(form) }}
{% endblock %}
```

## L'authentification

### Configuration du services Security

Il nous faut maintenant configurer ce service que nous avons ouvert précédemment à cette adresse `config/packages/security`. En effet nous allons devoir préciser plusieurs choses necessaires au fonctionnement de l'authentification, en premier nous préciserons le service ou *provider* et en second le mode d'authentification *http type apache etc. ou formulaire* ici ce sera un formulaire.


Donc dans le champs `providers` nous allons indiquer la ou Symfony doit recherché loes données :

* Un nom `in_database` car c'est explicite. Puis il contiendra :
  * `entity` L'entité à laquelle il se refère ici qui contiendra :
    * `class` Lien vers la classe de référence ici `App\Entity\User`.
    * `property` La propriété de recherche de l'utilisateur ici `username`.

```yaml
in_database:
    entity:
        class: App\Entity\User
        property: username
```

Il faut ensuite préciser le nom de notre nouveau provider dans `main` c'est à dire principale car c'est ici que seront l'ensemble de nos utilisateurs.

```yaml
provider: in_database
```

Puis également dans `main` nous indiquerons le type de connexion souhaité et ici ce sera `form_login` qui prend plusieurs options dont :

* `login_path` : Qui contient le nom de la route à partir de laquelle les utilisateurs peuvent saisir leur informations de connexion.
* `check_path` : Le chemin sur lequel Symfony devra effectuer la vérification (peut être identique au login)
* `username_parameter` : Le nom donné à l'attribut `name` ici `[form]username`
* `password_parameter` : Le nom donné à l'attribut `name` ici `[form]password`
* `default_target_path` : Le nom de la route de redirection une fois connecté.

```yaml
form_login:
    login_path: auth
    check_path: auth
    username_parameter: form[username]
    password_parameter: form[password]
    default_target_path: home
```

Puis, toujours dans `main` nous préciserons simplement le système de `logout` et sa propriété `path` :

```yaml
logout:
    path: logout
```

Vous pouvez maintenant vous connecter et voir apparaitre votre nom d'utilisateur à la place de `anon` dans le profiler de Symfony !

Petite précisions, nous aurions pu simplifier énormement la chose mais je souhaitez vous faire travailler un peut sur les formulaires et vous montrer différentes options. En effet nous aurions très bien pu faire un formulaire HTML simple avec pour valeur de champs username `_username` et password `_password` qui sont les valeurs par defaut. Configurer le formulaire pour nous redirigerez vers l'adresse de la route pour le login. Ainsi au lieu de faire une fonction pour fabriquer un formulaire, nous aurions pu créer une simple fonction renvoyant le template du formulaire.

#### Boutons de connexion et déconnexion

Rendons la chose plus conviviale que de taper dans la barre d'adresse des URL à répétition. Ajoutons un bouton de connexion et de déconnexion dans notre barre de navigation et donc dans le template `std-layout.html.twig`. Nous avons en plus une petite variable globale de twig nous indiquant si un utilisateur est connecté ou non. Cette variable est `app.user` (en réalité elle récupère les informations de l'entité connectée):

```html
<div>
	{% if app.user %}
	  <a class="btn btn-sm btn-outline-light" href="{{ path('logout') }}">Déconexion</a>
	{% else %}
	  <a class="btn btn-sm btn-outline-light" href="{{ path('auth') }}">Connexion</a>
	{% endif %}			
</div>
```

Voilà qui est bien plus sympa !

### Controle d'accès

Rappelons-nous tout de même l'objectif initial de l'authentification : Restreindre certaine partie du site à une identification particulière et donc à un role précis. Pour l'instant le role de tous nos utilisateur connectés est `ROLE_USER` c'est donc pour ces utilisateurs que nous allons autoriser l'accès à toutes les routes `admin/` pour cela retour dans notre fichier `security.yaml` et indiquons les lignes suivantes directement dans la propriété `security` :

```yaml
    access_control:
        - { path: ^/admin, roles: ROLE_USER }
```

Maintenant tout individue non connecté ne pourra accéder à ces routes et sera automatiquement redirigé vers la page de login. 

## Conclusion

Vous savez maintenant créer et restreindre l'accès à une interface d'administration.

Pour aller plus loin nous pourrions améliorer ce système en ajoutant un role d'administrateur qui serait définit directement dans notre base de données avec un propriété admin qui renverrait un booléen et dont la modification via le framework serait impossible. Puis la méthode `getRoles()` renverrait l'ensemble des role dont `ROLE_ADMIN` dans le cas ou le booléen serait définit à `true`. Nous pourrions aussi créer une autre entité. En bref il existe plein de manière de gérer ces roles et d'attribuer des fonctionnalités à chacun d'eux.

