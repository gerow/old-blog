---
layout: post
title: hnfs -- Beginnings
date: '2013-12-18T05:41:25-08:00'
tags: []
tumblr_url: http://mgerow.tumblr.com/post/70387905990/hnfs-beginnings
---
Recently I’ve been interested in learning more about filesystems and various ways you can represent things as filesystems. To that end I decided to start a little project called hnfs.

hnfs is a filesystem built using FUSE to allow one to read hacker news as a filesystem. You can find on my github here: <https://github.com/gerow/hnfs>.

It is currently unfinished, though I have made some decent progress in the last few days. It can represent each of the stories on the front page as a directory containing a number of files including one called “content.html” which contains the html of the article linked to. The one issue now is that it doesn’t actually get posts from hacker news and is instead reading some test posts I created from a json file. This is because hn doesn’t actually provide a proper API to the front page. After I finish messing around with the filesystem I’m hoping to throw together a little scraper in ruby and serving it up using Sinatra to get around this.

Of note is the fact that the entire thing is written in C. Although it is true that the FUSE libraries are implemented in C, there is a Python library to allow you to use FUSE in Python. I stuck with C, though, because I’m interested in honing my C skills.

One thing that using C makes quite tricky is working with json. In languages like Python or Ruby one can generally blow a json file up into primitives for the language, like a dict in Python or a hash in Ruby. In C such primitives do not exist. Although I am using a library for working with json in C, it is still somewhat tricky and worth making a blog post about in the future.
