---
layout: post
title: Learning Vim
date: '2013-01-27T15:08:00-08:00'
tags: []
tumblr_url: http://mgerow.tumblr.com/post/41649160925/learning-vim
---



Over winter break I made a promise to myself.  I promised that I wouldn’t touch Sublime Text (my favourite text editor) for the next school semester and would force myself to use nothing but vim in order to learn how to better use it.
It’s just over a month after I made that promise now, and things seem to be going ok.  My proficiency with vim has drastically increased from my first introduction to it (which mainly involved wondering why I couldn’t get the damn thing to stop beeping at me followed by puzzlement at how to quit it).  I’m still not quite to the point to where I’m faster than I was with Sublime, but I thought I would share a few of the commands I found useful while learning vim.
All the ci and ca have been really useful to me.  For example, if I move the cursor over a word and do ciw it will delete the word my cursor is over (no matter where on the word it is) and drop me into change mode right between the spaces.  Replace the i with a (caw) will cause the spaces to the left and right of the word to be deleted.  This alone was pretty neat but when I found out you could do the same thing with certain delimiters I used it all the time.  For example, with ci” inside of a double quoted string it will delete the entire string except for the quotes and drop me into insertion mode within the quotes.  The same can be done for <, (, ‘, {, and probably a whole lot of other delimiters.  Finally, you can do the same thing within html tags by simply typing cit.  So if I have something like <h1>Hello World</h1> and I type cit inside of it the Hello World part will be deleted and I will be in insertion mode right between the tags.  Nice.
The second thing I found indispensable is windows splitting.  I always loved having split windows in Sublime and I was pleased that creating a vertically split window within vim was as simple as C-w v and moving between windows required no more than C-w w.
Along a similar line, I at first found that having to :e in order to open another file in vim was a bit slow, especially when you can’t really remember the exact name of the file you’re looking to open.  Typing :E will open a vim file browser in the directory of the file that is currently in the window.  Typing :E. (notice the dot) will open up a vim file browser in the cwd (likely whatever directory you launched vim from or whatever directory you may :!cd to).


So much power, all just a few keys away from the home row.
Another very useful one is the = action, which fixes the indentation of code.  I consistently found myself running =gg after pasting code in order to quickly fix its formatting.
And lastly, this isn’t something that is very general, but I thought I would share one neat case where vim macros actually saved me a lot of tedium.  I was working on a small c program to encode text through a model of the Enigma machine.  This mainly involves a letter goes through a series of rotors, each of which changes one letter to another one.  An easy way to represent this is using one long string such as “JGDQOXUSCAMIFRVTPNEWKBLZYH” which corresponds A to J, B to G, etc.  These rings rotate so that with the next key that is pressed A becomes G and so on.
Within my c code I wanted to represent each ring as simply an array of characters.  So I started by typing char* ringI = {JGDQOXUSCAMIFRVTPNEWKBLZYH}.  I then went over the first character and hit q to start a macro, and hit q again to assign it to q.  I then dropped a single quote out front of J, dropped one on the back of it, and added a comma and space to make it look nice.  I then moved the cursor over to G before hitting q to end the macro.  I then simply did 25@q and boom, all the characters were correctly quoted and the only thing I needed to do was remove a straggling comma after the H.  Pretty sweet, huh?
It’s things like this that really excite me and push me to focus on using vim for the future.  I really hope to one day surpass my efficiency with Sublime and truly become a vim wizard™.
Note: it’s funny how writing about something like this can make you see how silly some of your solutions might be.  I realize now that I could have simply done each ring as a char * and stored it as a literal string simply as “JGDQOXUSCAMIFRVTPNEWKBLZYH”.  It would have added an extra byte for the string termination character, but really that would have probably been a lot easier.
