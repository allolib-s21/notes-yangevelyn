# PolySynth Explained (in Simple Terms)
*Note: This explanation is my own understanding of Allolib's PolySynth and very likely contains errors from a technical standpoint. However, it describes the PolySynth from a 
user's perspective, which may still be useful for anyone looking to get a better sense of how the PolySynth's different parts fit together.*

The [PolySynth](https://allosphere-research-group.github.io/allolib-doc/classal_1_1_poly_synth.html) is the Allolib class used to compose and play musical pieces that 
have multiple voices. It can combine voices dynamically in a "live" manner, or after voice sequences have already been recorded.

## What is a Voice?
In Allolib, a voice is like a single instrument. Different voices have different sounds, and you can play multiple voices at the same time. It is represented in the 
[SynthVoice](https://allosphere-research-group.github.io/allolib-doc/classal_1_1_synth_voice.html) class found on line 72 of `al_PolySynth.hpp`.  

### Triggering a Voice
Triggering a voice (triggerOn) is like playing a note on an instrument. More specifically, triggering a voice causes the voice to play a specified frequency at a specified time 
(e.g. now, in 10 seconds, etc.). Conversely, triggerOff turns off the voice.  

### Examples
See the files numbered 0-10 in `tutorials/synthesis` for examples of voices. In addition to defining a SynthVoice class, each file also defines an App class with a SynthGUIManager. 
A SynthGUIManager takes a VoiceType, sets up voices based on GUI input, and triggers them. When you run these files (i.e. `./run.sh tutorials/synthesis/08_SubSyn.cpp`), you can 
press different keys on the keyboard to play different notes. When you press a certain key, the SynthGUIManager sets its voice to a certain frequency and triggers the voice. When 
you release the key, the SynthGUIManager triggers the voice off. You can also use the GUI to adjust the voice's various parameters to adjust its sound.

## What is a Sequence?
A sequence is a series of voice events as scheduled by the [SynthSequencer](https://allosphere-research-group.github.io/allolib-doc/classal_1_1_synth_sequencer.html#a7fccff8bba4a39b31d5bfccd8f76907a)
class. Each event can be thought of as a note, with a specific frequency, time, duration, and voice.

### Creating a Sequence
The SynthSequencer can create sequences programmatically through its `add(startTime, duration)`, `addVoice(*voice, startTime, duration)`, and `addVoiceFromNow(*voice, startTime, duration)` 
functions. All three of these functions add an event to the current list of events, but differ in the amount of control they provide (i.e. choose voice, start time).

### .synthSequence Files
The SynthSequencer can also play pre-recorded sequence files, such as the ones produced by the [SynthRecorder](https://allosphere-research-group.github.io/allolib-doc/classal_1_1_synth_recorder.html). 
In the code, this is done by parsing through the file and adding events to the current list of events. 
Such files must end in ".synthSequence" and follow the format specified in the [SynthSequencer](https://allosphere-research-group.github.io/allolib-doc/classal_1_1_synth_sequencer.html#a7fccff8bba4a39b31d5bfccd8f76907a) 
documentation. Essentially, use the symbols '@', '+', '-', 't', and '=' at the start of a line to specify different commands. I found the wording of the documentation page to be 
a little confusing, so below is an explanation of the acceptable commands:  
 - **Add an event (like adding a note):** `@ [start time] [note duration] [voice name] [voice parameters]`
   - example: `@ 0.981379 0.116669 MyVoice 0 0 1 698.456 0.1 1`
 - **Turn on a voice (play indefinitely):** `+ [start time] [id] [voice name] [voice parameters]`
   - You can pick an id, but be careful that it does not overlap with an existing event
   - example: `+ 0.981379 25 MyVoice 0 0 1 698.456 0.1 1`
 - **Turn off a voice:** `- [end time] [id]`
   - example: `- 1.3 25`
 - **Set tempo:** `t [bpm]`
   - Only the last tempo statement in the file will have an effect
   - example: `t 120`
 - **Add another sequence (from a file):** `= [start time] [sequence name] [timeScale]`
   - example: `= 201.0000 "endEtherial.synthSequence" 1`

### Note on Sequences
I haven't been able to figure out a way to make sequences work as well as I would like in a "live" manner, as a sequence must be loaded by the SynthSequencer in order for it to 
play. The loading inevitably takes time, causing a pause in the sound output.
