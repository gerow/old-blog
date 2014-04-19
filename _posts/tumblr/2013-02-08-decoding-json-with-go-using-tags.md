---
layout: post
title: Decoding json with go using tags
date: '2013-02-08T20:52:00-08:00'
tags: []
tumblr_url: http://mgerow.tumblr.com/post/42641740774/decoding-json-with-go-using-tags
---

Note: if you are viewing this on mgerow.com you won’t see the fancy syntax hilighting in the code sections of this post. If you would like to see them properly please go here.

I was working on json decoding with go just now and thought it would be nice to share a neat little part of go that allows us to translate between json objects and go objects.
So let’s say I want to decode some json that looks like this:

    {
      "port":9180,
      "only_allow_ips":["192.168.1.1","192.169.1.1"],
      "commands":[
      {
        "path":"/here/is/path",
        "command":"touch hello",
        "only_allow_ips":["192.188.1.5", "192.162.1.55"],
        "limit_rate":120
      },
      {
        "path":"/here/is/another/path",
        "command":"touch hello",
        "only_allow_ips":["192.188.1.5", "192.162.1.55"],
        "limit_rate":120
      }]
    }

The general way to bring these into go is to define some structs with the same fields and simply run json.Unmarshal on them. Based on that, it seems the most obvious way to accomplish our goal would be to load the json into structs that look like this:

    type Command struct {
    	path         string
    	command      string
    	only_allow_ips []string
    	limit_rate    int
    }
    
    type Config struct {
    	port         int
    	only_allow_ips []string
    	commands     []Command
    }

Great, so we unmarshal and… the unmarshaller can’t access the fields of the struct. For those unfamiliar with the way go works you don’t explicitly state whether fields in a struct are public or private, it is assumed based on the case of the first character of the field. Capitalized means it’s public, lower case means it’s private. This can be nice for some things, but it kinda messes up situations like this where go is introspectively looking at the member field name.

Ok, so we have a few options for resolving this. We could just rename all the fields in the structs to start with a capital letter, but one would assume that you would then need to make sure the json is formatted appropriately (something we might not even be able to do). Especially considering the fact that the standard for json 2.0 (http://www.jsonrpc.org/specification under heading 2) says that member names are case sensitive. Well, not quite.

Looking through the go source code we see that matching goes through three stages (http://golang.org/src/pkg/encoding/json/decode.go starts on line 510, reproduced below)

    // First, tag match
    tagName, _ := parseTag(tag)
    if tagName == key {
    	f = sf
    	ok = true
    	break // no better match possible
    }
    // Second, exact field name match
    if sf.Name == key {
    	f = sf
	    ok = true
    }
    // Third, case-insensitive field name match,
    // but only if a better match hasn''t already been seen
    if !ok && strings.EqualFold(sf.Name, key) {
	    f = sf
	    ok = true
    }

This third option above seems to indicate that we can simply capitalize the first letter of our field names and everything will be hunky dory. That is, unless you have json tags that are differentiated only by capitalization (which the standard says is perfectly OK). Also, and this is purely a stylistic thing, I think that this:

    type Command struct {
    	Path         string
    	Command      string
    	Only_allow_ips []string
    	Limit_rate    int
    }
    
    type Config struct {
	    Port         int
	    Only_allow_ips []string
    	Commands     []Command
    }

Just looks plain ugly. The prevailing go style is to use UpperCamelCase for public struct members, and this just makes everything look ugly. We could change the way we specify the json (if we even can), but I think that json that looks like this breaks the same rule (breaking from the norm of naming members like_this):

    {
      "Port":9180,
      "OnlyAllowIps":["192.168.1.1","192.169.1.1"],
      "Commands":[
      {
        "Path":"/here/is/path",
        "Command":"touch hello",
        "OnlyAllowIps":["192.188.1.5", "192.162.1.55"],
        "LimitRate":120
      },
      {
        "Path":"/here/is/another/path",
        "Command":"touch hello",
        "OnlyAllowIps:["192.188.1.5", "192.162.1.55"],
        "LimitRate":120
      }]
    }

How do we solve this problem? Well, looking back at the go source code we notice that option one uses something called tags. Go has a pretty powerful feature (golang.org/ref/spec#struct) that allows you to tag any member of a struct with a string that can later be fetched using reflection. This allows us to elegantly rewrite our struct definitions like this:

    type Command struct {
	    Path         string   `json:"path"`
	    Command      string   `json:"command"`
	    OnlyAllowIps []string `json:"only_allow_ips"`
	    LimitRate    int      `json:"limit_rate"`
    }

    type Config struct {
    	Port         int       `json:"port"`
	    OnlyAllowIps []string  `json:"only_allow_ips"`
	    Commands     []Command `json:"commands"`
    }

Just lovely. Within go the struct members look like they belong in go, within json they look like the belong in json, and we didn’t have to do any laborious stuff like creating some kind of new JsonUnmarshaller object and then call a method to remap each member name to its equivalent json value. This gives us a final program that looks like this:

    package main
    
    import (
	    "encoding/json"
	    "fmt"
    )

    type Command struct {
	    Path         string   `json:"path"`
	    Command      string   `json:"command"`
	    OnlyAllowIps []string `json:"only_allow_ips"`
	    LimitRate    int      `json:"limit_rate"`
    }
    
    type Config struct {
    	Port         int       `json:"port"`
    	OnlyAllowIps []string  `json:"only_allow_ips"`
    	Commands     []Command `json:"commands"`
    }
    
    func main() {
    	jsonstring := `{
      "port":9180,
      "only_allow_ips":["192.168.1.1","192.169.1.1"],
      "commands":[
      {
        "path":"/here/is/path",
        "command":"touch hello",
        "only_allow_ips":["192.188.1.5", "192.162.1.55"],
        "limit_rate":120
      },
      {
        "path":"/here/is/another/path",
        "command":"touch hello",
        "only_allow_ips":["192.188.1.5", "192.162.1.55"],
        "limit_rate":120
      }]
    }`
    
	    var config Config
	    json.Unmarshal([]byte(jsonstring), &config)
    
	    fmt.Printf("Config looks like %v", config)
    }

How could we do better? Well, we could modify the go source to also look for an identifier where the field name is converted from ThisTypeOfForm to this_type_of_form at runtime. This would require modifying the go libraries directly (which is a little crazy). The other alternative is to perhaps send the struct through some kind of tagging function that creates those tags for us on the fly, which I don’t even know if that’s possible to do dynamically.

Any, you can run and play with the code in the go playground here http://play.golang.org/p/TcpdCQTNI5. I just thought I’d like to share one little part of why I find go to be a nice little language.

ADDENDUM: After reading up a little more on json it looks like there really isn’t a so-called “correct” variable naming style. Even so, this method should come in handy to those who wish to follow a different style in json than within their go code. In addition, when dealing with capitalization issues it can sometimes be better to simply be more explicit in how you’re identifying your json variables.
