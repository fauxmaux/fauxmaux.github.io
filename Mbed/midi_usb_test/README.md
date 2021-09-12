```
// for FRDM-KL-46Z, work on LPC1768
// https://os.mbed.com/users/samux/code/USBMIDI_HelloWorld//file/78af4df7012f/main.cpp/
// https://os.mbed.com/users/simon/code/USBMIDI_HelloWorld//file/4b55d56b6b61/main.cpp/
//https://os.mbed.com/handbook/USBMIDI
//https://os.mbed.com/cookbook/USBMIDI

//mbed-os-el nem kell a USBDevice konyvtar



#include "mbed.h"
#include "USBMIDI.h" //https://os.mbed.com/teams/ST/code/USBDEVICE/

USBMIDI midi;

int main() {             
    while (1) {    
        for(int i=48; i<83; i++) {     // send some messages!
            midi.write(MIDIMessage::NoteOn(i));
            wait(0.25);
            midi.write(MIDIMessage::NoteOff(i));
            wait(0.5);
        }
    }
}
```
