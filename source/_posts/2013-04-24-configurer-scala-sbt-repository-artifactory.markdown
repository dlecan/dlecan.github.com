
---
layout: post
title: "Configurer Scala SBT avec un repository Artifactory"
date: 2013-05-16 10:17
comments: true
published: true
categories: [sbt, artifactory]
---

[Scala SBT](scala-sbt) est à Scala ce que [Maven](maven) est à Java : un outil de build dédié qui épouse la philosophie du langage.

Comme [Gradle](gradle) avec Groovy, SBT est beaucoup plus puissant que Maven, dans le sens où, par exemple, étendre le build est beaucoup plus simple que de construire systématiquement un plugin (qu'il faut versionner, déployer, ...).

Cela dit, il ne faut pas occulter certains de ses inconvénients : incompatibilité des binaires de plugins entre versions de SBT, syntaxe complexe (en tout cas plus compliqué que du simple XML) ...

Scala SBT se répand petit à petit, avec [Play Framework 2](play2) comme cheval de Troie. En effet, Play2 a fait le choix de Scala SBT comme système de build :
- suite aux nombreuses critiques du système de build très fermé de Play 1
- car Play2 est écrit en Scala et se marrie donc naturellement avec SBT

Comme Maven, SBT se base sur le dépôt d'artifacts [Maven 2 Central](repo-maven2-central), mais aussi sur deux dépôts au format Ivy : [celui de Typesafe](repo-typesafe) et celui [dédié à SBT](repo-sbt).

# Scala SBT *à la Maven*

Qui dit entreprise, dit "proxy", "firewall", "anti-virus", ... bref tout ce qui empêche des outils de build comme SBT ou Maven de fonctionner en standard. Vous avez alors deux possibilités :
- soit paramétrer le proxy permettant à l'outil d'accéder à Internet
- soit paramétrer des dépôts d'artifacts *internes* à l'entreprise (ce qui ne nécessite plus d'accéder à Internet)

C'est ce dernier paramétrage que je vous propose de décrire en détails.

# Artifactory comme gestionnaire d'artifacts Maven et Ivy

Voici ce que nous allons configurer :

{% img /images/sbt-proxy-cloud-setup.png 650 528 'Artifactory dans son environnement' 'Source: documentation de Scala SBT_OPTS' %}

{% pullquote %}
Il existe plusieurs outils qui peuvent permettre de gérer des artifacts, dans différents formats (Maven, RPM, Deb, P2, ...), comme [Sonatype Nexus](nexus) ou [JFrog Artifactory](artifactory).

{"Ce qui compte pour SBT, c'est un gestionnaire d'artifacts qui supporte les formats Ivy et Maven."} J'ai donc choisi Artifactory.

Je vous passe les détails d'installation de l'outil, ils sont [très bien décrits dans sa documentation](install-artifactory).
{% endpullquote %}

La configuration des dépôts d'Artifactory s'effectue en deux étapes :

1. Ajouter les deux dépôts distants manquants, à savoir :

    `sbt-plugin-releases` => http://repo.scala-sbt.org/scalasbt/sbt-plugin-releases/
    `typesafe-ivy-releases` => http://repo.typesafe.com/typesafe/ivy-releases/

2. Ajouter un nouveau dépôt virtuel de type "Ivy" qui pointe sur `sbt-plugin-releases` et `typesafe-ivy-releases`, que j'ai nommé `ivy-remote-repos`

**L'erreur à ne pas commettre est de fusionner les dépôts Maven et Ivy en un seul**, au format Maven par exemple. En effet, le format Ivy est plus riche que celui de Maven et des informations essentielles pour la résolution des plugins SBT seraient perdues.

A ce stade de la configuration d'Artifactory, ces trois paramètres sont disponibles :

- L'URL d'accès à votre instance d'Artifactory : `http://localhost:8180/artifactory/` par exemple
- Le nom du dépôt Maven 2 virtuel qui agrège vos dépôts Maven 2 : `maven-remote-repos`
- Le nom du dépôt Ivy virtuel qui agrège vos dépôts Ivy : `ivy-remote-repos`

# Configurer Scala SBT

http://www.scala-sbt.org/release/docs/Getting-Started/Setup.html

La configuration de SBT s'effectue en deux temps.

## Ajouter les nouveaux dépôts à la configuration générale de SBT

Pour cela, créez le fichier `~/sbt/.repositories` avec le contenu suivant :

{% codeblock ~/sbt/.repositories %}
[repositories]
  local
  maven-local
  ivy-proxy-releases: http://localhost:8180/artifactory/ivy-remote-repos/, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext]
  maven-proxy-releases: http://localhost:8180/artifactory/maven-remote-repos/
{% endcodeblock %}

Ce fichier indique à SBT l'ensemble des dépôts qu'il peut consulter pour résoudre les dépendances :
- `local` : dépôt Ivy local par défaut, localisé dans `~/.ivy2/`
- `maven-local` : dépôt Maven local par défaut, localisé dans `~/.m2/repository/`
- `ivy-proxy-releases` : suivi d'une URL et d'un pattern, on indique à SBT qu'il pourra trouver les artifacts qui respectent ce pattern dans ce dépôts (pattern Ivy)
- `maven-proxy-releases` : suivi d'une URL, le dépôt au format Maven pour les autres dépendances

## Bloquer tous les dépôts configurés dans les builds projets

La configuration précédente permet d'ajouter des dépôts à la configuration de SBT. Mais des dépôts peuvent être paramétrés dans chaque projet SBT, ce qui ne fonctionnera pas si vous n'avez pas accès à Internet. Si vous désirez que toutes les demandes de dépendances SBT passent par votre proxy Artifactory, il vous faut le paramétrage complémentaire suivant :

```
-Dsbt.override.build.repos=true
```

A passer directement en argument à la ligne de commande Java qui lance SBT ou bien à rajouter à la variable `SBT_OPTS` par exemple.

# Tester

Un simple `sbt update` dans un projet SBT suffit à vérifier la bonne mise à jour des dépendances de SBT au travers d'Artifactory. Pour un projet Play2, la commande `play update` aura le même effet.

# Et pour Play Framework 2 ?

Cette configuration ne fonctionne pas pour Play Framework 2, j'y reviendrai dans un autre article.

[scala-sbt]: http://www.scala-sbt.org/	Projet Scala SBT
[maven]: http://maven.apache.org/ 		Projet Maven
[gradle]: http://www.gradle.org/		Projet Gradle
[play2]: http://www.playframework.com/
[repo-maven2-central]: http://repo1.maven.org/maven2/
[repo-typesafe]: http://repo.typesafe.com/typesafe/ivy-releases/
[repo-sbt]: http://repo.scala-sbt.org/scalasbt/sbt-plugin-releases/
[nexus]: http://www.sonatype.org/nexus/
[artifactory]: http://www.jfrog.com/home/v_artifactory_opensource_overview
[install-artifactory]: http://wiki.jfrog.org/confluence/display/RTF/Installing+Artifactory