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

* search better base sounds samples
* add tremolo
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

// -------- included in synth --------

const HMap cathedral_organ = {
   // Principal stops (8', 4', 2')
    {130.81,   0.0},   // 8' (fundamental)
    {261.63,  -1.0},   // 4' (octave)
    {523.25,  -3.0},   // 2' (super-octave)
    
    // Mixture stops (harmonic series)
    {392.0,   -6.0},   // 12th (3f, Quint)
    {784.0,   -8.0},   // 15th (4f, Octave + Quint)
    {1046.5, -10.0},   // 17th (5f, Tierce)
    
    // Pipe resonances
    {900.0,  -12.0},   // Chiff (attack noise)
    {2500.0, -15.0},   // Air turbulence
    
    // Subharmonic (32' stop simulation)
    {65.41,  -10.0}    // 32' (sub-octave)
};

const HMap angelic_harmonica = {
    {523.25,   0.0},   // 1f
    {1570.0,  -2.0},   // ~3f (slightly detuned)
    {2617.0,  -5.0},   // ~5f
    {3664.0, -10.0},   // ~7f
    {4700.0, -20.0},   // ~9f
    {8000.0, -25.0},   // High-frequency shimmer
    {12000.0, -40.0},  // Airy noise tail
    {261.63, -15.0}    // 0.5f (sub-octave)

};
const HMap gritty_bass = {
 	// Core harmonics (saturated and slightly detuned)
    {65.41,    0.0},   // 1f
    {130.82,  -3.0},   // 2f
    {196.23,  -9.0},   // ~3f (drifted)
    {261.64, -15.0},   // 4f
    
    // Subharmonic (for "fatness")
    {32.7,    -6.0},   // 0.5f (sub-bass)
    
    // Analog imperfections
    {100.0,  -18.0},   // Power supply hum
    {450.0,  -12.0},   // Saturation "grunge"
    {2000.0, -30.0}    // Filtered noise (key click)
};
const HMap vintage_trumpet = {
    {466.16,   0.0},    // 1f (fundamental)
    {800.0,   -8.0},    // Mouthpiece "buzz" peak
    {932.32,  -1.5},    // 2f (strong, near-ideal harmonic)
    {1398.5,  -3.0},    // ~3f (slightly sharp)
    {1500.0, -10.0},    // Mid-range "brassiness"
    {1864.6,  -6.0},    // ~4f 
    {2330.8,  -9.0},    // ~5f 
    {2797.0, -12.0},    // ~6f 
    {2800.0, -20.0},    // High-frequency "air"
    {3263.2, -18.0},    // ~7f (rapid high-end rolloff)
    {4000.0, -30.0},    // Key click/air turbulence
    {6000.0, -40.0}     // High-frequency noise tail
};
const HMap forest_flute = {
    // Core harmonics (weak even harmonics)
    {392.0,    0.0},   // 1f
    {784.0,   -12.0},  // 2f
    {1176.0,  -8.0},   // 3f (stronger)
    {1568.0, -20.0},   // 4f
    
    // Breath noise and overtones
    {2000.0, -15.0},   // Air turbulence
    {3000.0, -25.0},   // Whistle tone
    {5000.0, -40.0},   // Wind noise
    
    // Non-integer resonances
    {610.0,  -10.0},   // Finger hole resonance
    {950.0,  -14.0}    // Bamboo body resonance
};
const HMap retro_guitar = {
    {261.63,  0.0},    // 1f (fundamental)
    {273.0,  -22.0},   // Body resonance (near 1f)
    {522.5,  -4.0},    // ~2f (detuned +0.25 Hz)
    {650.0,  -16.0},   // Mid-range resonance
    {783.0,  -8.0},    // ~3f (detuned -1.88 Hz)
    {1045.0, -12.0},   // ~4f (detuned -1.5 Hz)
    {1306.0, -18.0},   // ~5f (detuned -2.1 Hz)
    {1200.0, -30.0},   // High-frequency "pluck" noise
    {1567.0, -24.0},   // ~6f (detuned -2.8 Hz)
};
const HMap simple_sine = { 
	{1.0, 0.0} 
};
const HMap simple_saw = {    
	{261.63,  0.0}, 
    {523.25, -6.0},
    {784.88, -9.5}, 
    {1046.5, -12.0},  
    {1308.1, -14.0}
};

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

