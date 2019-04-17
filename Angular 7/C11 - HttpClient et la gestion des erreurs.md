# CHAPITRE XI : HttpClient et la gestion des erreurs

Dans le chapitre précédent nous avons vu comment utiliser le module `HttpClient` et ainsi réaliser des requêtes HTTP pour échanger des données avec un serveur distant. Dans ce chapitre nous allons voir comment gérer les erreurs avec ce même module et ainsi éviter les problétiques qui pourrait causer des dysfonctionnement à notre application.

## L'objet error

Nous allons travailler avec la méthode `getDevicesState` qui nous permet de récupérer nos différents devices et leurs états dans notre service `DevicesService`. Pour rappel le code de cette méthode est pour le moment le suivant :

```typescript
	getDevicesState():void {
		const observer = {
			next: (data) => {
				this.devices = data;
				this.emitDevicesSubject();
				observer.complete();
			},
			error: (error) => {
				console.error('An error has occured ' + error);
			},
			complete: () => {
				request.unsubscribe();
			}
		}

		let request = this.HttpClient
		.get('https://learning-app-2320e.firebaseio.com/devices.json')
		.subscribe(observer);
	}
```
Je vous proprose de changer l'adresse en retirant l'extension de notre fichier et ainsi créer une erreur avec notre requête. ce qui donne la ligne suivante :

```typescript
		.get('https://learning-app-2320e.firebaseio.com/devices')
```

Maintenant tentons d'afficher nos différent devices dans la page correspondante en sachant que ceux-ci n'apparaitrons évidemment pas. Puis regardons dans la console voir ce que celle-ci affiche : `An error has occured [object Object]`. En effet l'obervable retourne la fonction correspondant au cas d'erreur et celle-ci indique que `error` placé en paramètre est un objet. Ce que je vous propose c'est de supprimer le texte et ainsi afficher directement cet obbjet dans notre console afin d'en savoir plus sur celui-ci. Pour cela modifions la ligne consernée comme suit : 

```typescript
			error: (error) => {
				console.error(error);
			},
```

Rechargeons la page et allons voir ce qu'affiche maintenant notre console sur cet objet. On peut constater que l'objet retourné est une instance de `HttpErrorResponse` qui contient plusieur propriétés dont un objet `error`. Nous avons trois autre propriétés également suceptibles de nous intéressées dont :

* `message` : Contient l'information que l'erreur provient d'une requête HTTP, l'adresse url et le texte et le code d'erreur.
* `status` : Contient uniquement le code de l'erreur HTTP.
* `statusText` : Contient uniquement le texte correspondant à l'erreur HTTP.
* `url` : Contient l'url de la requête ayant causée la problèmatique.

Grâce à ces quelques informations nous allons pouvoir afficher les informations que nous souhaitons et ainsi débuger plus facilement les erreurs relatives au requêtes HTTP.D'ailleurs testont un peu ses informations en changeant l'adresse URL de notre requête par une adresse inexistante et je vous laisse le choix pour déterminer laquelle vous utiliserez. Personnelement j'utiliserais celle-ci `.get('https://learning-app-2320e.firebaseio.com/devices.nuts')`. Lançons notre application et allons voir ce qu'il se passe dans la console :

```javascript
HttpErrorResponse {headers: HttpHeaders, status: 404, statusText: "Not Found", url: "https://learning-app-2320e.firebaseio.com/devices.nuts", ok: false, …}
error: "not found"
headers: HttpHeaders {normalizedNames: Map(0), lazyUpdate: null, lazyInit: ƒ}
message: "Http failure response for https://learning-app-2320e.firebaseio.com/devices.nuts: 404 Not Found"
name: "HttpErrorResponse"
ok: false
status: 404
statusText: "Not Found"
url: "https://learning-app-2320e.firebaseio.com/devices.nuts"
__proto__: HttpResponseBase
```

On comprend mieux d'ou provient l'erreur, ici une requête vers un fichier inexistant donc une erreur 404. Pour nous faciliter la tâche on pourrait afficher directement ce code d'erreur dans notre console. D'ailleurs nous utiliserons la propriété `message` qui contient toutes les informations dont nous avons besoin.

```typescript
			error: (error) => {
				console.error(error.message);
			},
```

Voilà qui pourra nous être d'une grande utilité !

## Créer un gestionnaire d'erreur

