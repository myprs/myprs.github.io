---
layout: post
title:  "First Alpha Version of My Letsencrypt Updater Script Published"
date:   2015-12-05 16:48:10 +0100
categories: letsencrypt, nginx, scripting
---


As promised in my [last post on letsencrypt](letsencrypt,/nginx,/primer/2015/12/05/my-first-review-on-letsencrypt.html) I wanted to look into the problem of updating the signatures if you are running a not so standard setup.

In my case I am running nginx with multiple [virtual name based hosts][namebasedvirtualhosting]. 

I have finished a alpha version of my idea of an updater script [on the Github project lenc-updater](https://github.com/myprs/lenc-update). No fancy installers or fool proof checks on the environment yet. Have a look and enjoy hacking...



## Links: 

* Project Github Page: <https://github.com/myprs/lenc-update>


[namebasedvirtualhosting]: http://httpd.apache.org/docs/2.2/en/vhosts/
