# Notes from Evelyn Yang

## PolySynth Explained
I've written up an explanation of the PolySynth class and some other related classes. It is just a summary of what I've learned from reading through the documentation and some 
of the code in Allolib. Find the explanation [here](https://github.com/allolib-s21/notes-yangevelyn/blob/main/PolySynth.md).

## GUI Tools
A collection of different tools and utilities that I've added to Allolib's GUI. The tools include:
- Quantization
- Tempo Adjustment
- Layering

When not in use, these tools should not affect existing features of Allolib.


### Quantization Tool
A feature built into Allolib's GUI that allows users the option to quantize their piece while playing it. When turned on, the resulting synthSequence file will be quantized according to the user's tempo and note specifications.  

#### To Use
Scroll to the bottom of the Allolib GUI to find the quantization checkbox. Select this checkbox to turn on the quantization feature. Also specify the tempo to quantize to, as well as the note type. Note type is calculated by the number of notes a bar will be divided into. In other words, 4 = quarter notes, 8 = eighth notes, and so on. This tool currently only quantizes to 4/4 time.  

#### Quantization Code
The quantization code can be found in the `allolib/src/scene/al_SynthRecorder.cpp` file. Essentially, a time grid is created based on the specified tempo and note type, and each note the user plays is moved to the closest point on the grid. The main functionality is provided by my `quantize` function, however small modifications were also made to the `startRecord` and `stopRecord` files in order to incorporate the quantization feature into the existing code.  

***

### Tempo Adjustment Tool
A simple tool that adjusts the tempo of an existing .synthSequence file. Simply check the "Adjust Speed" checkbox and enter the desired adjustment in tempo. Note that the desired adjustment should be inputted as a number (integer or float).

***

### Layering Tool
This tool allows users to play existing sequences on top of each other to create a new combined sequence with all of the voice events from the selected sequences layered on top of each other. In other words, the tool can be used to play .synthSequence files "live" on top of each other to create more complex pieces.

#### To Use
Navigate to the "Event Sequencer" section of Allolib's GUI. Notice that 10 dropdown lists have been added to the GUI. Next to each is a play button that, when clicked, will play the sequence specified in the associated dropdown. These 10 dropdowns allow quick access to 10 different files, although layering too many sequences at once is not recommended as it may lead to stream underflow errors. Below the 10 dropdowns is the default Allolib event sequencer sequence selector. This sequence can be thought of as a "base" on top of which the additional sequences are played.  

As usual, users can use the Event Recorder to record a new sequence that makes use of the layering tool.

***

### Notes on the GUI Code
The modifications to the GUI were done in the `allolib/src/ui/al_ParameterGUI.cpp` file, specifically in the `drawSynthRecorder` function. Allolib's GUI is designed using ImGui, so helpful info can be found [here](https://github.com/ocornut/imgui). Some specific things I found helpful are as follows:  

Checkboxes require an associated boolean, as such:  
``` cpp
// add quantize function
ImGui::Checkbox("Quantize##__EventRecorder", &state.quantizeButton);
```

Input text boxes require an associated char array (and the size of the char array)  
``` cpp
static char tempoBuf[16] = "120";
ImGui::InputText("Tempo##__EventRecorder", tempoBuf, 16);
```

Specify that the next element will be on the same line:  
``` cpp
ImGui::SameLine();
```

Change the style of elements by pushing to the stack, then popping to get rid of the most recently pushed styling.  
``` cpp
// make the following text boxes have a width of 32 pixels
ImGui::PushItemWidth(32);
ImGui::SameLine();
static char tempoBuf[16] = "120";
ImGui::InputText("Tempo##__EventRecorder", tempoBuf, 16);
ImGui::SameLine();
static char noteBuf[16] = "8";
ImGui::InputText("Note Type##__EventRecorder", noteBuf, 16);
// after this, text boxes will no longer be 32 pixels wide
ImGui::PopItemWidth();
```