const HMap m1_korg = {
	{65, -14.5}, {131, -14.2}, {196, -14.3}, {263, -50.0}, {327, -47.2}, {393, -24.5}
};

// -------- experiments --------

const HMap misc = {
   // Principal stops (8', 4', 2')
    {130.81,   0.0},   // 8' (fundamental)
    {261.63,  -1.0},   // 4' (octave)
    {523.25,  -3.0},   // 2' (super-octave)
    
    // Mixture stops (harmonic series)
    {392.0,   -6.0},   // 12th (3f, Quint)
    {784.0,   -8.0},   // 15th (4f, Octave + Quint)
    {1046.5, -10.0},   // 17th (5f, Tierce)
    
    // Pipe resonances
    {900.0,  -12.0},   // Chiff (attack noise)
    {2500.0, -15.0},   // Air turbulence
    
    // Subharmonic (32' stop simulation)
    {65.41,  -10.0}    // 32' (sub-octave)
};


const HMap my_trumpet = {
    {268, -23.5}, {528, -12.5}, {788, -10.4}, {1048, -6.1}, {1309, -3.4}, 
	{1570, -8.2}, {1829, -12.9}, {2090, -22.5}, {2349, -30.6}, {26.11, -35.1}
};

// -------- i also deepseek to produce some sounds --------
// C4 ≈ 261.63 Hz

const HMap piano_ds = {
    {261.63,  0.0},   // 1f (fundamental)
    {523.25, -3.1},   // 2f
    {784.88, -6.0},   // 3f
    {1046.5, -10.5},  // 4f
    {1308.1, -14.0},  // 5f
    {1569.8, -20.0},  // 6f
    {1831.4, -30.0}   // 7f (higher harmonics decay sharply)
};

// (Strong fundamental, moderate harmonics)
const HMap guitar_ds = {
    {261.63,  0.0},   // 1f
    {523.25, -4.4},   // 2f
    {784.88, -8.0},   // 3f
    {1046.5, -14.0},  // 4f
    {1308.1, -20.0},  // 5f
    {1569.8, -26.0}   // 6f
};

// (Near-sinusoidal, weak harmonics)
const HMap flute_ds = {
    {261.63,  0.0},   // 1f
    {523.25, -14.0},  // 2f
    {784.88, -26.0}   // 3f (higher harmonics negligible)
};

// (Bright, strong harmonics)
const HMap trumpet_ds = {
    {261.63,  0.0},   // 1f
    {523.25, -1.9},   // 2f
    {784.88, -3.1},   // 3f
    {1046.5, -6.0},   // 4f
    {1308.1, -10.5},  // 5f
    {1569.8, -14.0}   // 6f
};

const HMap guitar_improved_ds = {
    // Fundamental + harmonics (slightly detuned for realism)
    {261.63,  0.0},    // 1f (fundamental)
    {522.5,  -4.0},    // ~2f (detuned +0.25 Hz)
    {783.0,  -8.0},    // ~3f (detuned -1.88 Hz)
    {1045.0, -12.0},   // ~4f (detuned -1.5 Hz)
    {1306.0, -18.0},   // ~5f (detuned -2.1 Hz)
    {1567.0, -24.0},   // ~6f (detuned -2.8 Hz)
    
    // String resonance peaks (not strictly harmonic)
    {273.0,  -22.0},   // Body resonance (near 1f)
    {350.0,  -20.0},   // Neck/body interaction
    {650.0,  -16.0},   // Mid-range resonance
    {1200.0, -30.0},   // High-frequency "pluck" noise
};

