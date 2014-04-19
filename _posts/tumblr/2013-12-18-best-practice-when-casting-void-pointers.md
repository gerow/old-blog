---
layout: post
title: '"Best practice" when casting void pointers'
date: '2013-12-18T17:25:00-08:00'
tags: []
tumblr_url: http://mgerow.tumblr.com/post/70426670626/best-practice-when-casting-void-pointers
---
This is going to be a short little blurb. In an interview I had with a company (that I will not name) I was writing up some code in C and wrote something like this:
int *foo = malloc(10 * sizeof(*foo));

It all seemed fine to me, but the guy I was interviewing with insisted that it is good practice to explicitly cast a void pointer (which is what malloc(3) returns) when you get one. So something like this would be more proper:
int *foo = (int *)malloc(10 * sizeof(*foo));

Of course, since I was in an interview, I didn’t think much if it, and getting into nitty gritty technical arguments with your interviewer doesn’t sound like a great way to get hired. When I came back home, though, I thought about it for a while and determined that it’s actually probably better to NOT explicitly cast a void pointer to a specific type. The reason being that because you’re expecting a void pointer, it will fail (or at least provide a warning) if you don’t specify an explicit cast, but will provide no notice at all if you happen to convert some other type to your new type. Consider the following example:
Say that I have the following function pointer type that I use to handle a whole lot of different kinds of objects:
int (*turn_on)(int id, void *priv);
In this case we can imagine that we might have a number of devices that all have the ability to be turned on, so we would call that device’s turn_on function passing it an id and a void pointer to private data that it has allocated for its own use. So you might implement it somewhat like this:
#define MYCOOLDEVICE_ON_FLAG = 0x80

typedef struct {
  char[255] name;
  uint32_t fakeid;
  uint8_t flags;
} mycooldevice_priv_t;

int mycooldevice_turn_on(int id, void *priv)
{
  mycooldevice_priv_t *mypriv = priv;
  mypriv->flags |= MYCOOLDEVICE_ON_FLAG;
  
  return 0;
}
Of course, you would probably have some kind of init function that would have set up priv earlier, this shows a pretty common use of the void pointer. Now, imagine a few years down the road someone looks at the code and thinks “ah, all of these devices that we’re turning on are just casting the void* to some_other_type_t! We should just change the definitions of all these functions to take an explicit type to make it more obvious.” Unfortunately that person failed to notice that there was still one other type of device that was casting the void* to a different type. If the aformentioned programmer made the change and our turn_on code looked like it does above, we would get explicit warnings saying that we are implicitly casting from some_other_type_t* to mycooldevice_priv_t*, while something like this:
#define MYCOOLDEVICE_ON_FLAG = 0x80

typedef struct {
  char[255] name;
  uint32_t fakeid;
  uint8_t flags;
} mycooldevice_priv_t;

int mycooldevice_turn_on(int id, void *priv)
{
  mycooldevice_priv_t *mypriv = (mycooldevice_priv_t *)priv;
  mypriv->flags |= MYCOOLDEVICE_ON_FLAG;
  
  return 0;
}
Would fail silently, and we likely wouldn’t figure out it was a problem until a huge bug reared its head. I know this is a somewhat contrived example, but I hope it illistrates the ways that *not* explicitly casting a void pointer can provide benefits that explicit casts don’t provide.