Je vous propose pour nous exercer de créer un petit gestionnaire d'erreur que nous appelerons `handleError` et qui sera tout simplement une fonction de notre classe correspondant à notre service. Ce gestionnaire devra indiquer si l'erreur est retournée par le serveur avec un code associé ou si simplement celui-ci n'a pas répondu. Voici ce que je vous propose :

```TypeScript
	private handleError(error: HttpErrorResponse) {
		if (error.status >= 300 && error.status < 500) {
			console.error('Un erreur est survenue avec le serveur : \n' + error.message);
		} else {
			console.error('impossible d\'établir une connection avec le serveur.');
		}
	}
```

Je ne pense pas avoir besoin de réellement détailler cette fonction parle d'elle-même si vous suivez ce cours correctement depuis le départ. Vous noterez l'ajout du mot clé `private` que vous pouvez d'ailleurs ajouter à l'ensemble des propriétés et méthodes de notre classe sauf `deviceSubject` vous l'aurez compris. Vous savez maintenant comment gérer les erreurs que vous êtes suceptible de rencontrer. Si vous ne comprenez pas à quoi peuvent bien correspondre les codes d'erreur HTTP je vous conseille de faire un tour sur le web car sans cette notion fondamentale vous risquez d'être régulièrement perdu.

## Utiliser le pipe retry()

RxJS dispose d'un opérateur permettant de réitérer une action dans le cas ou celle-ci souléverait une erreur. Pour cela on utilise l'opérateur `retry()` qui ne fonctionne que si l'observable retourne une erreur, il prend en paramètre le nombre d'essais à effectuer. Voici comment nous pourriont l'utiliser :

```typescript
		let request = this.HttpClient
		.get('https://learning-app-2320e.firebaseio.com/devices.jon')
		.pipe(
			retry(3)
		)
		.subscribe(observer);
```

Pensez bien à importer l'opérateur `retry` depuis `rxjs/operators`.
Cela fonctionne bien, cependant nous avons un petit problème, le message d'erreur d'affiche autant de fois que la requête est soumise et dans notre cas 3 fois. Nous allons modifier ce comportement en utilisant `catchError()` qui comme `retry()` est un pipe à importer depuis les opérateurs RxJS. Cet opérateur nous permet de gérer les cas d'erreur comme dans notre observer, mais à une différence prêt, il ne sera appelé qu'après le troisième essai si celui-ci soulève également une erreur. Attention donc à l'ordre des pipes. `catchError()` prend en paramètre la méthode à appeler en cas d'erreur et y assigne en paramètre l'object `error`. Cependant la méthode appelée doit obligatoirement retourner un observable `throwError` qui prend au moins une chaîne de caractères en paramètre. Voici dans un premier temps comment modifier notre gestionnaire d'erreur à cet effet :

```typescript
	private handleError(error: HttpErrorResponse) {
		if (error.status >= 300 && error.status < 500) {
			console.error('Un erreur est survenue avec le serveur : \n' + error.message);
		} else {
			console.error('impossible d\'établir une connection avec le serveur.');
		}

		return throwError('Une erreur est survenue');
	}
```

Puis notre méthode `getDevicesState` :

```typescript
	getDevicesState():void {
		const observer = {
			next: (data) => {
				this.devices = data;
				this.emitDevicesSubject();
				observer.complete();
			},
			error: (error) => {				
			},
			complete: () => {
				request.unsubscribe();
			}
		}

		let request = this.HttpClient
		.get('https://learning-app-2320e.firebaseio.com/devices.nuts')
		.pipe(
			retry(3),
			catchError(this.handleError)
		)
		.subscribe(observer);
	}
```

Vous noterez la disparittion du gestionnaire d'erreur dans l'observer. Maintenant notre code fonctionne comme nous le souhaitons c'est à dire qu'en cas d'erreur celui-ci fera appelle 3 fois à la requête, ci cela ne fonctionne pas alors il lancera une erreur et l'affichera en fonction de son status avec notre gestionnaire d'erreur `handleError`.

## Conclusion

Il est particulièrement important de prévoir les erreur notamment quanc cela concerne les requêtes HTTP car celles-ci sont communes et peuvent parfois causer des erreurs complexes à résoudre surtout lorsque l'on manque d'informations sur leurs causes. Ainsi vous savez maintenant comment gérer les différents erreurs que vous rencontrerez. Sachez également que bon nombre d'erreurs sont gérables de façon similaire avec les observable alors restez précautionneux et attentifs.
