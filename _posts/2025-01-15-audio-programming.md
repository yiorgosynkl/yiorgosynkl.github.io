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

// SawWithFilter.klang

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
// make an oscillator that goes really slowly and handles the gain

// make it so at 0 the line is at 1
// and at full tremolo, it is between 0 and 1

#include <klang.h>
using namespace klang::optimised;
	
struct Tremolo : Effect {
	Sine lfo;

	Tremolo() {
		controls = { 
			Dial("Depth", 0, 0.5, 0),
			Dial("Rate", 1, 10, 6),
		};
	}

	void process() {
 		param rate = controls[1]; // Hz (cycles per second)
		// signal mod = lfo(rate); // 1 to 10 Hz (cycle per second) (ring modulation) (Dr. Who voice effect) (gives one extra frequency)
		// signal mod = lfo(rate) * 0.5 + 0.5; // to make sine of of lfo between 0 and 1 (two mirror bands in frequency space)
		signal mod = lfo(rate) * 0.5 + 0.5; // Use the depth 
		in * mod >> out;
	}
};

// TODO: make it so at 0 the line is at 1
// and at full tremolo, it is between 0 and 1

// ring 
```
