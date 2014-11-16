
---
layout: post
title: "Redeemable promises avec Play Framework"
date: 2014-11-15 22:34
comments: true
published: true
categories: [async, playframework]
---

Le design pattern *promise* est désormais répandu dans l'informatique pour résoudre le [*callback hell*](http://callbackhell.com/) -l'enfer des callbacks-, problème très fréquent avec la programmation asynchrone. En effet, votre code est exécuté au sein de *callbacks* en réponse à des évènements : fin de traitement, lecture d'un fichier, arrivée d'un message par Web Socket, ...

On trouve des implémentations de pattern naturellement en [Javascript](https://www.promisejs.org/) ([plusieurs même](https://docs.angularjs.org/api/ng/service/$q)), mais aussi en [Scala](http://docs.scala-lang.org/overviews/core/futures.html), en [Java](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) (quel nom !), ...

Récemment, j'ai eu à intervenir sur un programme écrit en Play Framework v2.3, dont le rôle est assez simple : faire passe-plat entre un client et un serveur et effectuant, entre autres, des transformations protocolaires (REST vers TCP par exemple).

On peut représenter les échanges de messages entre les différents systèmes avec le diagramme de séquence suivant :

{% img http://www.websequencediagrams.com/cgi-bin/cdraw?lz=dGl0bGUgdGl0cmUKCkNsaWVudC0-SW50ZXJmYWNlOm1lc3NhZ2UKAAkJLT5TZXJ2ZXIAEQkACQYtACkMIGFjawAnCy0-AFIGABIGCm5vdGUgb3ZlciAAaQYsAGUJLABUByBQbHVzIHRhcmQsIHVuIAB9ByBkZSByZXRvdXIKAG0IAGgMcsOpcG9ucwCBHg0AcAgAFAg&s=modern-blue %}

Le programme en Play Framework est nommé "Interface" dans ce diagramme.

Si les messages montants ne posent pas de difficulté particulière, le retour du "Server" est asynchrone, que l'équipe de développement avait implémentée avec des *callbacks*. Voici un exemple de pseudo-code Java :


``` [java]

/**
 * Receive responses from "server"
 */
public class ServerReceiver {

	private Client client;

	...

	public void onServerResponse(Response response) {
		client.sendResponse(response);
	}

	public void onServerError(Throwable exception) {
		client.sendError(exception);
	}

}

```

