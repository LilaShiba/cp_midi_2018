# DJ Circuit Playground

We will be following this: [link](https://learn.adafruit.com/circuit-playground-pizza-box-dj-controller/pz-1-pizza-box-dj-code)

FIrst things first, we have to install additional libraries. This is so that the CP can learn some new commands. Like how to be a MIDI

#### Quick Setup Overview
Boards to download or update:
- Ardunio AVR
- Adafruit AVR
- MIDIUSB
- TeeOnArdu

Board Link: 
https://adafruit.github.io/arduino-board-index/package_adafruit_index.json


#### Long Setup Overview: Add Additional Boards
Go to Arduino >> Preferences and add the link below to the 'Additional Boards Manager URL'

https://adafruit.github.io/arduino-board-index/package_adafruit_index.json

![link](https://cdn-learn.adafruit.com/assets/assets/000/033/278/medium800/flora_urls.png?1466733189)

Nice! Now, search for Adafruit TeeOnArdu. Click on the board entry and then click the Install button.

#### How to Use It

- In the Arduino IDE, select the new entry under Tools > Board > Circuit Playground (TeensyCore)
- Then, select Tools > USB Type > MIDI
- To upload new sketches you just have to double-press the reset button on the Circuit Playground each time you hit the upload button 
- If you decide to use your Circuit Playground as a regular Circuit Playground again, just select it under Tools > Board >Circuit Playground and upload an Arduino sketch like Blink File > Examples > Basics > Digital > Blink. The first time you have to double-press the reset button, but after that your Circuit Playground behaves like out of the box.

Look up notes: [midi note numbers](http://www.inspiredacoustics.com/en/MIDI_note_numbers_and_center_frequencies)

#### Program Process
- create threshold value. How hard do you have to touch the sensor for it to trigger?
```c
const int CAPMIN = 25; // it is 25 to cancel noise
```

- create your cap variables
```c
int cap1 = CircuitPlayground.readCap(1);
```
- use variable in conditional
```c
 if (cap1 > CAPMIN){
      magic code here
 }
```
- use MIDI methods to play notes: usb.MIDI.sendNoteOn(note,velocity,channel)
Velocity acts like pressing a key. How hard did you hit it, how fast, etc.
```c
 if (cap1 > CAPMIN){
     usbMIDI.sendNoteOn(42,127,0);
     delay(100);
     usbMIDI.sendNoteOff(61,0,0);
 }
```
- Customize code. Does a light pattern happen for a certain note?

#### Example Code

```c
#include <Adafruit_CircuitPlayground.h>

void setup() {
  CircuitPlayground.begin();
}

void loop() {
  const int CAPMIN = 25; // it is 25 to cancel noise
  int cap1 = CircuitPlayground.readCap(1);
  
  if (CircuitPlayground.leftButton()) {
    CircuitPlayground.redLED(HIGH);
                    //(note,velocity,channel)
    usbMIDI.sendNoteOn(60, 127, 0);  // 60 = C4 velocity 127 (max)
    delay(100);
    usbMIDI.sendNoteOff(60, 0, 0);
    CircuitPlayground.redLED(LOW);
  }
  if (CircuitPlayground.rightButton()) {
    CircuitPlayground.redLED(HIGH);
    usbMIDI.sendNoteOn(61, 127, 0);
    delay(100);
    usbMIDI.sendNoteOff(61, 0, 0);
    CircuitPlayground.redLED(LOW); 
  }
  
  if (cap1 > CAPMIN){
     usbMIDI.sendNoteOn(42,127,0);
     delay(100);
     usbMIDI.sendNoteOff(61,0,0);
  }

  // dont listen to any incoming MIDI!
  while (usbMIDI.read()) {
  }
}

```

