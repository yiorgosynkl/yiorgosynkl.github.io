---
layout: post
title: Audio Programming 
cover-img: ["assets/img/posts/speaker.jpg"]
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


```cpp
// Gain.klang

#include <klang.h>
using namespace klang::optimised;

struct Gain : Effect {

	Gain() { 
		controls = { 
			Dial("Gain") 
		};
	}

	void process() { 
		param gain = controls[0];
		gain >> debug;
		in * gain >> out;
	}

};


// Pan.klang
#include <klang.h>
using namespace klang::optimised;

struct Pan : Stereo::Effect {
	Pan() { 
		controls = { 
			Dial("Pan") 
		};
	}

	void process() { 
		param pan = controls[0];
		signal mono = 0.5 * (in.l + in.r);
		(1 - pan) * mono >> out.l;
		pan * mono >> out.r;
	}

};

// there is an issue with 0.5 pan having half the amplitude as the initial signal (volume drop)
// "Equal power panning law": is when panning doesn't lose it's volume.
// There is also -3dB, -4.5dB -6dB, depending on the volume lost.
// https://en.wikipedia.org/wiki/Panning_law

// MyEffect.klang : saw with filter

#include <klang.h>
using namespace klang::optimised;

struct MyEffect : Effect {
	Saw osc;    // (sawtooth oscillator)
	LPF filter; // (low-pass filter)

	MyEffect() { 
		controls = { 
			Dial("Cutoff", 100, 1000, 440) // min, max, start
		};
		osc.set(440); // set to 440Hz
	}

	void process() {
		param f = controls[0];
		filter.set(f);
		osc >> filter >> out;
	}
};

// Tremolo.klang
// an oscillator is essentially a signal (saw, sine, ...)
// a lfo is low frequency oscillator (signal with f = 1 - 10Hz)
// tremolo is a signal where the amplitude is modulated by an LFO 
// (essentially an oscillator that slowly increases/decreases the gain)

#include <klang.h>
using namespace klang::optimised;

struct Tremolo : Effect {
	Sine lfo;

	// Initialise plugin (called once at startup)
	Tremolo() {
		controls = { 
			Dial("Mod Rate", 1.0, 10.0, 6.0),
			Dial("Mod Depth", 0.0, 0.5, 0.5),
		};
	}

	// Prepare for processing (called once per buffer)
	void prepare() {
		
	}

	// Apply processing (called once per sample)
	void process() {
		param rate = controls[0];
		param depth = controls[1];
		
		// different types of modulation
		// signal mod = lfo(rate); // lfo osc between [-1, 1]
		// signal mod = lfo(rate) * depth; // lfo osc between [-depth, +depth] ( [-0.5, 0.5] for depth = 0.5 )
		// signal mod = lfo(rate) * depth + 0.5; // lfo osc between [0.5-depth, 0.5+depth] ( [0, 1] for depth = 0.5, [0.5,0.5] for depth = 0.5 )
		signal mod = lfo(rate) * depth + (1-depth); // lfo osc between [1-2*depth, 1] ( [0, 1] for depth = 0.5, [1,1] for depth = 1 )
		mod >> debug;
		in * mod >> out;
	}
};
```

In the professional community, the industry standard framework for developing plugins is [Juce](https://juce.com/). Juce is used by Traction, maxMSP, FocusRight, etc. Every year during November there is a [ADC (audio developer conference)](https://audio.dev/), and it's the number 1 networking opportunity. In 2025 it takes place in Bristol. 


Why is 44.1kHz (44,100 Hz) the standard sampling frequency? Explanation in [wiki](https://en.wikipedia.org/wiki/44,100_Hz). In brief, the Nyquist–Shannon sampling theorem says the sampling frequency must be greater than twice the maximum frequency one wishes to reproduce. To capture the human hearing range of roughly 20 Hz to 20,000 Hz, the sampling rate had to be greater than 40 kHz. Furthermore, the ideal filter is practically impossible to implement, so in practice a transition band is necessary, where frequencies are partly attenuated (reduced in force). The wider this transition band is, the easier and more economical. The 44.1 kHz sampling frequency allows for a 2.05 kHz transition band.

## Lesson 02 | Distortion

Ring modulation and distortion. 

## Lesson 03 | EQ

## Lesson 04 | Delay
## Lesson 05 | Flanger
## Lesson 06 | Additive
## Lesson 07 | Subtractive
## Lesson 08 | FM
## Lesson 09 | Physical 
## Lesson 10 | Plugin Project



