 ```
 //for NUCLEO-F103RB (or BLUEPILL)
 #include "mbed.h"
 #include "stdio.h"
 #include "string.h"
 #include "morsegenerator.h" //https://os.mbed.com/users/screamer/code/MorseGenerator//diff/79a42458a848/morsegenerator.h/
 
 ///DigitalOut myled(p18);
 //DigitalOut myled(LED1);
 DigitalOut myled(PB_1);
 
 void morse_callback(int val) {
     
      myled = val;
 //     key = val;
 }
  
 int main() {
 
 //float h, t; 
char buffer [50];
///int n, a=1, b=2;
//float readtemp = -48.3;
//int t = (readtemp + 274.15);
//sprintf (buffer, "%d", t);
///example: sprintf (buffer, "%4.0f",power);// datastream value

      MorseGenerator morse = MorseGenerator(morse_callback);
  
      while (1) {
          morse.transmit("T ");
//          morse.transmit(buffer);
          morse.transmit("V ");
      }
 }
```
