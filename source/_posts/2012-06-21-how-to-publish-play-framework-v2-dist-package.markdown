---
layout: post
title: "How to publish Play2 'dist' package ?"
date: 2012-06-21 17:20
comments: true
categories: [play20, SBT]
---

In an enterprise context, it is often necessary to deploy (maven) or publish (Ivy/SBT) artifacts in a central repository.

Here is a sample Play2 project configured to publish artifact in a local folder (`~/.ivy2/publish`), but you can easily adapt it to publish
to your central repository ([Nexus](http://www.cakesolutions.net/teamblogs/2012/01/28/publishing-sbt-projects-to-nexus/), Artifactory, ...)

```scala
import sbt._
import Keys._
import PlayProject._

object ApplicationBuild extends Build {

  val appName = "sample"
  val appVersion = "1.0-SNAPSHOT"

  // Properties to add configure the new Artifact  
  lazy val distSettings = Seq[Setting[_]] (
    
    // Type of the Artifact : zip
    // Extension of the Artifact : zip
    artifact in dist <<= moduleName(n => Artifact(n, "zip", "zip"))
  ) ++ Seq(addArtifact(artifact in (Compile, dist), dist).settings: _*)

  val appDependencies = Seq(
    // Add your project dependencies here,
  )

  val main = PlayProject(appName, appVersion, appDependencies, 
                         mainLang = SCALA, 
                         // Don't forget to add defaultSettings !
                         settings = Defaults.defaultSettings ++ distSettings
             ).settings(

    // Optional
    // Disable jar for this project (useless)
    publishArtifact in (Compile, packageBin) := false,

    // Disable scaladoc generation for this project (useless)
    publishArtifact in (Compile, packageDoc) := false,

    // Disable source jar for this project (useless)
    publishArtifact in (Compile, packageSrc) := false,

    // Where to 'publish'
    publishTo := Some(Resolver.file("file", file(Path.userHome.absolutePath + "/.ivy2/publish"))),

    // Use Maven pattern to publish
    publishMavenStyle := true)
}
```

Next step: to be able to "release" a Play2 project with SBT (update version in project, tag, build and publish artifacts).
