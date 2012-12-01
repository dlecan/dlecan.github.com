---
layout: post
title: "Octopress continous integration"
date: 2012-07-04 21:17
comments: true
published: true
categories: [octopress, cloudbees, jenkins]
---

**Update 2012-11-30**: update script to adapt to last Cloudbees changes.

[Octopress](http://octopress.org) is great for blogging:

 - [Clear and easy-to-read syntax](http://daringfireball.net/projects/markdown/syntax/) thanks to [Markdown](http://daringfireball.net/projects/markdown/syntax/)
 - Offline editing (write your post offline, push it to Github, see it in 'production)

This blog is powered by Octopress.

But Octopress relies on Ruby rake to generate and deploy your blog posts, so you need to install them on each computer from where you want to deploy your blog.
And installing theses tools are generally a pain: ruby/rvm, rake, bundler, encoding issues between platforms, ...

So the perfect "blog post flow" would be:

 1. write blog post everywhere: laptop, personnal/professional computer, ...
 2. generate and deploy from only one place

I found a centralized place to generate and deploy this blog: a [Cloudbees](http://www.cloudbees.com) Jenkins instance!
Jenkins is a continuous integration server, which allows you to build and deploy Java, Ruby, ... applications.

What about a ruby/rake/bundler build ?

Configure a new free-style projet, with the following *shell* job:

```sh
#export LC_ALL=fr_FR.UTF-8 # uncomment this if you need to force locale
#export LANG=fr_FR.UTF-8  # uncomment this if you need to force locale
curl -s -o use-ruby https://repository-cloudbees.forge.cloudbees.com/distributions/ci-addons/ruby/use-ruby
RUBY_VERSION=1.9.3-p327 \
source ./use-ruby
ln -s /scratch/jenkins/ruby /scratch/jenkins/rubies
gem install --conservative bundler
bundle install
rake setup_github_pages[git@github.com:dlecan/dlecan.github.com.git]
rake generate
rake deploy
```
That's it! Just replace `git@github.com:dlecan/dlecan.github.com.git` by your Git repository and let Jenkins do the job for you!

This blog is also built thanks to Cloudbees :) {% img right http://web-static-cloudfront.s3.amazonaws.com/images/badges/BuiltOnDEV.png %}

See it in action on this [Cloudbees Jenkins instance](https://play-war.ci.cloudbees.com/job/blog.dlecan.com/).
