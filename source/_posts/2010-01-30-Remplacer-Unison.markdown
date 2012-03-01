--- 
layout: post
title: Remplacer Unison
categories: 
- Ubuntu
permalink: /archives/54-Remplacer-Unison.html
s9y_link: http://www.dlecan.com/archives/54-Remplacer-Unison.html
date: 2010-01-30 22:01:54 +01:00
---
J'utilise depuis pas mal de temps <a href="http://doc.ubuntu-fr.org/unison" title="Doc Ubuntu pour Unison">Unison</a> pour synchroniser mes clés USB avec un répertoire local sous Ubuntu.<br />
<br />
L'outil fonctionne bien, répond à mes besoins. Mais quel est le problème alors ?<br />
<br />
Dans ma famille, il n'y a que moi qui réussisse à l'utiliser parce qu'il a quand même de gros défauts, que j'énumère par la suite.<br />
<br />
<ul><li><strong>L'interface est en anglais exclusivement</strong></li></ul><br />
Ca ne dérange pas vraiment, mais je comprends que ça puisse être un frein à son utilisation, malgré la bonne documentation du site <a href="http://doc.ubuntu-fr.org/unison" title="Doc Ubuntu pour Unison">Ubuntu-fr</a>.<br />
Après quelques recherches, Unison n'est apparemment pas disponible dans d'autres langues.<br />
<br />
Je me dis que c'est de l'open-source et que je n'ai qu'à le traduire moi-même. Un petit tour sur le site originel d'<a href="http://www.cis.upenn.edu/~bcpierce/unison/" title="Site d'Unison">Unison</a> me permet de télécharger le code source et de tomber sur de ... l'OCaml ?!?!<br />
<a href="http://fr.wikipedia.org/wiki/Objective_Caml">Wikipedia</a> me dit que c'est un super langage de programmation, mais que je ne connais pas.<br />
<br />
Je finis par tomber le fichier qui programme l'interface : ~4300 lignes de code !<br />
Toute l'interface en un seul fichier. OCaml c'est bien apparemment, mais la mise en oeuvre sur l'interface pourrait être améliorée.<br />
<br />
Visiblement, rien a été prévu pour gérer l'internationalisation de l'interface, je laisse tomber.<br />
<br />
<ul><li><strong>L'interface est particulièrement limitée et ne représente absolument les fonctionnalités de l'outil</strong></li></ul><br />
L'interface graphique d'Unison ne permet pas de configurer la façon dont les synchronisations vont s'effectuer. Il faut passer par l'édition d'un fichier texte avec <a href="http://wiki.mandriva.com/fr/unison">la doc sous la main</a>, mais impossible de donner ça à ma famille.<br />
<br />
Et faire évoluer l'interface est bien plus difficile que de la traduire, tâche que j'ai abandonnée ...<br />
<br />
Il est vraiment temps de trouver un remplaçant !
