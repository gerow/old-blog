---
layout: post
title: gsynth, a look at the UI
date: '2012-05-17T12:34:00-07:00'
tags:
- gsynth
- synthesizer
- music
- programming
tumblr_url: http://mgerow.tumblr.com/post/23241319628/gsynth-a-look-at-the-ui
---
Over the last few weeks I’ve been working on an audio synthesizer that I’ve just been calling gsynth.  Although it is pretty early in its development, I wanted to share some of the plans I have for it.
My main intention with gsynth is to provide a powerful way for artists to build their own synthesized sounds by using a number of visual building blocks and connecting them together using various signal “wires”, in a way that is similar to LabView, though hopefully the instrument paradigm should allow for “code” that is much more linear in nature, with essentially no branching (which is part of what made LabView so difficult to decipher).

Labview, in all its ugliness.
I have a couple of rough sketches of what the UI could look like.  I am by no means a UI expert, but a style of dragging components around on a canvas seems to make sense for this particular problem.
Click on the images for higher resolution versions.


The first image shows what a very simple instrument might look like.  In this one we are simply getting a frequency from a MIDI input and feeding it to a sine wave generator.  The output of the sine wave generator is fed to an amplifier whose gain is set by the velocity of the MIDI note (which happens to be a number between 0.0 and 1.0).  This simple setup just gets a frequency and a velocity from MIDI and plays it out through a PCM channel as a pure sine wave.
The resulting sound is something like this (for a 440Hz sine wave as the note)

The waveform, unsurprisingly, looks like a sine wave.

The second image shows something quite a bit more complicated, though.  I didn’t mean for it to get so cluttered, but I was just trying to be verbose enough.  The scroll bar at the bottom should prevent such clutter from happening as the user should be able to spread the components out over the canvas.  Either way, the overall effect of this instrument is that it takes a frequency in through midi and creates an output composed of two separate sounds.  The first one is at the requested frequency.  It is a sine wave that is modulated by another sine wave running at double the carrier’s (the first sine wave’s) frequency with a modulation index of 2.0.  This changes the timbre of the sound using a method known as FM synthesis.  The output of this sine wave is then fed directly into the left channel output.
The second part of this sound is another sine wave, except this one is pitched 7 semitones (or a perfect fifth) above the requested frequency.  In addition, we can see that the signal is being arbitrarily amplified with a gain of 0.5, ignoring the velocity information.  Also, the output of this wave is fed into a mixer, which simply adds all its input signals together to create a composite output signal.  I had originally intended to mix the two parts together and output both to the left and right channel equally, but ran out of space while sketching.
For the example sound I have mixed the left and right channels and appropriately reduced the volume of the main wave to keep us from clipping.  Again, the selected frequency is 440Hz.
 
The resulting waveform, as expected, is much more complex.

For those interested in the more technical side of things, I have included the source file used to create the second sample here.  Not all of the code required to create this is included there, just the higher level way the classes interact.
An interesting thing I’d like to point out is that every input is treated as a function.  This means that all the inputs can vary over time.  For example, the setting for the modulation index in the complex example above is simply a constant function, though a more interesting sound could arise if you used some kind of envelope to vary the index over time.  This creates the possibility to do some very interesting things and to create some very unique effects, though not always with the most audibly pleasing results.
Well, that’s about it for now.  With the entire summer at my disposal, I hope I’ll be able to get this thing put together soon.
