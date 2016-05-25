
---
layout: post
title: "Le Web sécurisé par défaut grâce à DevOps et Let's Encrypt ? "
date: 2016-05-25 21:13
comments: true
published: true
categories: [devops, letsencrypt]
---

* [Initialement publié sur le Journal Du Net](http://www.journaldunet.com/solutions/expert/64185/le-web-securise-par-defaut-grace-a-devops-et-let-s-encrypt.shtml)*.

**L'accès à de nombreux sites Web n'est pas sécurisé pour plusieurs raisons : c'est compliqué et cher. Voyons comment les principes du DevOps et un coup de pouce de Let's Encrypt pourraient nous conduire au Web sécurisé par défaut.**

Les principes du DevOps insistent fortement sur l'automatisation des processus de construction ou de déploiement des systèmes informatiques. De nombreuses entreprises se sont emparées de ces nouveaux outils et les mettent en œuvre progressivement afin de livrer plus vite, au meilleur coût, avec une qualité élevée, des fonctionnalités à forte valeur ajoutée à leurs clients et leurs utilisateurs.

La plupart des étapes du processus de construction des systèmes informatique sont en effet automatisables : construction et paramétrage des serveurs, construction des applications (serveur ou mobiles), exécution des tests, livraison, déploiement, gestion et prise en compte des pannes... La liste est chaque jour plus longue. Il reste pourtant un domaine où l'automatisation est faible voire inexistante : la sécurité de l'accès aux sites Internet. Or, le contexte actuel d'(in)sécurité du Web en général montre que la sécurité ne doit plus être sacrifiée sur l'autel de la complexité ou du prix.

## La sécurité du Web

Cette sécurité repose sur des protocoles comme HTTPS, TLS, des algorithmes de chiffrement ou de hashage et au cœur, des certificats X.509. La combinaison de ces éléments nous permet d'accéder à des sites Internet de manière sécurisée (httpS://...), nous garantissant des échanges de données chiffrées et donc illisibles pour tout tiers qui tenterait d'intercepter les données. Du moins, en théorie car les failles de sécurités des systèmes informatiques et le talent des pirates leur permettent quelquefois d'accéder à des données confidentielles.

En pratique, la sécurité repose sur d'autres concepts qui permettent de renforcer la sécurité et de limiter les risques de piratage mais l'essentiel du processus repose sur les certificats de la norme X.509.

## Normés certes, mais compliqués et chers

[X.509](https://fr.wikipedia.org/wiki/X.509) est une norme qui définit entre autres les fameux certificats sur lesquels reposent HTTPS et TLS. Une chaine de confiance hiérarchique est établie, dans laquelle des certificats particuliers -dits "racine"- signent et valident d'autres certificats. Les certificats racines sont gérés par des autorités de certification qui sont responsables de l'émission de nouveaux certificats.

Les certificats racines sont directement embarqués dans les navigateurs web, ce qui permet à ces derniers d'ouvrir des connexions sécurisées avec des sites web lorsqu'ils présentent un certificat délivré par une autorités de certification reconnue.

*Les certificats X.509 sont de bons concepts cryptographiques, qui présentent cependant des inconvénients majeurs.*

1/ Ils sont compliqués à gérer. Un certificat doit être d'abord généré, ce qui requiert plusieurs étapes et fait intervenir plusieurs acteurs. L'utilisateur émetteur doit générer une demande de signature de certificat (CSR) lors de laquelle il choisit un certain nombre de paramètres, tels que la taille de la clé du certificat (il faut se tenir au courant des valeurs assurant un niveau de sécurité adéquat, ce qui évolue avec le temps) et un mot de passe optionnel qui sera requit à chaque usage du certificat. Puis l'autorité de certification doit signer la demande et renvoyer le certificat à l'émetteur de la demande. Enfin, il faut configurer le site web avec le certificat reçu de l'autorité et d'autres paramètres, ce qui est tout sauf évident : les serveurs web ne proposant que rarement une configuration simplifiée (des dizaines de paramètres sont ajustables).

Un certificat a en outre une durée de vie limitée -généralement entre 1 et 3 ans- et doit donc être renouvelé avant son expiration par un processus proche de sa génération initiale. Il faut alors de nouveau mettre à jour la configuration du serveur.

Corollaire direct, gérer les certificats est long et lent. Comme le processus fait intervenir plusieurs acteurs, avec une complexité élevée et d'occurrence rare, pourquoi automatiser quelque chose qui se produit très rarement ? Les certificat sont donc dans la plupart des organisations générés et gérés manuellement et la difficulté est élevée à chaque fois qu'il faut les générer ou renouveler.

2/ Ils sont chers. Demander un certificat à une autorité de certification publique est la plupart du temps payant (jusqu'à plusieurs centaine d'euros par certificat).

Compte tenu de ces inconvénients, le choix des concepteurs de site web est donc radical : ils ne sécurisent pas ou très peu quand ils ne peuvent pas faire autrement. Pourtant, l'accès sécurisé à un site web est désormais essentiel ! [Google favorise et privilégie](https://webmasters.googleblog.com/2014/08/https-as-ranking-signal.html) les sites Internet accessibles au travers de HTTPS en leur offrant un meilleur référencement sur son moteur de recherche. De nouveaux protocoles qui vont apporter de meilleures performances dans les usages mobiles comme HTTP/2, requièrent, aussi, obligatoirement HTTPS, donc TLS, donc des certificats.

## Des nouvelles autorités de certification

Revenons à nos pratiques DevOps, et en particulier l'automatisation. Certains acteurs majeurs du Cloud ont bien compris la problématique et proposent depuis quelques temps la gestion complète des certificats, de leur cycle de vie et de leur configuration dans les serveurs web, sans (presque) aucune opération manuelle ! (comme [AWS certificate Manager](https://aws.amazon.com/fr/certificate-manager/)) Une raison de plus pour basculer dans le Cloud !

Pour les autres, une nouvelle autorité de certification répond aux trois problématiques exposées précédemment : Let's Encrypt. Let's Encrypt est une autorité de certification [officiellement opérationnelle depuis le 12 avril 2016](https://letsencrypt.org/2016/04/12/leaving-beta-new-sponsors.html), avec comme principes clés la gratuité, l'automatisation et la sécurité. Le tout garanti par la transparence et l'ouverture de ses processus et de ses codes sources.

Comme toute autorité de certification, Let's Encrypt délivre des certificats, les renouvelle mais aussi les configure sur les serveurs web, et ce automatiquement, sans autre intervention humaine que de fournir un paramétrage initial. Ensuite, les outils s'occupent de tout ! Et comme un bon processus est un processus récurrent fréquent, Let's Encrypt force à l'automatisation en générant des certificats à durée de vie extrêmement courte : seulement trois mois, avec préconisation de renouvellement au bout de deux mois. Impossible à utiliser sans automatisation donc.

## Le Web sécurisé par défaut grâce aux principes DevOps ?

[De nombreuses entreprises et entités soutiennent Let's Encrypt](https://letsencrypt.org/sponsors/), financièrement et moralement, comme Mozilla, Akamai, Cisco, OVH, Google, Facebook, Gemalto, Gandi, Free, HP… et l'usage des certificats émis par Let's Encrypt se répand comme une trainée de poudre : [le cap du million de certificats générés](https://letsencrypt.org/2016/03/08/our-millionth-cert.html) a été franchi en mars 2016 et la progression continue sur le même rythme ; de plus en plus de services Internet proposent ou vont proposer très prochainement de sécuriser les sites web de leurs utilisateurs au travers de Let's Encrypt : Gandi, Free/Online, Wordpress, OVH, sans compter divers CDN (KeyCDN, Kloudsec, CDNSun …).

L'usage simplifié promu par Let's Encrypt n'est pas étranger à cette adoption, qui sera sans nul doute, massive dans les années à venir.
En effet, tout le monde y gagne :

- Les concepteurs de site : activation de la sécurité de manière simple, voire transparente, voire même par défaut ; meilleur référencement auprès de Google,
- Les utilisateurs : garantie de confidentialité de leurs activités sur de nombreux sites web ; en ce temps d'espionnage massif, c'est essentiel pour garantir la confiance,
- Les services Internet d'hébergement de sites ou les CDN : c'est un service gratuit de plus à offrir à leur client et qui peut en attirer de nouveaux.

Les autorités de certification traditionnelles sont pour le moment relativement épargnées puisque que ce sont principalement des sites web qui n'étaient pas auparavant sécurisés qui basculent vers Let's Encrypt. De plus, certains types de certificats ne sont actuellement pas pris en compte par Let's Encrypt, ce qui continue de garantir leur monopole. Mais pour combien de temps ?

[Certains considèrent cependant](https://blog.imirhil.fr/2015/12/12/letsencrypt-joie-deception.html) que les choix effectués par Let's Encrypt vont à l'encontre de l'élévation du niveau de sécurité des sites web. Mais ce sont justement ces choix qui permettent et conduisent à l'automatisation. Le web sécurisé par défaut n'a jamais été aussi proche grâce aux principes DevOps et à Let's Encrypt.