const HMap trumpet_improved_ds = {
    // Core harmonics (slightly detuned and shaped like a real trumpet)
    {466.16,   0.0},    // 1f (fundamental)
    {932.32,  -1.5},    // 2f (strong, near-ideal harmonic)
    {1398.5,  -3.0},    // ~3f (slightly sharp)
    {1864.6,  -6.0},    // ~4f 
    {2330.8,  -9.0},    // ~5f 
    {2797.0, -12.0},    // ~6f 
    {3263.2, -18.0},    // ~7f (rapid high-end rolloff)

    // Formants and resonances (from mouthpiece/body)
    {800.0,   -8.0},    // Mouthpiece "buzz" peak
    {1500.0, -10.0},    // Mid-range "brassiness"
    {2800.0, -20.0},    // High-frequency "air"

    // Breath noise (modeled as non-harmonic content)
    {4000.0, -30.0},    // Key click/air turbulence
    {6000.0, -40.0}     // High-frequency noise tail
};

// (Rich harmonics, nasal tone)
const HMap violin_ds = {
    {261.63,  0.0},   // 1f
    {523.25, -1.9},   // 2f
    {784.88, -4.4},   // 3f
    {1046.5, -8.0},   // 4f
    {1308.1, -10.5},  // 5f
    {1569.8, -14.0}   // 6f
};

// (Odd harmonics only)
const HMap clarinet_ds = {
    {261.63,  0.0},   // 1f
    {784.88, -3.1},   // 3f (odd)
    {1308.1, -6.0},   // 5f
    {1831.4, -14.0}   // 7f
    // Even harmonics omitted (~-inf dB)
};


// (1/n amplitude rolloff → ~6 dB/octave)
const HMap sawtooth_ds = {
    {261.63,  0.0},   // 1f (0 dB)
    {523.25, -6.0},   // 2f (-6 dB)
    {784.88, -9.5},   // 3f (-9.5 dB)
    {1046.5, -12.0},  // 4f (-12 dB)
    {1308.1, -14.0},  // 5f (-14 dB)
    {1569.8, -15.6}   // 6f (-15.6 dB)
};

//  "Cathedral Pipe Organ"
// (Pure, expansive, with 8’ + 4’ + 2’ stops and harmonic flute pipes.)
const HMap cathedral_organ_ds = {
    // Principal stops (8', 4', 2')
    {130.81,   0.0},   // 8' (fundamental)
    {261.63,  -1.0},   // 4' (octave)
    {523.25,  -3.0},   // 2' (super-octave)
    
    // Mixture stops (harmonic series)
    {392.0,   -6.0},   // 12th (3f, Quint)
    {784.0,   -8.0},   // 15th (4f, Octave + Quint)
    {1046.5, -10.0},   // 17th (5f, Tierce)
    
    // Pipe resonances
    {900.0,  -12.0},   // Chiff (attack noise)
    {2500.0, -15.0},   // Air turbulence
    
    // Subharmonic (32' stop simulation)
    {65.41,  -10.0}    // 32' (sub-octave)
};

// (Ethereal, metallic, and haunting with high-frequency shimmer.)
const HMap glass_harmonica_ds = {
    // Odd harmonics dominate (metallic bell-like sound)
    {523.25,   0.0},   // 1f
    {1570.0,  -2.0},   // ~3f (slightly detuned)
    {2617.0,  -5.0},   // ~5f
    {3664.0, -10.0},   // ~7f
    {4700.0, -20.0},   // ~9f
    
    // Non-harmonic "glass friction" peaks
    {8000.0, -25.0},   // High-frequency shimmer
    {12000.0, -40.0},  // Airy noise tail
    
    // Subharmonic (for "weight")
    {261.63, -15.0}    // 0.5f (sub-octave)
};

// (Think Moog-style bass with oscillator drift and saturation.)
const HMap analog_bass_ds = {
    // Core harmonics (saturated and slightly detuned)
    {65.41,    0.0},   // 1f
    {130.82,  -3.0},   // 2f
    {196.23,  -9.0},   // ~3f (drifted)
    {261.64, -15.0},   // 4f
    
    // Subharmonic (for "fatness")
    {32.7,    -6.0},   // 0.5f (sub-bass)
    
    // Analog imperfections
    {100.0,  -18.0},   // Power supply hum
    {450.0,  -12.0},   // Saturation "grunge"
    {2000.0, -30.0}    // Filtered noise (key click)
};

