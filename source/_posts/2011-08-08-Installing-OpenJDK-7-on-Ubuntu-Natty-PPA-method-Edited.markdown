--- 
layout: post
title: Installing OpenJDK 7 on Ubuntu Natty (PPA method) (Edited)
categories: 
- Ubuntu
permalink: /archives/69-Installing-OpenJDK-7-on-Ubuntu-Natty-PPA-method-Edited.html
s9y_link: http://www.dlecan.com/archives/69-Installing-OpenJDK-7-on-Ubuntu-Natty-PPA-method-Edited.html
date: 2011-08-08 14:04:44 +02:00
---
<p><strong>Edit 2011-11-30 : this PPA is now deprecated&#160;as OpenJDK7 is available in Ubuntu Oneiric repositories.</strong></p><br />
<p>Many methods to install OpenJDK 7 on Ubuntu can be found on the Net :</p><br />
<ul><li>this <a href="https://launchpad.net/~openjdk/+archive/ppa">PPA</a>, but very old <br />
</li><li><a href="http://nikolavp.blogspot.com/2010/05/installing-jdk7-in-ubuntu.html">this method</a> by downloading OpenJDK from Oracle's website, but you need to do the same thing on each of your computers. </li></ul><br />
<p>OpenJDK 7 is "natively" available on Ubuntu Oneiric (11.10), but is not and won't be on Natty (11.04). But I need it now on Natty and I don't want to install it manually on each of my computers. More, I want a transparent migration when I will upgrade to Ubuntu Oneiric on next October.</p><br />
<p>So I decided to backport OpenJDK 7 Oneiric package to Natty and the result is available here : <a href="https://launchpad.net/~dlecan/+archive/openjdk">https://launchpad.net/~dlecan/+archive/openjdk<br /></a></p><br />
<p>Add this <strong>ppa:dlecan/openjdk</strong> to your Ubuntu and install openjdk-7-jre or openjdk-7-jdk.</p><br />
<script src="https://gist.github.com/1133900.js?file=gistfile1.sh"></script><br />
 <br />
<p>I will support this ppa as long as I will upgrade to Ubuntu Oneiric.</p><br />
<p>Enjoy !</p>
