
---
layout: post
title: "Redeemable promises avec Play Framework"
date: 2014-11-15 22:34
comments: true
published: true
categories: [async, playframework]
---

Récemment, j'ai eu à intervenir sur un programme écrit en Play Framework v2.3, dont le rôle est assez simple : faire passe-plat entre un client et un serveur et effectuant, entre autres, des transformations protocolaires (comme REST vers TCP par exemple). Ce qui m'a donné l'occasion d'utiliser les [RedeemablePromise](https://www.playframework.com/documentation/2.4.x/api/java/play/libs/F.RedeemablePromise.html) de Play Framework, pas du tout documentées à ce jour.

# Client asynchrone

Les différents échanges de messages entre les systèmes peuvent être représentés à l'aide du diagramme de séquence suivant :

{% comment %}
http://www.websequencediagrams.com/?lz=dGl0bGUgQ2xpZW50IGFzeW5jaHJvbmUKCgANBi0-UGFzc2VQbGF0OlBPU1QgbWVzc2FnZQoADgktPlNlcnZldXI6c2VuZAAXCQAOBy0ANQwgYWNrAC4LLT4AcQY6IDIwMAAXBQpub3RlIG92ZXIAgQsHLAB1CSwAXgggUGx1cyB0YXJkLCB1bgCBCAggZGUgcmV0b3VyCgByCQBuDHJlc3BvbnMAgSsNAHYIAIFTBQAYCQCBcQcAgSsNAIEYCACBMAwAgQcJYWNr&s=modern-blue
{% endcomment %} 

{% img http://www.websequencediagrams.com/cgi-bin/cdraw?lz=dGl0bGUgQ2xpZW50IGFzeW5jaHJvbmUKCgANBi0-UGFzc2VQbGF0OlBPU1QgbWVzc2FnZQoADgktPlNlcnZldXI6c2VuZAAXCQAOBy0ANQwgYWNrAC4LLT4AcQY6IDIwMAAXBQpub3RlIG92ZXIAgQsHLAB1CSwAXgggUGx1cyB0YXJkLCB1bgCBCAggZGUgcmV0b3VyCgByCQBuDHJlc3BvbnMAgSsNAHYIAIFTBQAYCQCBcQcAgSsNAIEYCACBMAwAgQcJYWNr&s=modern-blue %}

Le programme en Play Framework est nommé "PassePlat" dans ce diagramme.

Les messages montants sont implémentés sous forme d'une API RESTful ; tandis que le retour du "Serveur", asynchrone, est implémentée avec des *callbacks* et une requête REST vers le client d'origine. Voici un exemple de pseudo-code Java sur le traitement du message de retour :


``` [java]
...
/**
 * Receive responses from "server"
 */
public class ServerReceiver {

	public void onServerResponse(Response response) {
		// Utilisation de la librairie play.libs.ws.WS
		WS.url("http://client_url/api/response")
			.setContentType("application/json")
			.post(response.asJson());
	}

	public void onServerError(Throwable error) {
		WS.url("http://client_url/api/response")
			.setContentType("application/json")
			.post(... // error serialized as Json);
	}

}
...

```

Le système complet fonctionne bien, mais n'est pas très satisfaisant :

- le Client et le PassePlat sont très couplés. En effet, le Client doit connaître l'adresse du PassePlat pour lui envoyer les messages montants et le PassePlat doit connaitre l'adresse du Client pour lui renvoyer le message de retour. Bref, une dépendance cyclique. Donc bof ;

- Le fonctionnement du Serveur est asynchrone (messages montants et de retours sont décorrélés) et cette implémentation a déporté l'asynchronisme jusqu'au Client, alors que le client (l'humain cette fois-ci :-) voulait plutôt un fonctionnement synchrone du côté du Client, ce qui était plus facile à appréhender pour lui.

# Client synchrone

On a donc travaillé sur une implémentation du système plutôt comme ceci :

{% comment %}
http://www.websequencediagrams.com/?lz=dGl0bGUgQ2xpZW50IHN5bmNocm9uZQoKAAwGLT5QYXNzZVBsYXQ6UE9TVCBtZXNzYWdlCgAOCS0-U2VydmV1cjpzZW5kABcJAA4HLQA1DCBhY2sKCm5vdGUgb3ZlcgBuBywAWQksAEIIIFBsdXMgdGFyZCwgdW4AbAggZGUgcmV0b3VyCgBWCQBSDHJlc3BvbnMAgRAMLT4AgVMGOiAyMDAAGAkgKwCBBQUAgTATADsJQWNr&s=modern-blue
{% endcomment %}


{% img http://www.websequencediagrams.com/cgi-bin/cdraw?lz=dGl0bGUgQ2xpZW50IHN5bmNocm9uZQoKAAwGLT5QYXNzZVBsYXQ6UE9TVCBtZXNzYWdlCgAOCS0-U2VydmV1cjpzZW5kABcJAA4HLQA1DCBhY2sKCm5vdGUgb3ZlcgBuBywAWQksAEIIIFBsdXMgdGFyZCwgdW4AbAggZGUgcmV0b3VyCgBWCQBSDHJlc3BvbnMAgRAMLT4AgVMGOiAyMDAAGAkgKwCBBQUAgTATADsJQWNr&s=modern-blue %}

L'appel du Client vers le PassePlat est donc bloqué tant que l'acquittement **et** la réponse du Serveur ne sont pas parvenus au PassePlat.

## Comment ?

On peut faire cela très simplement avec les [RedeemablePromise](https://www.playframework.com/documentation/2.4.x/api/java/play/libs/F.RedeemablePromise.html) de Play Framework.

Les [RedeemablePromise](https://www.playframework.com/documentation/2.4.x/api/java/play/libs/F.RedeemablePromise.html) sont une implémentation du design pattern *promise*, désormais répandu dans l'informatique pour résoudre le [*callback hell*](http://callbackhell.com/) -l'enfer des callbacks-, problème très fréquent avec la programmation asynchrone. En effet, votre code est exécuté au sein de *callbacks* en réponse à des évènements : fin de traitement, lecture d'un fichier, arrivée d'un message par Web Socket, ...

On trouve des implémentations de pattern naturellement en [Javascript](https://www.promisejs.org/) ([plusieurs même](https://docs.angularjs.org/api/ng/service/$q)), mais aussi en [Scala](http://docs.scala-lang.org/overviews/core/futures.html), en [Java](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html), ...

Voyons comment nous pouvons les utiliser pour répondre à notre nouveau besoin.

Adaptons la classe qui réceptionne les évènements venant du Serveur :

``` [java]
...
/**
 * Receive responses from "server"
 */
public class ServerReceiver {

	private RedeemablePromise<Response> promise;

	// Constructeur masqué, qui permet d'assigner `promise`

	public void onServerResponse(Response response) {
		// La promesse est résolue ou complétée avec succès ici
		promise.success(response);
	}

	public void onServerError(Throwable error) {
		// La promesse est résolue ou complétée en erreur avec l'exception
		promise.failure(error);
	}

}
...
```

Jetons maintenant un coup d'oeil au contrôleur REST sur le PassePlat qui reçoit les requêtes HTTP depuis le Client et qui "bloque" tant que la réponse du Serveur n'est pas parvenue :

``` [java]
...
public static Promise<Result> sendMessage(...) {
	
	// Initialisation de la promesse, à vide
	RedeemablePromise promise = RedeemablePromise.empty();

	// Envoi du message entrant vers le serveur
	// server.send(message)

	ServerReceiver serverReceiver = new ServerReceiver(promise);

	server.listen(serverReceiver);

	// promise est du type <Response>, alors que Play attend un <Result>
	// on fait la conversion
	return promise.map(reponse ->
		ok(response.asJson());
	)
}
```
