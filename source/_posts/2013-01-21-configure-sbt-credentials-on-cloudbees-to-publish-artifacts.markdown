---
layout: post
title: "Configure SBT credentials on Cloudbees platform to publish artifacts"
date: 2013-01-21 17:10
comments: true
categories: [Cloudbees, SBT]
---

When you want to deploy your Scala artifacts (or anything else) with [SBT](http://www.scala-sbt.org), SBT reference documentation [explains well how to configure](http://www.scala-sbt.org/release/docs/Community/Using-Sonatype.html#fourth-adding-credentials) your own repository's credentials.

Great, just create a bunch of `*.sbt` file in `~/.sbt` folder. Easy, simple.

But wait, I'm trying to publish artifacts from a Cloudbees Jenkins instance. How can I copy my `*.sbt` files into `~/.sbt``on Cloudbees Jenkins platform? You can't.

On Cloudbees, all files handled by Jenkins come from either your SCM (GIT, SVN, ...) or from your private repository: `/private/<my_account>/` ([you can upload files in thanks to WebDAV](http://wiki.cloudbees.com/bin/view/DEV/Sharing+Files+with+Build+Executors)).

As I want to load crendentials in SBT, that is to say secret passwords, I used the 2nd source.

## Create and upload your `*.sbt` file your private folder

I have choosen to copy all my `*.sbt` files into `/private/<my_account>/.sbt` folder.

## Create a new shell script

Create this new shell script `/private/<my_account>/bin/sbt-setup.sh` with the following content:

```sh
#!/bin/sh

mkdir -p ~/.sbt
for f in /private/play-war/.sbt/*.sbt
do
  ln -s $f ~/.sbt/
done
``` 

This script will create a symbolic link in `~/.sbt/` for each files found in `/private/<my_account>/.sbt`.

## Add a new Jenkins Job to excute the shell script

Before a SBT build which requires credentials (eg. for `publish` task), create a new Jenkins job :

```sh
/bin/sh /private/play-war/bin/sbt-setup.sh

```
Save and run your job.

Voilà, that's all folks!