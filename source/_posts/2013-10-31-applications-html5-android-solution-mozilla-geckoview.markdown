
---
layout: post
title: "Applications HTML5 sur Android : la solution Geckoview de Mozilla"
date: 2013-10-31 16:35
comments: true
published: true
categories: [html5, mozilla, android]
---

Si les débats "applications natives" vs "applications HTML5" sont toujours aussi agités, c'est avant tout parce qu'aucune des deux approches ne se démarquent significative de l'autre : le natif a principalement pour lui l'avantage de la performance ou de la réactivité et le HTML5 celui de la portabilité (dans ses grandes lignes) et donc celui de coûts de développement plus réduits à mesure que le nombre de plateformes à supporter grandi.

La présence dans un "store" étant un pré-requis désormais, une application HTML5 dont donc être "packagée", c'est-à-dire encapsulée dans un lanceur écrit en code natif. Sont donc exclus les navigateurs autonomes disponibles sur chaque plateforme :

- Windows 8 : Firefox, Chrome, ...
- iOS : Safari
- Android : Chrome pour Android, Firefox, Opera, ...

Quels sont les moteurs HTML qui peuvent être instanciés dans une application HTML5 ?

- Windows 8.x : IE 10/11 dans un mode d'exécution adapté à une application HTML5
- iOS : Safari, amputé de son moteur d'exécution Javascript performant
- Firefox OS : Firefox dans un mode d'exécution adapté à une application HTML5
- Android : un "truc" vaguement basé sur Chromium, le moteur partagé avec Chrome, peu comptible avec HTML5

Que valent ces navigateurs ?

Prenez une application HTML5 qui a été écrite pour se faire passer pour du natif, c'est-à-dire reposant massivement sur des animations, des rotations, des effets de fondus, d'apparition avec dégradés, du défilement infini ... comme j'ai peu en voir une récemment.

Sur une iPad 4 / iOS 6, les performances du HTML5 sont très, très proches de celles du natif. Il faut pratiquer longuement deux versions pour les différencier et se rendre compte petit à petit que la version HTML5 est un peu moins fluide, un peu moins réactive, que quelques animations saccadent, ... Inutile de faire du natif sur cette plateforme.

Sur une tablette (une vraie) Windows 8 Lenovo, j'ai été très, mais vraiment très surpris des très bonnes performances d'IE10. Les performances sont du niveau de celles d'iOS. Les résultats du travail de Microsoft sont là, HTML5 est une plateforme de développement crédible sur Windows 8.

Enfin sur des tablettes Android (Nexus 10, Notes 10.1), ce fut la douche froide : saccades, blocages, disparitions purement et simplement de certaines animations (trop lentes pour être affichées ?), malgré de nombreux efforts pour ajouter des optimisations, des accélérations matériel, ... sans succès notable. L'application est inutilisable, son ergonomie doit être revue à la baisse pour cette plateforme.

Voici un extrait d'un [rapport de bug Android](chrome-as-webview) qui résume bien la problématique : 

{% blockquote Jason http://code.google.com/p/chromium/issues/detail?id=113088#c12 %}
Webview for android is the Internet Explorer of the mobile world.
{% endblockquote %}

{% blockquote Jason http://code.google.com/p/chromium/issues/detail?id=113088#c12 %}
La *Webview* d'Android est l'Internet Explorer [6] du monde mobile.
{% endblockquote %}

En effet, sachant qu'Android est aujourd'hui [la plateforme mobile dominante sur le marché](pdm-android), une stratégie de développement d'applications "sexies" basée sur HTML5 vous expose donc à l'ire et aux complaintes de vos utilisateurs équipés de terminaux Android.

Résoudre ce problème sur Android se résume donc très simplement : embarquer un moteur HTML performant directemnt **dans** les applications HTML5. Le moteur HTML5 fournit avec la version d'Android livrée avec votre tablette ou votre smartphone ne serait ainsi plus utilisée, remplacé par un Chrome ou un Firefox performant.

Voyons donc les [solutions proposées](efforts) qui me paraissent les plus prometteuses :

- [Chromium/Chrome en tant que nouvelle "Webview"](https://github.com/davisford/chromeview)
- Le projet [Geckoview](geckoview) de Mozilla




[pdm-android]: http://techcrunch.com/2013/08/07/android-nears-80-market-share-in-global-smartphone-shipments-as-ios-and-blackberry-share-slides-per-idc/
[chrome-as-webview]: https://code.google.com/p/chromium/issues/detail?id=113088
[efforts]: https://code.google.com/p/chromium/issues/detail?id=113088#c86
[geckoview]: https://wiki.mozilla.org/Mobile/GeckoView