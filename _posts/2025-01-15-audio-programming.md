---
layout: post
title: Audio Programming 
cover-img: ["assets/img/posts/speaker.jpg"]
tags: [🎵music, 💻code]
---

This is a post about audio programming and what people of the field call DSP (digital signal processing). I just started a course and here is the place where I will be keeping my notes. 

what are the plugins in the industry? 
what projects can we do at the end?
I would like more resources to check (during the lesson, code and plugins).
I would like to use JUCE for my plugin at the end. I have to find my level. 
book a studio.

### Most important books / resources

The two most important ones:
* The Computer Music Tutorial (1997/2023) by Curtis Roads
* Audio Processes (2017) by David Creasey

Some other good resources from the lessons:
* Computer Music - Synthesis, Composition, and Performance (1997) by Charles Dodge and Thomas Jerse
* Designing Audio Effect Plugins (2013) in C++ by Will Pirkle

* [Distortion In The Studio](https://www.soundonsound.com/techniques/distortion-studio)
* [wiki on ](https://en.wikipedia.org/wiki/Distortion_(music)), [wiki on ](https://en.wikipedia.org/wiki/Filter_(signal_processing)), [wiki](https://en.wikipedia.org/wiki/Digital_filter), 
* RBJ book: 
* Psychoacoustic: auditory analysis



## Lesson 05 - Modulation

## first half modulation

modulation effects: Flanger, Chorus
* combining things we have learned

* chorus of different types: multiple voices (ensemble effect) vs New Order sound 

We have already seen modulation with Whah-What and Tremolo.

modulation means controling/chaning one parameter with an LFO (low frequency oscillator, 1Hz - 10Hz). Meaning 

* A tremolo effect is created by modulating amplitude/level using an LFO for the gain parameter.
* A wah-wah effect is created by modulating filter cutoff using an LFO for the cutoff parameter.
* A flanger or chorus (mod delay) effects are created by modulating time (affects pitch and frequency) using an LFO for the delay time parameter.
	* LFO-modulated delay repeatedly speeds up and slows down the signal, in turn modulating frequency and pitch, producing a vibrato effect.


> we multiply and add with delay to keep the range to positive [0, 2*delay] (instead of [-delay, delay] range)

Let's make the Mod Delay effect.    musically this is related to vibrato.

* vibrato: the things violins or guitars do, changing the pitch slightly

```cpp
#include <klang.h>
using namespace klang::optimised;

// Mod Delay
struct ModDelay : Effect {
	Delay<192000> delay;
	Sine lfo;

	ModDelay() {
		controls = { 
			{ "Mod", Dial("Rate"), // the velocity/frequency of LFO (usually 1-10 Hz) (how fast it changes)
			         Dial("Depth") } // the amount of time we go back (usually some miliseconds)
		};
	}

	void process() {
		param rate = controls[0]*(10-1) + 1; // range: 1 to 10 Hz
		param depth = controls[1] * 0.001;  // range: 0 to 0.001 seconds (delay time in seconds)
		// using the feedforward delay
		in >> delay;
		signal mod = lfo(rate) * depth + depth;
		// output the modulated delay
		delay(mod * fs) >> out;
	}
};
```

what if depth is much bigger (sounds like scratching).
essentially the scracting sound (which can be made in DJ console) changes depth a lot.

```cpp
#include <klang.h>
using namespace klang::optimised;

// Mod Delay
struct ModDelay : Effect {
	Delay<192000> delay;
	Sine lfo;

	ModDelay() {
		controls = { 
			{ "Mod", Dial("Rate"), // 
			         Dial("Depth") }
		};
	}

	void process() {
		param rate = controls[0]*(10-1) + 1; // range: 1 to 10 Hz (frequency range of oscilation - aka how fast do we change the parrameter)
		param depth = controls[1];  // range: 0 to 1 seconds (delay time)
		// using the feedforward delay
		in >> delay;
		signal mod = lfo(rate) * depth + depth;
		// output the modulated delay
		delay(mod * fs) >> out;
	}
};
```

flanger: essentially mod delay plus original signal
* popular effect for guitars
* it creates curves in frequency domain

The "dry" singal is the original signal (before effect).
The "wet" signal is after you changed it (after effect).
Flanger was adding back the dry signal to mod delayed "wet" signal essentially.

```cpp
#include <klang.h>
using namespace klang::optimised;

// Mod Delay
struct FeedForwardFlanger : Effect {
	Delay<192000> delay;
	Sine lfo;

	FeedForwardFlanger() {
		controls = { 
			{ "Mod", Dial("Rate"), // 
			         Dial("Depth") }
		};
	}

	void process() {
		param rate = controls[0]*(10-1) + 1; // range: 1 to 10 Hz
		param depth = controls[1] * 0.001;  // range: 0 to 0.001 seconds (delay time in seconds)
		// using the feedforward delay
		in >> delay;
		signal mod = lfo(rate) * depth + depth;
		// output the modulated delay
		in + delay(mod * fs) >> out;
	}
};

```


what if we make use feedback delay (instead of feed forward?)

remember how to distinguish in block diagrams:
* feed forward: input signal goes to delay 
* feedback: output signal goes into delay (and we add it to output)

```cpp
#include <klang.h>
using namespace klang::optimised;

// Mod Delay
struct FeedbackFlanger : Effect {
	Delay<192000> delay;
	Sine lfo;

	FeedbackFlanger() {
		controls = { 
			{ "Mod", Dial("Rate"), 
			         Dial("Depth"), 
					 Dial("Feedback") }
		};
	}

	void process() {
		param rate = controls[0]*(10-1) + 1; // range: 1 to 10 Hz
		param depth = controls[1] * 0.001;  // range: 0 to 0.001 seconds (delay time in seconds)
		param feedback_gain = controls[2];

		signal mod = lfo(rate) * depth + depth;
		signal feedback = delay(mod * fs) * feedback_gain;
		in + feedback >> out;
		out >> delay;
	}
};

```

You can add noise to fill the frequency domain (and you can visulaise the changes)

For modulating effects:
* Triangle and Sine are usually th ebest 
* Square and Saw are not usually used, because it is like reseting itself (and we lose some delays)


* the New Order bass player always uses kind of flanger/chorus effect.

* feedback flanger is more Blonde effect.


> reverb and chorus are in all the dj decks

* chorus: it's like many people sing, like a choir

3 delays with different effects
* we want to seperate voices so longer delays


Chorus (essentially an ensemble of feedfoward flangers with differenet rates and depths):
```cpp
#include <klang.h>
using namespace klang::optimised;

struct MyChorus : Effect {
	Delay<192000> delay; // we can use only one delay because it's feed forward
	// otherwise we would use the output and that would affect each delay
	Sine lfo[3];
	param rates[3] = { 2.5, 3, 3.5 }; // Hz
	param depths[3] = { 0.00045, 0.00050, 0.00055 }; // secs

	MyChorus() {
		// set rates for LFOs
		for (int i = 0; i < 3; i++){
			lfo[i](rates[i]);
		}
	}

	void process() {
		in >> delay;
		signal sig = in;
		for (int i = 0; i < 3; i++){
			signal mod = lfo[i] * depths[i] + depths[i];
			sig += delay(mod * fs);
		}
		sig * 0.5 >> out; // attenuating a bit
	}
};
```

We can update with more LFOs and signals:
```cpp
#include <klang.h>
using namespace klang::optimised;

struct MyChorus : Effect {
	Delay<192000> delay;
	static const int max_amount = 5;
	Sine lfo[max_amount];
	param rates[max_amount] = { 2.5, 3, 3.5, 4.0, 4.5 }; // Hz (evenly spaced)
	param depths[max_amount] = { 0.00045, 0.00050, 0.00055, 0.00060, 0.00065 }; // secs (evenly spaced)

	MyChorus() {
		controls = { 
			Dial("Gain", 0.0, 1.0, 0.5), 
			Dial("Voices (INT)", 0.0, 5.0, 1.0)
		};
		// set rates for LFOs
		for (int i = 0; i < max_amount; i++){
			lfo[i](rates[i]);
		}
	}

	void process() {
		param gain = controls[0];
		int amount = int(controls[1]); // amount of voices (LFOs)
		in >> delay;
		signal sig = in;
		for (int i = 0; i < amount; i++){
			signal mod = lfo[i] * depths[i] + depths[i];
			sig += delay(mod * fs);
		}
		sig * (1.0/(amount+1)) * gain >> out;
	}
};
```

Enhancments:
* dial to multiply (control) rates and depth (use smooth)
* random numbers for rates
* changing rates more often

When the values for rates and depths are evenly spaced, the sound is "flanger"-like.
The more evenly spaced the rates and depths are, the more "flanger"-like effect.

You can modulate distorion and filters as well.

Different way (extension). Filtering and distortion be used as well.
* Before or after distorion?
* distortion adds harmonic content (adding filter on the start will make this content shine).
* adding a filter on the end (it's kind of EQ, if you add too many harmonics, you erase them).

U2 discoteque is kinda like "modulated distorion?".

### extras 

The manual by Edelweiss: a psynical way of how to make hit songs
* by Bill Drummond
* "bring me edelweiss" by Edelweiss (stealing SOS by abba, yodel by sound of music, ...)
* the also wrote "3 A.M. Eternal" (guns of Mu Mu)


* audio processes has many block diagrams
* The computer music tutorial by Curtis Road (goes academically, refernces)
* will pirkle books (specific things)


TODO idea: Use AI to optimise parameters of many models. And then start producing plugins.

## Lesson 04 - Delay

In this lesson we are going to look at delay:
* We will look simple echo effects and then feedback.
* We will make a reverb as well (mono, stretch goal to make stereo).
* We will look at impulse responses.

> Delays buffer a signal in memory to recall later, giving access to previous samples...

* Essentially, we move a sound later in time.
* The buffer will hold all the necessary values of the past that we will need.
* The implementation happens using a ring buffer (essentially shifting the start of the list instead of all the elements).

* The z-1 block diagram is a one-sample delay.
* The time is usually a variable of the dealy system (how long ahead to reproduce the past sound).


Let's implement an Echo effect. In it's simplest form it is adding a quieter dealyed signal (attenuated).
The echo practially gives a good sense of space (like listening music into an open area). That is because in real life, we here many echoes of every sound, because it bounces on surfaces around the room.

```cpp
#include <klang.h>
using namespace klang::optimised;

struct MyEcho : Effect {
	Delay<192000> delay; // we assume maximum sample rate of 192kHz, so our memory should be at least that to hold 1 second of past data

	MyEcho() {
		controls = { 
			{ "Echo", Dial("Time", 0.0, 1.0, 0.5),	// in seconds
			          Dial("Gain", 0.0, 0.99) }
		};
		presets = { { "Low Time / Low Gain",    { 0.23, 0.15 } }, // loading interesting presets, can be selected from the UI
					{ "Mid Time / High Gain", { 0.55, 0.88 } },
		};
	}

	void process() {
		param time = controls[0];
		param gain = controls[1];

		// every time we add something to the delay, the head moves	
		in >> delay;  // send input to the delay
		in + gain * delay(time * fs) >> out; // echo is the delayed signal but quitter
	}
};
```

We now want feedback, that why save the output to the saved samples (in delay array/buffer).
This way, we save all of the previous echoes. 

```cpp
#include <klang.h>
using namespace klang::optimised;

struct MyEcho : Effect {
	Delay<192000> delay;

	MyEcho() {
		controls = { 
			{ "Echo", Dial("Time"),
			          Dial("Gain", 0.0, 0.99) }
		};
	}

	void process() {
		param time = controls[0];
		param gain = controls[1];
				
		in + gain * delay(time * fs) >> out; // echo is the delayed signal but quitter
		out >> delay; // feeed the output to the delay
	}
};
```

> note that the output signal is a summation of two signals. If the gain is high enough, the signal can exponentially start growing and crash our system/speakers etc. 

This is a good moment to pause and talk about resonance.
It's possible that with bad timing, the amplitudes line up, meaning that: 
* the positives will line up with positivies
* the negatives will line up with negatives
This could happen e.g if the time parameter is equal to 1 bar of our sample.
This lining up would make the sound to loud (resonance)

> Resonance just means "things vibrating together, aka in the same frequency". It can be good or bad, depneding on the scenario. 
* It's bad when it magnifies the amplitude to our signal (and resonant delay produces sounds that could be described as "ringy" and are unpleasant). 
* It's good when it makes the sound powerful (could be described as "sharp").

> “Resonance” is also used in spoken English to suggest agreement. “Her ideas really resonate with how I feel.” 

**Huge mind connection**: if time is really low in the echo filter, it effectively becomes a filter, specifically and IIR (infinate impulse response) filter

Easter egg song: "this is the sound of C" by Confettis in 1980s.


In the ping pong, the left and right signals are delayed and **reversed** simulateanously. This creates a really interesting effect.

```cpp
#include <klang.h>
using namespace klang::optimised;

struct MyPingPong : Stereo::Effect {
	Stereo::Delay<192000> delay;

	MyPingPong() {
		controls = { 
			{ "Left",  Dial("Time"),
			           Dial("Gain", 0, 0.99) },
			{ "Right", Dial("Time"),
			           Dial("Gain", 0, 0.99) }
		};
	}
	
	// feedforward delay (we only read from an echoless sample)
	void process() {
		stereo::signal time = { controls[0], controls[2] };
		stereo::signal gain = { controls[1], controls[3] };
		
		in.l >> delay.l;
		in.l + delay.l(time.l * fs) * gain.l >> out.l;

		in.r >> delay.r;
		in.r + delay.r(time.r * fs) * gain.r >> out.r;
	}
```
```cpp
#include <klang.h>
using namespace klang::optimised;

struct MyPingPong2 : Stereo::Effect {
	Stereo::Delay<192000> delay;

	MyPingPong2() {
		controls = { 
			{ "Left",  Dial("Time"),
			           Dial("Gain", 0, 0.99) },
			{ "Right", Dial("Time"),
			           Dial("Gain", 0, 0.99) }
		};
	}
	
	// feedback delay (we read samples that contain previous echoes)
	void process() {
		stereo::signal time = { controls[0], controls[2] };
		stereo::signal gain = { controls[1], controls[3] };
		
		in.l >> delay.l;
		in.l + delay.l(time.l * fs) * gain.l >> out.l;

		in.r >> delay.r;
		in.r + delay.r(time.r * fs) * gain.r >> out.r;
	}
};
```

flatter echo (kind of ringy echo)
* in bathrooms with ringy, tinny echo, because the tile parallel wall have many echoes

Easter egg song: "Electric counterpoint" by Steve Reich

### impulse 

Impulse is a momentary burst of input (a sample of amplitude 1 for example). An impulse response is the output of a system over time in response to an impulse.

You can plot the impulse to understand the system.
Since the delay will never actually become zero (based on zeno's paradox) but is decreased gradually through the gain, we plot until `t60`. -60 dB is considered silence (for human hearing) and `t60` is the amount of time for an impulse response to reach -60 dB (silence).


* Midi is a music protocol. Every note (key in piano) is assigned an integer. Middle C is 60. It was created in 1983 and is widely popular today.


For the following effect, we make the delay pattern of multiple feedforward signals.

```cpp
#include <klang.h>
using namespace klang::optimised;

struct Patterns : Effect {
	Delay<192000> delay;

	Patterns() {
		controls = { 
			{ "Delay 1", Dial("Time"), Dial("Gain") },
			{ "Delay 2", Dial("Time"), Dial("Gain") },
			{ "Delay 3", Dial("Time"), Dial("Gain") },
		};
	}

	void process() {
		param time[] = { controls[0],controls[2],controls[4] };
		param gain[] = { controls[1],controls[3],controls[5] };
		
		in >> delay;
		signal taps = 0;
		for (int i = 0; i < 3; i++){ // using a for loop
			taps += gain[i] * delay(fs * time[i]);
		}
		in + taps >> out;
	}
};
```

Select "Plot Impulse Response" in klang to see plot.
We also add pass the echo through a filter (to gradually keep only the low frequencies of the echoes, which "makes the sound duller").

```cpp
#include <klang.h>
using namespace klang::optimised;

struct MyEchoWithFilter : Effect {
	Delay<192000> delay;
	LPF filter;

	MyEchoWithFilter() {
		controls = { 
			{ "Echo", Dial("Time"),
			          Dial("Gain", 0.0, 0.99) }
		};
	}

	// this runs less frequently (once per buffer) 
	void prepare(){
		filter.set(controls[2]);
	}

	void process() {
		param time = controls[0];
		param gain = controls[1];
				
		in + gain * delay(time * fs) >> out; // echo is the delayed signal but quitter
		out >> delay; // feeed the output to the delay
	}
};
```

It's important to not do heavy/costly operation in process function. 

* Changing the filters tunes are expensive, (becuase of calculating complicated float parameters using functions like tan, sin, etc). That's why we use prepare, that runs "once every buffer".
* In DAWs we define "block size" or "buffer size" (e.g. x = 1024, 2048 etc). The DAW will give x samples to the plugin as a single batch to calcualte. The plugin will then give the batch back. (The larger the buffer, the less times they data around and therefore faster computation. However, really big buffer size is bad because it adds latency to the system, because you will have to calculate "buffer size" first to get back a response and start producing the sound ).
* Also, do not do costly operations like allocation in the process function, because it's slow. We always preallocate memory. 


### Reverberation (reverb)

In a real physical space, there are infinate reflections (soundwaves bounce on multiple surfaces to reach our ears). Reverb effect simulates the impulse response of space.
It uses delays to replication the pattern of reflections in real life.

Direct sound, early reflections and reverberation.

How are we going to implement it?
* We could use loads of feedforward delays. But it's too many sounds. (in a cathedral we would like huge amount of them).
* We could use feedback delay. But all the dealys are regularly spaced and you get resonance. (which is not similar to real rooms).
* Therefore, we use a combination of these with spacings.
* we can also add some dampening (a filter in the end to make it duller).


* the best amplifier (you want total harmonic distortion "how much does it distort sounds", the less the better)
* the synthesizers


We use random numbers for parameters (prime numbers that don't even have common factors).

Analogue equipment 
Resonance vs Ringing. (avoid ringing)


* Feeback delay networks (FDNs). Network of feedback delays.
It can be a very good reverb.




### Convolution Reverb

Another approach of making reverb that mimic real spaces is known as convolution reverb.
* we record an 8 second audio sample (e.g. `.wav`) of an impulse and 
* to accomplish that we use a starting pistol or popping ballon or a crazy good speaker to produce an impulse sound in a room
* the recording is essentially "the impulse response" 

* by combining the impulse response and tricks such as FFT (fast fourier transform), we can reproduce the reverb of any sound in that room 
* FFT and reverse FFT move the sound between the time and frequency domains. And calculating a convultion (which is an expensive/slow calculation) in the time domain becomes a simple mutliplication in frequency domain (which is a cheap/fast calculation to execute)
* computers can execute FFT relatively fast and use this method nowadays (40-50 years ago it was impossible). 
* However, artificial reverbs still play a role, because people like experimenting with sounds (they have a different taste).


There are so many reverb plugins in the market (you can pay as much as 500$ for some). In general, it's not really hard to make reverbs. But what is really hard to choosing good presets. It's difficult to tune the dials is difficult for different setting (e.g. a small room or a cathedral, etc). 
These algorithms were kept secret for long time. 
An popular design for reverb is called **Schroeder-Moorer**. It uses an all pass filter by cascading a feedback comb-filter with a feedforward comb-filter. A popular open source reverb is called free-verb ([stanford site](https://ccrma.stanford.edu/~jos/pasp/Freeverb.html)) (open source [github](https://github.com/sinshu/freeverb)).

Good idea for project: it's difficult to create usable smooth dials (e.g. you could have 3 dials, room size, resonance/brightness, echo). 

```cpp
#include <klang.h>
using namespace klang::optimised;

struct MyReverb : Effect {
	Delay<192000> feedforward, feedback;
	LPF filter;

	param times[8] = { 2.078,  5.154,  5.947,  7.544,
		                8.878, 10.422, 13.938, 17.140 }; // in miliseconds
	param gains[8] = {  .609,   .262,  -.3600, -.470, 
		                 .290,  -.423,   .100,   .200 };
	MyReverb() {
		controls = { "Feedback", Dial("Time", 0, 0.5, 0.4), // in seconds
		                         Dial("Gain", 0, 0.4, 0.1),
		                         Dial("Cutoff", 500, 5000, 1500) 
		}; // the controls are only for the feedback (gain, time and cutoff of filter)
		                         
		presets = { { "Bathroom",    { 0.276, 0.057, 3835.062 } },
					{ "Lively Room", { 0.309, 0.060,  806.557 } },
					{ "Air Vent",    { 0.496, 0.089, 2807.678 } },
					{ "Parking Lot", { 0.216, 0.220, 1367.304 } },
		};
	}

	// called once per buffer
	void prepare() {
		filter.set(controls[2]);
	}

	signal early() {
		signal mix = in;
		// feed input to delay
		in >> feedforward;
		// add early reflections
		for (int i = 0; i < 8; i++){
			mix += gains[i] * feedforward( times[i] / 1000 * fs);
		}
		return mix;
	}

	signal late() {
		param time = controls[0];
		param gain = controls[1];
		signal mix = feedback(time*fs*0.0001) * gain;
		return mix >> filter; // the feedback signal
	}

	void process() {
		early() + late() >> out >> feedback;
	}
};
```


```cpp
#include <klang.h>
using namespace klang::optimised;

struct MyReverb : Effect {
	Delay<192000> feedforward, feedback;
	LPF filter;

	param times[8] = { 2.078,  5.154,  5.947,  7.544,
		                8.878, 10.422, 13.938, 17.140 }; // in miliseconds
	param gains[8] = {  .609,   .262,  -.3600, -.470, 
		                 .290,  -.423,   .100,   .200 };
	MyReverb() {
		controls = { "Feedback", Dial("Time", 0, 0.5, 0.4), // in seconds
		                         Dial("Gain", 0, 0.4, 0.1),
		                         Dial("Cutoff", 500, 5000, 1500) 
		};
		                         
		presets = { { "Bathroom",    { 0.276, 0.057, 3835.062 } },
					{ "Lively Room", { 0.309, 0.060,  806.557 } },
					{ "Air Vent",    { 0.496, 0.089, 2807.678 } },
					{ "Parking Lot", { 0.216, 0.220, 1367.304 } },
		};
	}

	// called once per buffer
	void prepare() {
		filter.set(controls[2]);
	}

	signal early() {
		signal mix = in;
		// feed input to delay
		in >> feedforward;
		// add early reflections
		for (int i = 0; i < 8; i++){
			mix += gains[i] * feedforward( times[i] / 1000 * fs);
		}
		return mix;
	}

	signal late() {
		param times[3] = { 0.2, 0.324, 0.777 }; // seconds, (random numbers)
		param gains[3] = { 1.0, 0.6, 0.2 };
		param time_c = controls[0];
		param gain_c = controls[1];
		signal mix = 0;
		for (int i = 0; i < 3; i++){
			mix += feedback( time_c*times[i] * fs) * gain_c*gains[i];
		}
		return mix >> filter; // the feedback signal
	}

	void process() {
		early() + late() >> out >> feedback;
	}
};
```

Use bypass to actually understand the difference between the reverb and the original sound. Also, use the tool to check the impulse response.

If time is really high, we need more taps (for the forward signal)
* TODO: think about this
* TODO: make a control for the amount of taps (the tap times should be random and prime numbers to avoid resonance that leads to ringy sound)

The reverb is one of the holy grails in DSP design.


### Questions / Todos / Ideas

* TODO: make a fast way to compute FFTs and convolutional filters (computers can do this now)
* TODO: use AI for parameter tuning of reverb to create decent plugins that mimic real reverbs

## Lesson 03 - Filters

General notes:
* Filters are used to remove frequency content from a signal.
* The plot shows the spectrum of a signal processed with a low-pass filter (LPF). The filter attenuates or removes a range (band) of frequencies, shown faded.
* The cutoff marks the limit of the pass band (frequencies allowed through the filter), and beginning of the transition into the stop band (where frequencies are fully rejected). (usually it's not just a straight line down)
* slope : dB / octave ("higher-order" filters have steeper slopes for)
* Some filter designs — especially in synthesis — exhibit resonance



Resonance (Q)
* the sharpness of the spike


In EQ, we want to correct the sound.
* Ducking / Boosting different frequencies

* low-pass (high-cut) filter
* high-pass (low-cut) filter
* band-pass filter (center frequency and bandwidth) (cuttoff vs b)
* band-reject filter (or band-cut filter) (notch filter)

> Analog filters do not have 


For a low pass filter, we want to smooth it out -> moving average
* it is based in previous values, so they affect frequency but also add phase (move the wave to the right)
* it's a frequency depended delay (this phase can call )


* there is also an all-pass filter (it only adds a delay)
 	* decoralte tails, and they are the basis of phasers
	* TODO: code all-pass filters

* z-1 means delay by one in block diagrams (z for zahlen: to count in German)

#### FIR vs IIR

filters are made with math
* finite-impulse response (FIR)
* each time we add a dealy, we increase a delay

* the following is a 3rd order filter:
$$y(n) = 1/3 * x(n) + 1/3 * x(n-1) + 1/3 * x(n-2)$$

* you can add bigger or smaller factors  at the end

* infinite-response filter (IIR) (feedback loop)

* constants a,b to control how much
* this filter would explode if b > 1
* there is recursion, the formula would be:
$$y(n) = a*x(n) + b*y(n)$$
* usally $b = 1 - a$
* the phase moves logarithmically



impulse: more directly related to a delay


* it refers to the single input line

* out at the initial moment is one

```cpp
param a = controls[0]
param b = 1-a
a*in + b*out >> out;
```

* we don't want a linear slider (the interesting range is actually when a ~ 0.01 - 0.10 )
* frequency we perceive logarithmically
* the higher b, the higher the effect of the past (and lower the present note)
* the higher a (the lower b), the higher the effect of the new input (and lower the past) 

```cpp
// squaring/cubing a control can make it better
param a = controls[0]
a = a*a
param b = 1-a
a*in + b*out >> out;
```

```cpp
#include <klang.h>
using namespace klang::optimised;

struct MyIIR : Effect {
	signal last = 0; // last output

	MyIIR() {
		controls = { 
			Dial("a"),
		};
	}

	void process() {
		param a = controls[0];
		a = a*a; // to make slider more usable
		// warpping (bending): cubbing or squarring the slider (it's works when we are between 0 and 1)
		param b = 1-a;
		a*in + b*last >> out;
		out >> last;
	}
};
```



> We can minus the lower frequencies we produced to only keep the high frequencies. And that how we produce a high pass filter. !! AMAZING !!

```cpp
#include <klang.h>
using namespace klang::optimised;

struct MyIIR : Effect {
	signal last = 0; // last output

	MyIIR() {
		controls = { 
			Dial("a"),
		};
	}

	void process() {
		param a = controls[0];
		a = a*a; // to make slider more usable
		param b = 1-a;
		signal lpf = a*in + b*last;
		last << lpf;

		singal hpf = in - lpf;
		hpf >> in;
	}
};
```

It's very hard to make filters.
Relatively few DSP engineer understand how they work.


In alaogue systems, you use capacitors and resistances. Trying to fake analogue (non linear stuff).

That frequncy get boosted. It's a desirable effect.


#### biquad filter

this has FIR and IIR.
the 5 coefficients can create anything (lowpass, highpass, bandpass).

RBJ biquad filters

* [klang makes it](https://github.com/nashaudio/klang/blob/f1f9b6e7896345664537eabaa03be0e075371420/klang.h#L4578)

* it has code so you map the slider with Hz.
* you can warp it.

We are going to be using it as a black box.



> White noise is a singal generation. Random numbers from 0 to 1.


Mapping values.

We are listening to cut sounds.
We want to smoothen the cutoff. (we add a filter essentially)


* Dials update 30 times a second
* process() executes 44000 times a second

> Rule of Thumb: 100ms latency feels instant to humans


doubling the filter (makes the slope more steep)
* each one will distort the phase
* treble and bass (if you link them up to be inverse)




dynamic level compressors 
* first we had limiters (automatic way to stop too loud signals)
* a compressor does this artificially
* a bit like distortion but they work onlevel

* on mixing desks with 
* you can do it with voice, 
* gritty, heavy sound, compreses

* multi-band processing (making)
* bass boost
* exciter (harmonic enahncer)
* eq (31 band equalisers)
	* the transition bands should sum up to 1

* let's make an eq version of the dj console
	* first design costs to phases
	* second approach: low-pass, high-pass and mid is subraction of them.

> each time you pass the signal, you get the cost of one phase. For FIR you have predictable phase. But with IIR, it's unpredictable (the price you pay for efficiency).

### More Qs:

* do you have any courses on signals? in YT? There is that guy who is really good.
* how can I make a compressor? TODO

* encapsulation: hide the internals, and don't allow them change the inside (python has it by convention)
* RTFM: read the fucking manual


## Lesson 02 - Distortion

* Distortion describes any process that changes (distorts) the shape of the waveform.
* The pitch (effectively the frequency and wavelenght) do not change.
* Gain is a linear effect, meaning it doesn't change the shape of the wave — only its scale.

FM : important in synthesis
AAnalogue systems are inherently non-linear (amps, mics, pick-ups, speakers, analog synths). Every copy/transmition you make is distorted. But it might be perceived as "warmth" or "has character". 
We can create distortions. 

Everybody likes mp3 and stream which are worse than CDs. Mp3 is compressed (it throws away all the content we cannot hear).

Everybody likes a bit of compression.

mpeg compressed and mono effectively. it's heartbreaking, we 
it's encoded to be scratch resistent.

`.wav` or `.aif` are CD quality like. When you are at the studio, always use these and at the end convert to `.mp3`.

bitcrushing - daft punk used it a lot.

clipping : gain with limit at (-1, 1). if you apply a lot of it, effectively you create a squarewave. (the basic waves are sinewave, squarewave, sawwave). Clipping is a classic type of distortion. it creaets edges in the sinewave.

```cpp
// Clipping.k
#include <klang.h>
using namespace klang::optimised;

struct Clipping : Effect {

	// Initialise plugin (called once at startup)
	Clipping() {
		controls = { 
			Dial("Clipping", 0.0, 20.0, 1.0),
		};
	}

	// Apply processing (called once per sample)
	void process() {
		param gain = controls[0];
		max(-1.0f, min(1.0f, in*gain)) >> out;
	}
};
```

We can represent distortions as a transfer function that maps input amplitude (x-axis) to output (y-axis). `y = x`, known as the identity (no change). `y = 0.5x` would give gain by half. `y= 2x` would make the amplitude go away.

In order to push the audio signal outside of bounds in order to be clipped, we have to put gain (amplitude). in the clipping scenario, it's called `overdrive` (beacause it makes signal go over the boundries).

`clipping` is great with drums.

```cpp
#include <klang.h>
using namespace klang::optimised;

signal clip(signal x){
	signal out;
	if (x > 1)
		out = 1;
	else if (x < -1)
		out = -1;
	else
		out = x;
	return out;
}
	
struct Clipping : Effect {
	Clipping() {
		controls = { 
			Dial("Overdrive", 1, 11, 1),  // controls how much of input goes `over` the threshold
			Dial("Output Gain", 0, 1, 1) // it's really loud, maybe you want to make it quieter
		};
	}

	void process() {
		signal in_gain = controls[0];
		signal out_gain = controls[1];
		
		out_gain * clip(in*in_gain) >> out;
		
		clip >> graph(-2,2);
	}
};
```

squarewave is richer in terms of harmonics. (todo: listen to the difference between square and sine).
What are harmonics? frequencies that are related to each other (multiples of the base). this is how we perceive music and pitch.

sinewave = has the fundamental (1)
sawwave = has decreasing harmonics (1,2,3,4,5,6, ...) or (2,4,6,8,10, ...)
squarewave = has only the odd harmonics (1,3,5, ...) or (2, 6, 10, ...)


* if you double the fundamental, that is an octave higher.
* To produce even harmonics, our transfer function needs to be asymmetric.

* Even if a violin and a piano play the same note, it sounds different (although similar pitch). it's a different vibe. in music we say different 'timbre'.

soft clipping to create different sounds. 
(saturation, ).

we can stretch vertically and horizontally (by mulitplication)
and moving around a function (by adding).

[desmos](https://www.desmos.com/calculator) is great to visualise all of these.


MaxMSP and RNBO are some classic software. `param` means controller vs `signal` which refers to audio.


in analogue, how does this happen?


you can have multiple distortions. instead of switch, you can use cross-fade between two signals (linear combination between two singals).


other distortions: foldback, bitcrusing (quantizing with steps).


threshold for pain: 120dB
it can actually happen

vertical resolution: (amount of horizontal lines, amplitudes that we can have):
* the standard now is 16-bit
* 8-bit was an old thing with character
* 12-bit were used by earlier samplers
* 14-bit was a thing
* 16-bit became a standard 1 year after CDs were invented 




double dynamic range is 6 decibels. 
gives 96 


* Place theory
* There are a lot we don't know.

book:
* Psychoacoustic: auditory analysis
* David Chris: audio processes book

TODO: hunt some transfer functions


### symmetry and harmonics


Symmetry vs. Harmonics
Hi George,

I've tried to find an accessible explanation for why only odd harmonics are produced by symmetric transfer functions, but there's not much that doesn't require an understanding of Fourier theorem (which can be used to transform a time domain signal into the frequency domain, and thus find which frequencies exist in a signal - e.g. FFT, which stands for Fast Fourier Transform and allows us to plot spectrums and sonograms). We'll touch a little on Fourier in Additive Synthesis, but without going into the maths in detail.

I gave ChatGPT a crack at summarising it, which I've included below. It provides a little more context, but still needs to you understand Fourier. In practice, for now, you really only need to know the take homes (symmetric = odd harmonics, assymetric = both).

Hope this helps!

Best,
Chris

"Symmetrical transfer functions produce only odd harmonics because of their inherent mathematical properties. A transfer function is symmetrical when f(−x)=−f(x), meaning it is odd with respect to the origin. When a sinusoidal input passes through such a function, the resulting waveform contains only odd powers of the input signal in its expansion (e.g. x, x ^ 3, x ^ 5, etc.). These odd powers correspond to odd harmonics in the Fourier series.

To produce even harmonics, the transfer function must have an asymmetric component. Asymmetric functions include terms that are even powers of the input (e.g. x ^ 2, x ^ 4, etc.), which correspond to even harmonics in the output. Asymmetry introduces this even-order nonlinearity, breaking the symmetry condition f(−x)=−f(x), and allowing the creation of both odd and even harmonic content.

In summary:

Symmetrical transfer functions (odd functions): Produce only odd harmonics.
Asymmetric transfer functions (even + odd terms): Produce both even and odd harmonics."

## Lesson 01 - Basics

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