// (Lo-fi, wobbly, and nostalgic with pitch instability.)
const HMap vinyl_record_ds = {
    // Fundamental + harmonics (warped)
    {440.0,    0.0},   // 1f
    {880.0,   -8.0},   // 2f
    {1320.0, -16.0},   // 3f
    
    // Wow/flutter effects (pitch modulation)
    {441.5,   -5.0},   // 1f + slight drift
    {882.5,  -10.0},   // 2f + drift
    
    // Vinyl noise artifacts
    {7000.0, -20.0},   // Crackle
    {12000.0, -35.0},  // Hiss
    {50.0,   -15.0}    // Rumble
};

// (Organic, breathy, and dynamically alive with natural randomness.)
const HMap forest_flute_ds = {
    // Core harmonics (weak even harmonics)
    {392.0,    0.0},   // 1f
    {784.0,   -12.0},  // 2f
    {1176.0,  -8.0},   // 3f (stronger)
    {1568.0, -20.0},   // 4f
    
    // Breath noise and overtones
    {2000.0, -15.0},   // Air turbulence
    {3000.0, -25.0},   // Whistle tone
    {5000.0, -40.0},   // Wind noise
    
    // Non-integer resonances
    {610.0,  -10.0},   // Finger hole resonance
    {950.0,  -14.0}    // Bamboo body resonance
};

// "Vintage Jazz Muted Trumpet": (Dark, velvety, and slightly distorted with a Harmon mute effect.)
const HMap jazz_muted_trumpet_ds = {
    // Fundamental + muted harmonics (strong midrange)
    {466.16,   0.0},   // 1f
    {932.32,  -8.0},   // 2f (suppressed by mute)
    {1398.5,  -4.0},   // 3f (boosted "nasal" peak)
    {1864.6, -12.0},   // 4f
    {2330.8, -20.0},   // 5f

    // Mute-induced resonances
    {1100.0,  -6.0},   // Metallic "buzz" of Harmon mute
    {2800.0, -25.0},   // High-end staccato noise

    // Valve click and breath
    {6000.0, -30.0},   // Key noise
    {80.0,   -18.0}    // Puffing breath
}; 

// "Brass Fanfare Herald" (Bright, heroic, with extra "blare" and harmonic shimmer.)
const HMap fanfare_trumpet_ds = {
    // Strong harmonics for "brightness"
    {523.25,   0.0},   // 1f
    {1046.5,  -1.0},   // 2f (near-unison)
    {1569.8,  -2.0},   // 3f
    {2093.0,  -5.0},   // 4f
    {2616.2,  -8.0},   // 5f

    // Formant peaks (chest resonance)
    {800.0,   -4.0},   // "Blare" region
    {2500.0,  -6.0},   // "Shimmer" peak

    // Overblow effect (fortissimo)
    {3700.0, -12.0},   // Extra edge
    {5000.0, -25.0}    // Air noise
};

// dB Reference: 0.0 dB = full amplitude of the fundamental; negative values are quieter.

// Tips for Additive Synthesis:
// Detuning Harmonics: Real instruments have slight inharmonicity—try adding small random detuning (±1-5%).
// Dynamic Envelopes: Harmonics don’t all decay at the same rate (e.g., high harmonics fade faster in pianos).
// Formants: Some instruments (like brass) have resonant peaks that don’t follow exact harmonic ratios.
// Experiment!: Adjust amplitudes to match the brightness/darkness you want.

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

namespace dsg { // distortions group

signal bitcrush(signal x, param c){ 
    int levels = int(c); // quantization levels (if really big, it's indistinguishable from original signal) 
    param level_inc = 1.0 / (2*levels); // increment per level 
    // [0, level_inc], [1*level_inc, 3*level_inc], [3*level_inc, 5*level_inc], ..., [1-3*level_inc, 1-level_inc] [1-level_inc, 1]
    // essentially rounding each x to the closest level
    int group = 0;
    if (x > 0){
    	group = (int(x / level_inc) + 1) / 2;
    }
    else {
    	group = (int(x / level_inc) - 1) / 2;
    }
    return group * 2 * level_inc;
}

signal softclip(signal x, param c){
    if (x >= 0){
        return (1+c) * x / (1 + c*x);
    }
    return (1+c) * x / (1 - c*x);
}

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

#define osc_amount 8
// cathedral_organ, gritty_bass, retro_guitar, angelic_harmonica, vintage_trumpet, forest_flute, simple_saw, simple_sine;


struct MySynth : Synth {
	struct SimpleNote : public Note {
		// the amount of oscillators is 8
		MyModularOscillator oscs[8];
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
		
