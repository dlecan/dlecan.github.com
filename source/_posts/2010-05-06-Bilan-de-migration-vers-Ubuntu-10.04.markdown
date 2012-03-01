--- 
layout: post
title: Bilan de migration vers Ubuntu 10.04
categories: 
- Ubuntu
permalink: /archives/60-Bilan-de-migration-vers-Ubuntu-10.04.html
s9y_link: http://www.dlecan.com/archives/60-Bilan-de-migration-vers-Ubuntu-10.04.html
date: 2010-05-06 12:18:00 +02:00
---
<p>Je viens de migrer 3 PC que je gère de Ubuntu 9.10 vers Ubuntu 10.04. Globalement, tout s'est bien déroulé, je suis content des outils de migration de Canonical.</p> <br />
<p>Voici les différences/problèmes que j'ai rencontré et comment je les ai résolus :</p> <br />
<ul> <br />
<li>Migration partielle du thème graphique</li> <br />
</ul> <br />
<p>Alors que j'utilise partout le thème par défaut, la migration vers les nouveaux thèmes Ambiance ou Radiance ne s'effectue que partiellement : nouvelles icônes, mais anciennes couleurs par exemple.</p> <br />
<p>Un passage dans <em>Système</em>=&gt;<em>Préférences</em>=&gt;<span style="font-style: italic;">A</span><em>pparence</em>=&gt;<em>Thème </em>et une sélection du thème Ambiance ou Radiance rétablit la situation.</p> <br />
<ul> <br />
<li>Nouvelle gestion de l'ouverture des onglets dans Firefox 3.6</li> <br />
</ul> <br />
<p>Dans Firefox 3.6, les onglets ouverts sur un lien s'ouvrent désormais juste à côté de l'onglet courant. Cela permet de garder les onglets d'un même contexte groupés.</p> <br />
<p>Intéressant, mais habitué à l'ancien comportement, je l'ai rétabli en <a href="http://protuts.net/restaurer-comportement-ouverture-onglets-firefox-3/">suivant cette procédure</a>.</p> <br />
<ul> <br />
<li>Préférence des &quot;Effets visuels&quot;</li> <br />
</ul> <br />
<p>J'ai eu régulièrement des soucis avec cette préférence, qui change de valeur un peu comme elle veut en fonction des machines.</p> <br />
<p>Sur ma machine professionnelle, avec des pilotes graphique NVidia propriétaires (carte vidéo mobile trop récente), je n'ai jamais réussi à l'activer sur Ubuntu Karmic 9.10. Mais maintenant cela fonctionne sur Ubuntu Lucid 10.04 <img src="http://www.dlecan.com/templates/default/img/emoticons/smile.png" alt=":-)" style="display: inline; vertical-align: bottom;" class="emoticon" />.</p> <br />
<p>En revanche, sur mes deux autres machines (carte vidéo Intel et ancienne NVidia), tout fonctionnait très bien sur Ubuntu Karmic. Alors qu'avec Ubuntu Lucid les effets visuels sont désactivés à chaque fermeture de session. Je dois les réactiver manuellement. Pas de solution pour le moment, c'est juste ennuyant.</p> <br />
<ul> <br />
<li>Impossible d'utiliser UbuntuOne ou Gwibber</li> <br />
</ul> <br />
<p>Sur une de mes machines, UbuntuOne ou Gwibber (mais sûrement d'autres programmes sont affectés aussi) ne fonctionnent pas. Un obscur problème avec le gestionnaire de clés. Plusieurs problème de ce genre sont déjà rapportés sur Launchpad, on verra ce qu'il en sort.<br /></p> <br />
<p>Je contourne temporairement le problème en lançant manuellement <em>gnome-keyring-daemon</em>.</p> <br />
<ul> <br />
<li>GeoGebra et OpenJDK</li> <br />
</ul> <br />
<p>Officiellement, la machine virtuelle Java Sun/Oracle n'est plus disponible sur Ubuntu, remplacée par sa version libre OpenJDK. Globalement, les applications Java fonctionnent bien avec cette autre machine virtuelle.</p> <br />
<p>Cependant, les <a href="http://www.slu.edu/classes/maymk/GeoGebra/">applets GeoGebra ne fonctionnent pas</a> avec pour le moment (solution : retour à une JVM Sun/Oracle standard en <a href="https://bugs.launchpad.net/ubuntu/+source/openjdk-6/+bug/440841">attendant un correctif</a>).<br /> </p> <br />
<p><strong>Ce que je retiens de cette migration</strong></p> <br />
<ul> <br />
<li>La migration s'est parfaitement déroulée avec un PC qui avait été installé avec la version <em>n-1</em> par rapport à Ubuntu 10.04,</li> <br />
<li>La migration s'est correctement déroulée, avec quelques soucis mineurs, sur un PC installé avec la version <em>n-3</em>, migré en <em>n-2</em>, ... jusqu'à la version 10.04,</li> <br />
<li>La plus mauvaise migration s'est faite <br />
sur un PC installé avec la version <em>n-4</em>, migré jusqu'à la version 10.04.</li> <br />
</ul>J'ai l'impression qu'avec le temps, les migrations se déroulent de moins en moins bien, cela étant probablement dû à l'accumulation de résidus de préférences utilisateurs.<br />
