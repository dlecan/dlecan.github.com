
---
layout: post
title: "Configurer Play Framework 2.x avec un repository Artifactory"
date: 2013-06-03 10:44
comments: true
published: true
categories: [sbt, playframework, artifactory]
---

[Play Framework[(play2] utilise [Scala SBT][scala-sbt] comme outil de build depuis la version 2.0. *A la Maven*, il télécharge les dépendances
de votre projet Play Framework depuis des dépôts centralisés, comme [Maven Central][repo-maven2-central].

Cette technique est très intéressante, mais il faut bien tenir compte des inconvénients induits :

1. Pas de maîtrise des dépendances ou des dépôts configurés par les développeurs : on télécharge n'importe quoi depuis n'importe où

2. Gâchi de bande passante / latence : chaque développeur télécharge chaque dépendance depuis Internet, là où une mutualisation depuis un 
serveur localisé dans le réseau de l'entreprise ferait économiser de la bande passante et gagner en réactivité (latence réduite)

3. Vous ne pouvez pas dépendre d'artifacts uniquement internes à votre entreprise (sans les publier sur Internet)

En entreprise, 1/ est généralement résolu par "blocage naturel" : on ne peut pas télécharger depuis Internet. 2/ et 3/ peuvent se résoudre à l'aide d'un serveur proxy dédié à Maven, SBT, ... comme [Sonatype Nexus][nexus] ou [JFrog Artifactory][artifactory].

Comment paramétrer Play Framework pour utiliser Artifactory ?

[J'ai déjà expliqué comment configurer SBT pour utiliser Artifactory][/configurer-scala-sbt-repository-artifactory/], mais, bien que Play Framework utilise SBT, ces explications ne s'y appliquent pas (pour le moment). SBT étant embarqué dans Play Framework, il ne lit pas les instructions de configuration fournies en ligne de commande.

Pas de différence du côté d'Artifactory en revanche.

Par la suite, je suppose que la variable `PLAY_HOME` pointe vers votre installation de Play Framework 2.x.

## Configurer SBT pour lire les dépôts "miroirs"

Créez le fichier `~/sbt/.repositories` avec le contenu suivant :

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

## Forcer Play Framework à lire le fichier `~/sbt/.repositories`

Éditez le fichier `$PLAY_HOME/framework/sbt/sbt.boot.properties` et complétez le bloc suivant :

{% codeblock $PLAY_HOME/framework/sbt/sbt.boot.properties %}
...
[ivy]
  ivy-home: ${play.home}/../repository

{% endcodeblock %}

De cette manière :

{% codeblock $PLAY_HOME/framework/sbt/sbt.boot.properties %}
...
[ivy]
  ivy-home: ${play.home}/../repository
  override-build-repos: ${sbt.override.build.repos-true}
  repository-config: ${sbt.global.base-${user.home}/.sbt}/repositories

{% endcodeblock %}

Avec ces deux instructions, on force Play Framework à utiliser uniquement les dépôts configurés dans le fichier `~/sbt/.repositories`.

## Tester

Commencez par nettoyer votre dépôt local de cache de Play Framework (par précaution, je vous propose de simplement le déplacer) :

```sh
mv $PLAY_HOME/repository/cache $PLAY_HOME/repository/cache.old

```

Postionnez-vous dans un projet Play, et testez :

```sh
dlecan@portable:~/dev/projet_test$ play
[info] Loading global plugins from /home/dlecan/.sbt/plugins
[info] Updating {file:/home/dlecan/.sbt/plugins/}default-ccfcd1...
[info] Resolving org.scala-sbt#precompiled-2_10_0;0.12.2 ...
[info] downloading http://localhost:8180/artifactory/ivy-remote-repos/com.typesafe.sbt/sbt-pgp/scala_2.9.2/sbt_0.12/0.8/jars/sbt-pgp.jar ...
[info] 	[SUCCESSFUL ] com.typesafe.sbt#sbt-pgp;0.8!sbt-pgp.jar (50ms)
[info] downloading http://localhost:8180/artifactory/maven-remote-repos/com/github/mpeltonen/sbt-idea_2.9.2_0.12/1.4.0/sbt-idea-1.4.0.jar ...
[info] 	[SUCCESSFUL ] com.github.mpeltonen#sbt-idea;1.4.0!sbt-idea.jar (37ms)
[info] downloading http://localhost:8180/artifactory/maven-remote-repos/com/jsuereth/gpg-library_2.9.2/0.8/gpg-library_2.9.2-0.8.jar ...
[info] 	[SUCCESSFUL ] com.jsuereth#gpg-library_2.9.2;0.8!gpg-library_2.9.2.jar (29ms)
[info] downloading http://localhost:8180/artifactory/maven-remote-repos/org/bouncycastle/bcpg-jdk16/1.46/bcpg-jdk16-1.46.jar ...
[info] 	[SUCCESSFUL ] org.bouncycastle#bcpg-jdk16;1.46!bcpg-jdk16.jar (21ms)
[info] downloading http://localhost:8180/artifactory/maven-remote-repos/org/bouncycastle/bcprov-jdk16/1.46/bcprov-jdk16-1.46.jar ...
[info] 	[SUCCESSFUL ] org.bouncycastle#bcprov-jdk16;1.46!bcprov-jdk16.jar (41ms)
[info] Done updating.
[info] Loading project definition from /home/dlecan/dev/truc/project
[info] Set current project to truc (in build file:/home/dlecan/dev/truc/)
       _            _
 _ __ | | __ _ _  _| |
| '_ \| |/ _' | || |_|
|  __/|_|\____|\__ (_)
|_|            |__/

play! 2.1.1 (using Java 1.7.0_21 and Scala 2.10.0), http://www.playframework.org

> Type "help play" or "license" for more information.
> Type "exit" or use Ctrl+D to leave this console.

[projet_test] $

```

Les dépendances sont téléchargées depuis `http://localhost:8180/...`, comme voulu.

Note : l'affichage obtenu peut varier selon vos dépendances paramétrées.

[scala-sbt]: http://www.scala-sbt.org/	Projet Scala SBT
[maven]: http://maven.apache.org/ 		Projet Maven
[play2]: http://www.playframework.com/
[repo-maven2-central]: http://repo1.maven.org/maven2/
[repo-typesafe]: http://repo.typesafe.com/typesafe/ivy-releases/
[repo-sbt]: http://repo.scala-sbt.org/scalasbt/sbt-plugin-releases/
[nexus]: http://www.sonatype.org/nexus/
[artifactory]: http://www.jfrog.com/home/v_artifactory_opensource_overview