---
layout: post
title: Deep equality checking in go for unit testing
date: '2013-02-11T01:57:00-08:00'
tags: []
tumblr_url: http://mgerow.tumblr.com/post/42833264994/deep-equality-checking-in-go-for-unit-testing
---
I just finished a quick little go function that performs deep equality checking between two interface{}es and creates calls to (testing.T).Errorf when it finds differences along the way. There is already a deep equality checker built into go (reflect.DeepEqual) but it wasn’t that useful for unit testing since it when it finds that two interface{}es are unequal it doesn’t give you any idea as to WHY they were not equal.

My little function (gotest.AssertDeepEqual(a interface{}, b interface{}, t *testing.T)) overcomes that by making calls to t.Errorf when it spots a difference from the most specific to most general level.  This means that if you have say two arrays in the form [ “a”, “b” ] and [ “c”, “b” ] you will first get an Errorf saying that it found that two strings “a” and “c” were different.  You will then get an Errorf call sying that arrays [ “a”, “b”] and [ “c”, “b”] are different.

Using this I was able to fix a small error in the way that my little go project boop (which is completely unfinished by the way) was being tested.

Anyway, you can find the code and how to use it here: https://github.com/gerow/gotest

I also hope to add more useful stuff for testing to this repo in the future.
