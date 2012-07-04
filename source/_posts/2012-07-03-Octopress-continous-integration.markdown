---
layout: post
title: "Octopress continous integration"
date: 2012-03-21 21:17
comments: true
published: true
categories: [octopress, cloudbees, jenkins]
---
[Octopress](http://octopress.org) is great for blogging:
- Clear and easy syntax thanks to Markdown
- Offline editing (write your post offline, push it to Github, see it in 'production)

This blog is powered by Octopress.

But Octopress relies on Ruby rake to be generated and deploy your blog posts, so you need to install them on each computer from where you want to deploy your blog.
And installing theses tools are generally a pain: ruby/rvm, rake, bundler, encoding issues between platforms, ...

So the perfect "blog post flow" would be:
- write blog post everywhere: laptop, personnal/professional computer, ...
- generate and deploy from only one place

I found a centralized place to generate and deploy this blog: a [Cloudbees](http://www.cloudbees.com) Jenkins instance!
Jenkins is a continuous integration server, which allows you to build and deploy Java, Ruby, ... applications.

What about a ruby/rake/bundler build ?

Configure a new free-style projet, with the following shell job:
```
curl -s -o use-ruby https://repository-cloudbees.forge.cloudbees.com/distributions/ci-addons/use-ruby
RUBY_VERSION=1.9.2-p320 \
source ./use-ruby
gem install --conservative bundler
bundle install
rake setup_github_pages[git@github.com:dlecan/dlecan.github.com.git]
rake generate
rake deploy
```
That's it! Just replace `git@github.com:dlecan/dlecan.github.com.git` by your Git repository.

this blog is also powered thanks to Cloudbees :) {% img right http://web-static-cloudfront.s3.amazonaws.com/images/badges/BuiltOnDEV.png %}