		// ---- distortion fx ----
		Sine dis_lfo;
		
		

		SimpleNote()
		: oscs{
			// should match osc_amount
			MyModularOscillator{hsg::cathedral_organ}, 
			MyModularOscillator{hsg::gritty_bass}, 
			MyModularOscillator{hsg::retro_guitar}, 
			MyModularOscillator{hsg::angelic_harmonica}, 
			MyModularOscillator{hsg::vintage_trumpet}, 
			MyModularOscillator{hsg::forest_flute}, 
			MyModularOscillator{hsg::simple_saw}, 
			MyModularOscillator{hsg::simple_sine}, 
		  }
		{
			osc = nullptr;
		}

		event on(Pitch pitch, Amplitude velocity) {
			param frequency = pitch -> Frequency;
			param type = controls[0];
			int osc_num = min(type, osc_amount-1);
			osc = &oscs[osc_num]; // check for amount of modulators defined
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
			
			wah_depth = min(wah_depth, wah_centre-10); // to prevent usage over limits
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
		
		signal dis_fx(signal in_sig){
			param dis_switch = controls[12];
			bool is_dis_off = (dis_switch == 0);
			if (is_dis_off){
				return in_sig;
			}
			
			param rate = controls[13]; // range: 1 to 10 Hz
			param depth = controls[14]; // 0 to 1
			
			if (dis_switch == 1) { // Tremolo
				signal mod = dis_lfo(rate) * depth + (1-depth); // lfo osc between [1-2*depth, 1] ( [0, 1] for depth = 0.5, [1,1] for depth = 1 )
				return in_sig * mod;
			}
			else if (dis_switch == 2) { // Softclip
				signal mod = depth * 10; // range: 0 to 10
				return dsg::softclip(in_sig, mod);
			}
			else {	// Bitcrush (dis_switch == 3)
				param min_level = 5.0;
				param max_level = min_level + 50.0 * (1-depth);
				signal mod = (dis_lfo(rate) + 1) / 2 * max_level + min_level; // range: 5 to 25 levels
				return dsg::bitcrush(in_sig, mod);
			}
		}
			
		void process() {
			signal osc_sig = (*osc)*osc_gain;
			signal env_sig = osc_sig * adsr;
			signal dis_out = dis_fx(env_sig);
			signal wah_out = wah_fx(dis_out);
			signal out_sig = delay_fx(wah_out);
			
			param out_gain =  controls[15];
			out_sig * out_gain >> out;
			if(adsr.finished())
				stop();
		}
	};


