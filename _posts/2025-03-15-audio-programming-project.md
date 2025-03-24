---
layout: post
title: Audio Programming 
cover-img: ["assets/img/posts/speaker.jpg"]
tags: [🎵music, 💻code]
---

Project code. It's additive synthesis and keeping presets.

more todos:
* use flanger instead of vibrato
* use envelopes (ADSR)

* search better base samples
* add bitcrash (and other types of distortions)
* add filter (low, mid, high) and master gain

### latest version - with wah wah effect

Wah effect and delay/vibrato.
Dials are placed specifically now.

```cpp
#include <utility>
#include <vector>
#include <klang.h>
using namespace klang::optimised;

using HMap = std::vector<std::pair<float, dB>>; // harmonic pairs (Hz / dB) (first pair is the fundamental)

namespace hsg { // harmonics groups

// -------- visually investigate frequency spectrum with Audacity --------
const HMap trumpet = {
	{268, -41.5}, {528, -30.5}, {788, -28.4}, {1048, -24.1}, {1309, -21.4}, 
	{1570, -26.2}, {1829, -30.9}, {2090, -40.5}, {2349, -48.6}, {26.11, -53.1}
};

const HMap sopsax = {
	{270, -23.6}, {532, -28.2}, {795, -26.5}, {1057, -38.6}, 
	{1320, -56.0}, {1590, -50.1}, {1860, -56.8}, {2121, -53.9}
};

const HMap flute = {
	{325, -19.5}, {658, -31.3}, {982, -36.8},
	{1310, -47.8}, {1636, -47.2}, {1960, -42.7}
};

const HMap xylophone = {
	{416, -23.4}, {586, -54.0}, {673, -58.5}, {1046, -45.7}, {1260, -14.4}, 
	{1582, -58.5}, {1763, -60.8}, {2565, -26.1}, {2736, -16.6}, {3454, -54.4}, 
	{4332, -48.6}, {4837, -45.3}, {5419, -42.1}, {6100, -39.5}, {6666, -52.3}, {7428, -55.4}
};

// -------- i also asked ChatGPT to produce some sounds --------
// Organ (Smooth, Hollow Sound)
// A pipe organ-like sound is created using strong odd and even harmonics with a smooth falloff.
const HMap gpt_organ = {{1, 0},{2, -6},{3, -12},{4, -18},{5, -24},{6, -30}};

// Clarinet (Warm, Hollow, Woody)
// Clarinet timbre is mainly composed of odd harmonics.
const HMap gpt_clarinet = {{1, 0},{3, -8},{5, -15},{7, -22},{9, -28}};

// Electric Guitar (Plucky, Bright, Metallic) 
// A bright, twangy sound with strong odd and even harmonics, slightly detuned higher harmonics
const HMap gpt_electric_guitar = {{1, 0},{2, -5},{3, -9},{4, -12},{5, -15},{6, -18},{7, -20}};
// Trumpet (Brassy, Bright, Sharp)
// A brassy sound has strong odd harmonics with slow amplitude decay.
const HMap gpt_trumpet = {{1,0.0},{2,-4.0},{3,-8.0},{4,-12.0},{5,-14.0},{6,-17.0},{7,-20.0}};
// Synth Pad (Soft, Airy, Dreamy)
// A soft pad sound with gentle harmonic content.
const HMap gpt_synth = {{1,0.0}, {2,-10.0}, {3,-15.0}, {4,-20.0}, {5,-25.0}, {6,-30.0}, {7,-35.0}};

}

struct MyModularOscillator : Oscillator {
	std::vector<Sine> osc;
	HMap hs; // harmonics
	int enhance_dBs; // enhance dBs
	
	MyModularOscillator(HMap in_hs = hsg::gpt_synth){
		hs = in_hs.size() > 0 ? in_hs : hsg::gpt_synth; // we need at least one harmonic
		osc = std::vector<Sine>(hs.size(), Sine());
	}

	void set(param f){ // runs one time when we press the note
		const float base_freq = hs[0].first;
		for (int i = 0; i < hs.size(); i++){
			const auto& [freq, gain] = hs[i];
			osc[i].set(f * (freq / base_freq)); // normalising
		}
	}

	void process(){
		signal mix = 0;
		for (int i = 0; i < hs.size(); i++){
			const auto& [freq, gain] = hs[i];
			if (freq < fs.nyquist){ // the sample rate limits the maximum frequency we can produce
				mix += osc[i] * (gain -> Amplitude);  // attenuate the amplitudes of the frequencies
			}
		}
		mix >> out;
	}
};


struct MySynth : Synth {
	struct SimpleNote : public Note {
		MyModularOscillator oscs[3];
		MyModularOscillator* osc;
		param osc_gain; // to store velocity, will be used as gain
		
		// amplitude envelope
		ADSR adsr;
		
		// ---- wah_fx -----
		Sine wah_lfo;
		LPF wah_filter;
		
		// ---- delay fx ----
		Delay<192000> delay;
		Sine delay_lfo;

		SimpleNote()
		: oscs{MyModularOscillator{hsg::gpt_synth}, MyModularOscillator{hsg::trumpet}, MyModularOscillator{hsg::xylophone}}
		{
			osc = nullptr;
		}

		event on(Pitch pitch, Amplitude velocity) {
			param frequency = pitch -> Frequency;
			param type = controls[0];
			if(type == 0){
				osc = &oscs[0];
			} else if (type == 1) {
				osc = &oscs[1];
			} else {
				osc = &oscs[2];
			}
  	   		osc->set(frequency);
			osc_gain = velocity * velocity * velocity; 
			adsr(0.25, 0.25, 0.8, 0.5); 
            adsr(controls[8], controls[9], controls[10], controls[11]);
		}
		
		event off(Amplitude velocity) {
			adsr.release();
		}
		
		signal wah_fx(signal in_sig){
			param wah_switch = controls[1];
			bool is_wah_off = (wah_switch == 0);
			if (is_wah_off){
				return in_sig;
			}
			
			signal out_sig = 0;
			param wah_rate = controls[2];
			param wah_depth = controls[3];
			param wah_centre = controls[4];
			
			wah_depth = min(wah_depth, wah_centre); // to prevent usage over limits
			signal wah_mod = wah_lfo(wah_rate) * wah_depth + wah_centre;
			in_sig >> wah_filter(wah_mod) >> out_sig;
			
			return out_sig;
		}

		signal delay_fx(signal in_sig){
			in_sig >> delay; // save feedforward delay
			
			param delay_switch = controls[5];
			bool is_delay_off = (delay_switch == 0);
			if (is_delay_off){
				return in_sig;
			}
			

			param rate = controls[6]; // range: 1 to 10 Hz
			param depth = controls[7] * 0.001;  // range: 0 to 0.001 seconds (delay time in seconds)
			
			signal mod = delay_lfo(rate) * depth + depth;
			signal out_sig = delay(mod * fs);

			bool is_flanger = (delay_switch == 2);
			if (is_flanger){
				out_sig += in_sig;
			}
			
			return out_sig;
		}
		
		signal dis_fx(signal in_sig){ // TODO
			return in_sig;
		}
			
		void process() {
			signal osc_sig = (*osc)*osc_gain;
			signal env_sig = osc_sig * adsr;
			signal dis_out = dis_fx(env_sig); // TODO: distortion functions (softclipping, bitcrush)
			signal wah_out = wah_fx(dis_out);
			signal delay_out = delay_fx(wah_out);
			delay_out >> out;
			if(adsr.finished())
				stop();
		}
	};


	MySynth() {
		controls = {
	      {
	        "Base Sound",
	        Menu("", { 20, 40, 60, 20 }, "Organ", "Trumpet", "Xylophone") // controls[0]
	      },
	      {
	        "WahWah Fx",
	        Menu("Switch", { 110, 40, 40, 20 }, "Off", "On"), // controls[1]
	        Dial("Rate", 1, 10, 3, { 170, 40, 40, 40 }), // 1 to 10 Hz
	        Dial("Depth", 10, 1000, 100, { 220, 40, 40, 40 }), 
	        Dial("Centre", 10, 2000, 400, { 270, 40, 40, 40 }),
	      },
	      {
	        "Delay Fx",
	        Menu("Switch", { 110, 130, 40, 20 }, "Off", "Vibrato", "Flanger"), // controls[5]
	        Dial("Rate", 1, 10, 3, { 170, 130, 40, 40 }), // range: 1 to 10 Hz
	        Dial("Depth", 0.0, 1.0, 0.5, { 220, 130, 40, 40 }), // range: 0 to 0.001 seconds (delay time in seconds)
	      },
          { "AMP Envelope",    	
            Slider("A", 0, 1, 0.25, { 20, 130, 10, 40 } ), // time (0.0 to 1.0 sec) // controls[8]
            Slider("D", 0, 1, 0.25, { 35, 130, 10, 40 } ), // time (0.0 to 1.0 sec)
            Slider("S", 0, 1, 0.9, { 50, 130, 10, 40 } ), // amplitude (none to max dB)
            Slider("R", 0, 1, 0.5, { 65, 130, 10, 40 } ), // time (0.0 to 1.0 sec)
          }   
	    };
	    
//	    presets = {
//	    	{ "Sensitive Organ", { 0, 1, 1.000, 741.798, 1437.639, 2, 4.771, 0.500, 0.603, 0.581, 0.747, 0.647 } },
//          { "Copied Preset", { 0, 0, 3.000, 100.000, 400.000, 0, 4.189, 0.020, 0.000, 0.250, 0.900, 0.500 } },
//          { "Vanilla Organ", { 0, 0, 1.000, 10.000, 10.000, 0, 1.000, 0.000, 0.000, 0.000, 1.000, 0.000 } },
//	    };
		notes.add<SimpleNote>(32);
	}
};

```
