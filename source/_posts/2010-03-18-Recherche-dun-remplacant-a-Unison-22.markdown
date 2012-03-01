--- 
layout: post
title: "Recherche d'un remplaçant à Unison (2/2)"
categories: 
- Ubuntu
permalink: /archives/57-Recherche-dun-remplacant-a-Unison-22.html
s9y_link: http://www.dlecan.com/archives/57-Recherche-dun-remplacant-a-Unison-22.html
date: 2010-03-18 09:29:57 +01:00
---
<p><a href="http://www.dlecan.com/archives/55-Recherche-dun-remplacant-a-Unison-12.html">Ma recherche d'un outil de synchronisation de fichiers intégré à Ubuntu n'ayant rien donné</a>, je me suis résigné à chercher un outil non empaqueté pour Ubuntu, avec l'espoir, soit d'en trouver un multi-plateforme, ce qui augmente sa communauté d'utilisateurs et donc sa pérennité (mais sans aucune garantie dans le temps effectivement), soit uniquement dédié à Ubuntu.<br /></p> <br />
<p>Je trouve rapidement <a href="http://en.wikipedia.org/wiki/File_synchronization">une page sur Wikipedia</a> qui liste quelques produits open-source et multi-plateforme. Parmi les huit candidats, six semblent multi-plateforme, et parmi ceux-ci deux sortent du lot : <a href="http://synkron.sourceforge.net/index.php">Synkron</a> et <a href="http://sourceforge.net/projects/freefilesync/">FreeFileSync</a>, que je teste immédiatement.</p> <br />
<ul> <br />
<li>&#160;Synkron&#160;</li> <br />
</ul> <br />
<div align="justify"> <br />
<p>L'interface de l'outil est sympathique, les fonctionnalités au rendez-vous et il est disponible en français. Des binaires sont disponibles pour Windows, Mac OS et, avec un peu de recherche, aussi pour Linux sous forme de RPM (pour Mandriva par exemple) ou DEB (pour Ubuntu ou Debian). Il est à noter que les paquets pour Linux sont un peu en retard sur les autres.</p> <br />
<p>Un des avantages de Linux est de ne pas à faire soi-même les mises à jour, et, même si les outils peuvent embarquer en interne un système de mise à jour, il est nettement plus efficace qu'un logiciel soit disponible dans un dépôt plutôt qu'en téléchargement quelque part. Les mises à jour sont automatiques et automatisées pour l'utilisateur.</p> <br />
<p>Je recherche donc un PPA Ubuntu ou un effort d'intégration dans les dépôts officiels Ubuntu. Je trouve <a href="https://launchpad.net/%7Esportman1280/+archive/ppa">ce PPA</a>, mais Synkron n'y est plus mis à jour. Je trouve aussi <a href="http://revu.ubuntuwire.com/details.py?upid=3821">une page REVU témoignant de l'effort d'intégration</a> de Synkron dans Ubuntu, mais qui n'a pas abouti.</p> <br />
<p> <u>Bilan</u> : un outil intéressant, mais disponible sur Ubuntu <em>à-la-Windows</em> (installe et mets à jour toi-même).</p> <br />
<ul> <br />
<li>FreeFileSync</li> <br />
</ul> <br />
<p>En français, l'interface est un peu moins professionnelle que celle de Synkron, mais elle me plaît immédiatement. Les options avancées sont masquées par défaut à l'utilisateur, ce qui correspond bien à mon besoin d'un outil simple et compréhensible.</p> <br />
<p>Il est multi-plateforme Windows, Mac OS et Linux mais réellement empaqueté seulement pour Windows et Mac OS. Linux doit se contenter de binaires &quot;portables&quot;, c'est-à-dire que l'installation s'effectue par extraction d'un fichier zip : idéal pour une clé USB mais pas de paquet DEB.</p> <br />
<p>La lecture du forum m'apprend qu'un paquet deb est régulièrement demandé, mais sans succès.</p> <br />
<p> <u>Bilan</u> : un outil prometteur qui mériterait d'être empaqueté pour Ubuntu.</p> <br />
<p> </p> <br />
<p>N'ayant aucune connaissance en création de paquet pour Ubuntu, je me suis dit que c'était l'occasion idéale d'apprendre. <em>Let's go</em> pour <a href="http://sourceforge.net/projects/freefilesync/">FreeFileSync</a>, empaqueté pour Ubuntu !<br /></p> <br />
</div>