	MySynth() {
		controls = {
	      {
	        "Base Sound",
	        // cathedral_organ, gritty_bass, retro_guitar, angelic_harmonica, vintage_trumpet, forest_flute, simple_saw, simple_sine;
	        Menu("", { 20, 40, 80, 20 }, "Cathedral Organ", "Gritty Bass", "Retro Guitar", "Angelic Harmonica", "Vintage Trumpet", "Forest Flute", "Simple Saw", "Simple Sine") // controls[0]
	      },
	      {
	        "Filter Fx",
	        Menu("Switch", { 120, 40, 70, 20 }, "Off", "Wah Wah"), // controls[1]
	        Dial("Rate", 1, 10, 5.5, { 200, 40, 40, 40 }), // 1 to 10 Hz
	        Dial("Depth", 10, 1000, 500, { 260, 40, 40, 40 }), 
	        Dial("Centre", 10, 2000, 1000, { 320, 40, 40, 40 }),
	      },
	      {
	        "Delay Fx",
	        Menu("Switch", { 120, 120, 70, 20 }, "Off", "Vibrato", "Flanger"), // controls[5]
	        Dial("Rate", 1, 10, 5.5, { 200, 120, 40, 40 }), // range: 1 to 10 Hz
	        Dial("Depth", 0.0, 1.0, 0.5, { 260, 120, 40, 40 }), // range: 0 to 0.001 seconds (delay time in seconds)
	      },
          { "AMP Envelope",    	
            Slider("A", 0, 1, 0.25, { 20, 130, 15, 40 } ), // time (0.0 to 1.0 sec) // controls[8]
            Slider("D", 0, 1, 0.25, { 41, 130, 15, 40 } ), // time (0.0 to 1.0 sec)
            Slider("S", 0, 1, 0.9, { 63, 130, 15, 40 } ), // amplitude (none to max dB)
            Slider("R", 0, 1, 0.5, { 84, 130, 15, 40 } ), // time (0.0 to 1.0 sec)
          }, 
          {	
	        "Amp Fx",
	        Menu("Switch", { 120, 200, 70, 20 }, "Off", "Tremolo", "Softclip", "Bitcrush"), // controls[12]
	        Dial("Rate", 1.0, 10, 5.5, { 200, 200, 40, 40 }), // range: 1 to 10 Hz
	        Dial("Depth", 0.0, 1.0, 0.5, { 260, 200, 40, 40 }), // range: 0 to 0.001 seconds (delay time in seconds)
          },
          {
          	"Master",
          	Dial("Gain", 0.0, 1.0, 1.0, { 320, 200, 40, 40 }), // controls[15]
          }
	    };
	    
	    presets = {
	    	{ "Diabolic Base (Harmonica)", { 3, 1, 5.500, 746.410, 829.998, 0, 1.000, 0.462, 0.043, 0.092, 0.882, 0.508, 2, 1.472, 0.220, 1.000 } },
	    	{ "Bad Bass", { 1, 1, 6.735, 1000.000, 2000.000, 0, 1.000, 0.890, 0.072, 0.066, 1.000, 0.559, 2, 10.000, 1.000, 1.000 } },
	    	{ "Vanilla Saw", { 6, 0, 1.000, 10.000, 10.000, 0, 1.000, 0.000, 0.035, 0.068, 1.000, 0.000, 2, 1.000, 0.000, 1.000 } },
	    	{ "Breezy Flute", { 5, 0, 5.500, 500.000, 1000.000, 1, 8.096, 0.188, 0.500, 0.497, 0.836, 0.364, 1, 6.797, 0.213, 1.000 } },
	    	{ "Heavy Guitar", { 2, 0, 5.500, 500.000, 1000.000, 2, 8.096, 0.351, 0.072, 0.066, 1.000, 0.559, 0, 6.797, 0.213, 1.000 } },
	    	{ "Alien Organ", { 0, 0, 6.735, 1000.000, 2000.000, 2, 10.000, 0.890, 0.799, 0.765, 1.000, 0.559, 3, 1.000, 1.000, 1.000 } },
	    };
		notes.add<SimpleNote>(32);
	}
};

```


```
//	    presets = {
//	    	{ "Sensitive Organ", { 0, 1, 1.000, 741.798, 1437.639, 2, 4.771, 0.500, 0.603, 0.581, 0.747, 0.647 } },
//          { "Copied Preset", { 0, 0, 3.000, 100.000, 400.000, 0, 4.189, 0.020, 0.000, 0.250, 0.900, 0.500 } },
//          { "Vanilla Organ", { 0, 0, 1.000, 10.000, 10.000, 0, 1.000, 0.000, 0.000, 0.000, 1.000, 0.000 } },
            // { "Warm Xylophone", { 2, 0, 1.000, 10.000, 10.000, 1, 4.0, 0.40, 0.21, 0.42, 0.80, 0.58 } },

            // { "Bitcrush Vibrato", { 0, 0, 1.000, 10.000, 10.000, 1, 1.000, 0.191, 0.406, 0.504, 0.900, 0.500, 3, 8.45, 0.900 } },

            // {{ "Korg Base", { 1, 1, 5.029, 427.933, 1784.272, 1, 4.795, 0.895, 0.000, 0.000, 1.000, 0.000, 2, 5.176, 0.726 } },}




    //	    };

		notes.add<SimpleNote>(32);
	}
};

```
