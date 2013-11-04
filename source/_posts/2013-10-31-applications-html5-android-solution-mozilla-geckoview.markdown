
---
layout: post
title: "Applications HTML5 sur Android : la solution Geckoview de Mozilla"
date: 2013-10-31 16:35
comments: true
published: true
categories: [html5, mozilla, android]
---

Si les débats "applications natives" vs "applications HTML5" sont toujours aussi agités, c'est avant tout parce qu'aucune des deux approches ne se démarquent significative de l'autre : le natif a principalement pour lui l'avantage de la performance ou de la réactivité et le HTML5 celui de la portabilité (dans ses grandes lignes) et donc potentiellement celui de coûts de développement plus réduits à mesure que le nombre de plateformes à supporter grandit.

La présence des applications dans les "stores" étant un pré-requis désormais, une application HTML5 doit donc être "packagée", c'est-à-dire encapsulée dans un lanceur écrit en code natif. Elle est alors exécutée dans ce que l'on appelle une *Webview*, dont on peut paramétrer finement le comportement : activer ou non le zoom, taille du cache HTML5, gestion fine de la sécurité, ...


Voici une synthèse des *webviews* disponibles selon les plateformes :

- Windows 8.x : IE 10/11 dans un mode d'exécution adapté à une application HTML5
- iOS : Safari, amputé de son moteur d'exécution Javascript performant
- Firefox OS : Firefox dans un mode d'exécution adapté à une application HTML5
- Android <= 4.3.x : un "truc" vaguement basé sur Chromium, le moteur partagé avec Chrome, peu comptible avec HTML5 : WebGL, WebRTC, Websockets, SSE, ... sont manquants
- Android >= 4.4 : [une image de Chrome pour Android](chrome-as-webview), mais qui, a priori, se met à jour uniquement avec Android lui-même 


Que valent ces navigateurs ?

Prenez une application HTML5 qui a été conçue pour se faire passer pour du natif, c'est-à-dire reposant massivement sur des animations, des rotations, des effets de fondus, d'apparition avec dégradés, du défilement infini ... comme j'ai peu en voir une récemment.

{% pullquote %}
{" Sur iPad 4, les performances du HTML5 sont très proches de celles du natif. "} Il faut pratiquer longuement deux versions pour les différencier et se rendre compte petit à petit que la version HTML5 est un peu moins fluide, un peu moins réactive, que quelques animations saccadent, ... Pour cette application, inutile de faire du natif sur cette plateforme. Sur l'iPad 5 Air que je viens de tester, ces différences ne sont plus notables.
{% endpullquote %}

Sur une vraie tablette Windows 8 Lenovo, j'ai été très, mais vraiment très surpris des excellentes performances d'IE10. Elles sont du niveau de celles d'iOS. Les résultats du travail de Microsoft sont là, HTML5 est une plateforme de développement crédible sur Windows 8.

Enfin sur des tablettes Android (Nexus 10, Notes 10.1), ce fut la douche froide : saccades, blocages, disparitions purement et simplement de certaines animations (trop lentes pour être affichées ?), malgré de nombreux efforts pour ajouter des optimisations, des accélérations matériel, ... sans succès notable. L'application est inutilisable, son ergonomie doit être revue à la baisse pour cette plateforme.

Voici un extrait d'un [rapport de bug Android](issue-chrome-as-webview) qui résume bien la problématique : 

{% blockquote Jason http://code.google.com/p/chromium/issues/detail?id=113088#c12 %}
Webview for android is the Internet Explorer of the mobile world.
{% endblockquote %}

{% blockquote Jason http://code.google.com/p/chromium/issues/detail?id=113088#c12 %}
La *Webview* d'Android est l'Internet Explorer du monde mobile.
{% endblockquote %}

En effet, comme Android est aujourd'hui [la plateforme mobile dominante sur le marché](pdm-android), une stratégie de développement d'applications "sexies" basée sur HTML5 vous expose donc à l'ire et aux complaintes de vos utilisateurs équipés de terminaux Android. Et même si le moteur de la *webview* a été mis à jour dans Android 4.4 (très proche de Chrome pour Android), des fonctionnalités importantes de HTML5 ne sont pas encore disponibles (WebGL, WebRTC, ... Websockets ?) et il faudra des années à cette nouvelle version pour être suffisamment déployée.


En 2013, résoudre ce problème sur Android se résume donc très simplement : embarquer un moteur HTML performant directement **dans** les applications HTML5, sous forme d'une librairie statique. Le moteur HTML5 fournit avec la version d'Android livrée avec votre tablette ou votre smartphone ne serait ainsi plus utilisée, remplacé par un Chrome ou un Firefox performant.

Voyons donc les [travaux proposées](wip-static-webview) qui me paraissent les plus prometteurs :

- Chromium/Chrome en tant que nouvelle *webview*, soit en tant que [projet Google](google-chromium-static-library) ou en tant que [projet "communautaire"](communautary-chromium-static-library)
- Le projet [Geckoview](geckoview) de Mozilla

{% pullquote %}
C'est surtout le deuxième qui me semble le plus prometteur, parce qu'il ne se restreint pas à la seule plateforme Android. {" L'ambition de ce projet est de fournir une *webview* basée sur Firefox pour toutes les plateformes, d'aujourd'hui ou à venir, mobiles ou non "} : Android, Windows, Linux, ... Seul [iOS fait exception](firefox-apple-restrictions) car les restrictions imposées par Apple ne permettent pas de déployer Firefox dans de bonnes conditions. Mais c'est sans conséquence pour une pplication HTML5, iOS etant une plateforme de choix pour exécuter du HTML5 comme nous l'avons déjà évoqué plus haut. Cerise sur le gâteau, cette Geckoview est compatible avec des versions d'Android 2.x/3.x et toutes les fonctionnalités HTML5 avancées disponibles pour Firefox sont aussi utilisables dans une *webview* : à vous les websockets, WebGL, ...
{% endpullquote %}

Le projet est très actif et il est dores et déjà possible de monter des prototypes avec [le code mis à disposition par l'équipe de développement](geckoview-static-library), qui propose [un navigateur basé sur cette Geckoview](geckbrowser). Pour l'avoir déjà mis en oeuvre sur un projet à titre de démonstration, c'est très, très prometteur : sur la fameuse application qui veut se faire passer pour du natif dont je vous ai parlé plus haut, les performances du HTML5 sont très proches de celles du natif ! Quelques


Alors, Geckoview est-il l'avenir du HTML5 sur Android ? Compte-tenu du fait qu'Android 4.4 ne résoud que partiellement la problématique, la solution qui consiste à embarquer un nouveau moteur de *webview* directement dans nos applications HTLM5 à de l'avenir. Et sur ce point, Mozilla est bien parti.


[pdm-android]: http://techcrunch.com/2013/08/07/android-nears-80-market-share-in-global-smartphone-shipments-as-ios-and-blackberry-share-slides-per-idc/
[issue-chrome-as-webview]: https://code.google.com/p/chromium/issues/detail?id=113088
[chrome-as-webview]: http://developer.android.com/about/versions/kitkat.html#44-webview
[wip-static-webview]: https://code.google.com/p/chromium/issues/detail?id=113088#c86
[google-chromium-static-library]: http://code.google.com/p/chromium/issues/detail?id=113088#c100
[communautary-chromium-static-library]: https://github.com/davisford/chromeview
[geckoview-static-library]: https://wiki.mozilla.org/Mobile/GeckoView
[firefox-apple-restrictions]: https://support.mozilla.org/fr/kb/firefox-disponible-pour-iphone-ipad
[geckbrowser]: https://github.com/mfinkle/geckobrowser