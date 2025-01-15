---
layout: post
title: Audio Programming 
cover-img: ["assets/img/posts/code-wide.png"]
tags: [🎵music, 💻code]
---

This is a post about audio programming and what people of the field call DSP (digital signal processing). I just started a course and here is the place where I will be keeping my notes. 

## Lesson 01

Sound in a digital form is just a sequence of numbers (that express the amplitutde at a given point in time).

A basic workflow of how sound gets captured, saved, manipulated and reproduced is as follows:
* the microphone is a device that captures sound (changes in the air) and turns them into voltage changes.
* the ADC (analog to digital converter) will transform voltages to digital numbers (anaolog to digital signal).
* using DAWs and other computer programs, we can manipulate the digital signals and do all sorts of stuff.
* the DAC (digital to analog converter) transform the numbers to voltages essentially. The sound card we connect to our laptop acts as an ADC and DAC.
* lastly, the amplifier increases the power of a signal and the speaker projects the enhanced sound. Sometimes a speaker can act as both.

[Klang](https://github.com/nashaudio/klang) is a C++ library that makes it easy to create plugins using C++. Audio programming needs to be low latency, hence C++ is great for that. Klang offers predefined objects (like Filters, Oscillators) and syntactic sugar (`signal`, `>>` and `<<` operators) that make readable code. Using the [Klang Studio](https://nash.audio/klang/studio/) as a plugin, you can write code inside the DAW and test it. Amazing stuff!

It's valuable as a musician to understand what the fundamental effects (like filtering, tremolo) do numerically and create an intuition of how different sounds are created.

