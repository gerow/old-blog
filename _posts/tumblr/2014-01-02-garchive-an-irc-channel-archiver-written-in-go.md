---
layout: post
title: garchive -- An IRC channel archiver written in Go
date: '2014-01-02T02:08:00-08:00'
tags:
- irc
- golang
- go
- git
tumblr_url: http://mgerow.tumblr.com/post/71952321235/garchive-an-irc-channel-archiver-written-in-go
---
Recently I’ve been working on a little project for archiving IRC channel conversations. This sprang out issues on a channel I frequent where my wifi would drop out sporadically and I would miss entire chunks of conversation. I could use a fancy cloud-based irc service like IRCCloud, but that costs money. So instead I opted to write a little bot in Go to run on my server at mgerow.com to do the archiving.
Now, I’m sure there are plenty of other bots like this out there that are way better than mine, but writing this thing was a real learning experience, and Go’s ability to easily split things out into new routines actually made writing an IRC client pretty easy.
You can find the code here <https://github.com/gerow/garchive>.
