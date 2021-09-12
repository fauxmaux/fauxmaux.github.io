```
//muxik plc1768-on
//USB connector: p31 (D+), p32 (D-) and GND for the LPC1768 and the LPC11U24

#include "mbed.h"
#include "USBKeyboard.h" //https://os.mbed.com/handbook/USBKeyboard
 
//LED1: NUM_LOCK
//LED2: CAPS_LOCK
//LED3: SCROLL_LOCK
BusOut leds(LED1, LED2, LED3);
 
//USBKeyboard
USBKeyboard keyboard;
 
int main(void) {
    while (1) {
        //keyboard.mediaControl(KEY_VOLUME_DOWN);
        //keyboard.printf("Hello World from Mbed\r\n");
        keyboard.printf("s");
        //keyboard.keyCode('s', KEY_CTRL);
        //keyboard.keyCode('s');
        keyboard.keyCode(KEY_CAPS_LOCK);
        wait(1);
        leds = keyboard.lockStatus();
    }
}
```
