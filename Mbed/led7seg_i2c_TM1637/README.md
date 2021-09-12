```
/* mbed TM1637 Test program, for TM1637 LED controller
 * Copyright (c) 2016, v01: WH, Initial version
 *               2017, v02: WH, Added RobotDyn 6 Digit module,
 *                          Added Eyewink 6 Digit + 5 Keys module,
 *                          Constructor adapted to 2 pins: dio, clk    
 *
 * Permission is hereby granted, free of charge, to any person obtaining a copy
 * of this software and associated documentation files (the "Software"), to deal
 * in the Software without restriction, including without limitation the rights
 * to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 * copies of the Software, and to permit persons to whom the Software is
 * furnished to do so, subject to the following conditions:
 *
 * The above copyright notice and this permission notice shall be included in
 * all copies or substantial portions of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 * OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
 * THE SOFTWARE.
 */
 
 //muxik - 20190627
 //https://os.mbed.com/components/TM1637-LED-controller-48-LEDs-max-Keyboa/
 //https://os.mbed.com/users/wim/code/mbed_TM1637/
 //lasd: https://os.mbed.com/components/TM1637-LED-controller-48-LEDs-max-Keyboa/
 //fontos: 3.3V-al muxik, 5-el nem
 //lasd a TM1637_Config.h beallitasokat
 //hasznalat eseten ki kell vagni a felesleges sorokat
 
#include "mbed.h"
#include "TM1637.h" //https://os.mbed.com/users/wim/code/TM1637//file/f63d87466f55/Font_7Seg.h/

#if (CATALEX_TEST == 1)
// CATALEX TM1637 4 Digit display test
#include "Font_7Seg.h"

Serial pc(USBTX, USBRX);
DigitalOut myled(LED1);  //NOTE: On F401 LED1 is Pin D13, which is SCK!

// DisplayData_t size is 4 bytes (4 Grids @ 8 Segments) 
TM1637::DisplayData_t all_str   = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF};  
TM1637::DisplayData_t cls_str   = {0x00, 0x00, 0x00, 0x00, 0x00, 0x00};  
TM1637::DisplayData_t hello_str = {C7_H, C7_I, 0x00, 0x00, 0x00, 0x00};
TM1637::DisplayData_t bye_str   = {C7_B, C7_Y, C7_E, 0x00, 0x00, 0x00};

// KeyData_t size is 1 bytes  
TM1637::KeyData_t keydata; 

//TM1637_CATALEX declaration
#if(SPI==1)
//Old Constructor
TM1637_CATALEX CATALEX(p5, p6, p7);   //LPC1768
//TM1637_CATALEX CATALEX(D8, D9, D10);  //F401
#else
//New Constructor
TM1637_CATALEX CATALEX(p6, p7);       //LPC1768
//tul keppen p6=miso=dio, p7=sck=clk, tehat p6=dio p7=clk, nem a p9-p10-hez kotni
//TM1637_CATALEX CATALEX(D9, D10);      //F401
#endif

void show_menu() {
//    pc.printf("0:   Exit\n\r");
    pc.printf("1:   All\n\r");    
    pc.printf("2:   Show all segs\r\n");
    pc.printf("3:   Show all chars\n\r");
    pc.printf("4:   Show all digits\n\r");
    pc.printf("5:   Show all icons\n\r");
    pc.printf("6:   Counter\n\r");
    pc.printf("7:   Floats\n\r");
    pc.printf("8:   Kitt\n\r");
    pc.printf("9:   Cls\n\r");    
    pc.printf("A:   Bye\n\r");    
    pc.printf("k:   Key\n\r");
}


char cmd, bits;
int main() {
    
    pc.printf("Hello World\r\n"); //    
    
    CATALEX.cls(); 
    CATALEX.writeData(all_str);
    wait(2);
    CATALEX.setBrightness(TM1637_BRT3);    
    wait(1);
    CATALEX.setBrightness(TM1637_BRT0);        
    wait(1);
    CATALEX.setBrightness(TM1637_BRT4);            

    wait(1);
    CATALEX.cls(true); 
    CATALEX.writeData(hello_str); 
    
    char cmd2 = '0';
    while (1) {
      
      show_menu();
      cmd2 = pc.getc();

      switch (cmd2) {
        case '1' : {
                      pc.printf("all\r\n");
                      CATALEX.cls(); 
                      CATALEX.writeData(all_str);
                      break;
        }

        case '2' : {
#if(1)
//test to show all segs
          pc.printf("Show all segs\r\n");
          wait(1);          
          CATALEX.cls(); 
         
          for (int i=0; i<TM1637_DISPLAY_MEM; i++) {            
            for (int bit=0; bit<8; bit++) {
              CATALEX.cls(); 

              bits = 0x01 << bit;              
              CATALEX.writeData(bits, i);

              pc.printf("Idx = %d, Bits = 0x%02x\r\n", i, bits);              
//              wait(0.5);
              cmd = pc.getc(); // wait for key
            }  
          }
          pc.printf("\r\nShow all segs done\r\n");                    
#endif 
          break;          
        }

        case '3' : {

#if(1)
//test to show all chars
          pc.printf("Show all alpha chars\r\n");
          wait(1);          
          CATALEX.cls(); 
         
          for (int i=0; i<26; i++) {
            CATALEX.printf("%c", char(i + 'A'));
//            CATALEX.printf("%c", char(i + 'a'));            
            wait(0.25);                      
          }
          pc.printf("Show all alpha chars done\r\n");                    
#endif            

#if(0)
//test to show all chars
          pc.printf("Show all chars\r\n");
          wait(1);          
          CATALEX.cls(); 

          for (int i=FONT_7S_START; i<FONT_7S_END; i++) {
            CATALEX.printf("%c", char(i));
//            wait(0.25);
            cmd = pc.getc(); // wait for key            
          }
          pc.printf("Show all chars done\r\n");                    
#endif            
          break;          
        }
        case '4': {
#if(0)
//test to show all digits (base is 10)
          pc.printf("Show all digits\r\n");
          wait(1);          
          CATALEX.cls(); 

          for (int i=0; i<CATALEX_NR_DIGITS; i++) {

            for (int cnt=0; cnt<10; cnt++) {
              CATALEX.locate(i);
              CATALEX.printf("%0d", cnt);

//              wait(0.5);
              cmd = pc.getc(); // wait for key
            }  
          }
          pc.printf("\r\nShow all digits done\r\n");                    
#endif                

#if(1)
//test to show all digits (base is 0x10)
          pc.printf("Show all hex digits\r\n");
          wait(1);          
          CATALEX.cls(); 

          CATALEX.printf("%04x", 0x01AB);
          cmd = pc.getc(); // wait for key                                     
          CATALEX.printf("%04x", 0x0);
                    
          for (int i=0; i<CATALEX_NR_DIGITS; i++) {

            for (int cnt=0; cnt<0x10; cnt++) {
              CATALEX.locate(i);
              CATALEX.printf("%0x", cnt);

//              wait(0.5);
              cmd = pc.getc(); // wait for key
            }  
          }
          pc.printf("\r\nShow all hex digits done\r\n");          
#endif                
          break;          
        }
        case '5':  { 
#if(1)
//test to show all icons
          pc.printf("Show all icons\r\n");
          CATALEX.cls(true); // Also clear all Icons
          
          float delay=0.1;
          // Icons on, Note that some versions do not have decimal points, but a
          // a double colon on the second digit to show hours:min
          CATALEX.setIcon(TM1637_CATALEX::DP1); wait(delay);
          CATALEX.setIcon(TM1637_CATALEX::DP2); wait(delay);
//          CATALEX.setIcon(TM1637_CATALEX::COL2); wait(delay);
          CATALEX.setIcon(TM1637_CATALEX::DP3); wait(delay);
          CATALEX.setIcon(TM1637_CATALEX::DP4); wait(delay);

          wait(delay);
 
          // Icons off
          CATALEX.clrIcon(TM1637_CATALEX::DP4); wait(delay);
          CATALEX.clrIcon(TM1637_CATALEX::DP3); wait(delay);
          CATALEX.clrIcon(TM1637_CATALEX::DP2); wait(delay);
//          CATALEX.clrIcon(TM1637_CATALEX::COL2); wait(delay);
          CATALEX.clrIcon(TM1637_CATALEX::DP1); wait(delay);

//          wait(1);
//          CATALEX.cls(); // clear all, preserve Icons
          pc.printf("Show all icons done\r\n");                    
#endif           
          break;
        }                          

        case '6': {
          CATALEX.cls(); // clear all, preserve Icons

#if(1)
          CATALEX.locate(0);
         
          for (int cnt=0; cnt<=0xFF; cnt++) {
            CATALEX.locate(2);
            CATALEX.printf("%02x", cnt);           
            wait(0.2);
          }  
#endif

//          CATALEX.writeData(hello_str);
//          CATALEX.printf("hello");                  

          break;          
        }
        case '7': {
          CATALEX.cls(); // clear all, preserve Icons
          CATALEX.printf("%1.2f", -0.12);  // test decimal point display                    
          wait(0.5);
          CATALEX.cls(); // clear all, preserve Icons          
          CATALEX.printf("%1.3f", 1.234);  // test decimal point display
          break;          
        }

        case '8': {

#if(1)
//test to show KITT
          pc.printf("Show KITT scanner\r\n");

          
//                          0123
const char KITT[][5]   = {
                          {"8   "},
                          {"38  "},
                          {" 38 "},
                          {"  38"},
                          {"   3"},
                          {"    "},
                          {"   8"},
                          {"  8E"},
                          {" 8E "},
                          {"8E  "},
                          {"E   "},
                          {"    "},
                        };     
         
          CATALEX.cls(); // clear all, preserve Icons
          
          while (!pc.readable()) { // wait for key            
            for (int i=0; i < (sizeof(KITT) / 5) ; i++) {
              CATALEX.locate(0);
              CATALEX.printf("%s", KITT[i]);
              wait(0.05);
            }
          }
          cmd = pc.getc(); // read key           
          pc.printf("Show KITT done\r\n");
#endif
          break;
        }  

        case '9': {
//          CATALEX.cls(); // clear all, preserve Icons
          CATALEX.cls(true); // clear all, including Icons                
          break;
        }  

        case 'A': {

          CATALEX.cls(); // clear all, preserve Icons
//          CATALEX.writeData(bye_str);
          CATALEX.printf("Bye");          
       
          break;
        }  

        case 'k': {
          if (CATALEX.getKeys(&keydata)) {
            pc.printf("Keydata = 0x%02x\r\n", keydata);
          } 
          break;
        }
          
        default : {
          break;
        }         
     
      } //switch cmd

#if(1)
      // Check and read keydata
      if (CATALEX.getKeys(&keydata)) {
        pc.printf("Keydata = 0x%02x\r\n", keydata);

        CATALEX.cls(); 
        switch (keydata) {
          case TM1637_SW9_BIT : { //sw9, 0xF7
                                  CATALEX.printf("--09");
                                  break;
                                }

          case TM1637_SW10_BIT: { //sw10, 0x77
                                  CATALEX.printf("--10");
                                  break;
                                }

          default :             {
                                  CATALEX.printf("----");
                                  break;
                                }         
        }  

      } // Check keydata
#endif

      myled = !myled;
      wait(0.3);      
    } //while
}
#endif


#if (ROBOTDYN_TEST == 1)
// ROBOTDYN TM1637 6 Digit display test
#include "Font_7Seg.h"

Serial pc(USBTX, USBRX);
DigitalOut myled(LED1); //NOTE: On F401 LED1 is Pin D13, which is SCK!

// DisplayData_t size is 6 bytes (6 Grids @ 8 Segments) 
TM1637::DisplayData_t all_str   = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF};  
TM1637::DisplayData_t cls_str   = {0x00, 0x00, 0x00, 0x00, 0x00, 0x00};  
TM1637::DisplayData_t hello_str = {0x00, 0x00, 0x00, C7_I, C7_H, 0x00};
TM1637::DisplayData_t bye_str   = {0x00, 0x00, 0x00, C7_E, C7_Y, C7_B};

// KeyData_t size is 1 bytes  
TM1637::KeyData_t keydata; 

//TM1637_ROBOTDYN declaration
//New Constructor
TM1637_ROBOTDYN ROBOTDYN(p6, p7);       //LPC1768
//TM1637_ROBOTDYN ROBOTDYN(D9, D10);      //F401

void show_menu() {
//    pc.printf("0:   Exit\n\r");
    pc.printf("1:   All\n\r");    
    pc.printf("2:   Show all segs\r\n");
    pc.printf("3:   Show all chars\n\r");
    pc.printf("4:   Show all digits\n\r");
    pc.printf("5:   Show all icons\n\r");
    pc.printf("6:   Counter\n\r");
    pc.printf("7:   Floats\n\r");
    pc.printf("8:   Kitt\n\r");
    pc.printf("9:   Cls\n\r");
    pc.printf("A:   Bye\n\r");
}


char cmd, bits;
int main() {
    
    pc.printf("Hello World\r\n"); //    
    
    ROBOTDYN.cls(); 
    ROBOTDYN.writeData(all_str);
    wait(2);
    ROBOTDYN.setBrightness(TM1637_BRT3);    
    wait(1);
    ROBOTDYN.setBrightness(TM1637_BRT0);        
    wait(1);
    ROBOTDYN.setBrightness(TM1637_BRT4);            

    wait(1);
    ROBOTDYN.cls(true); 
    ROBOTDYN.writeData(hello_str); 
    
    char cmd2 = '0';
    while (1) {
      
      show_menu();
      cmd2 = pc.getc();

      switch (cmd2) {
        case '1' : {
                      pc.printf("all\r\n");
                      ROBOTDYN.cls(); 
                      ROBOTDYN.writeData(all_str);
                      break;
        }

        case '2' : {
#if(1)
//test to show all segs
          pc.printf("Show all segs\r\n");
          wait(1);          
          ROBOTDYN.cls(); 
         
          for (int i=0; i<TM1637_DISPLAY_MEM; i++) {            
            for (int bit=0; bit<8; bit++) {
              ROBOTDYN.cls(); 

              bits = 0x01 << bit;              
              ROBOTDYN.writeData(bits, i);

              pc.printf("Idx = %d, Bits = 0x%02x\r\n", i, bits);              
//              wait(0.5);
              cmd = pc.getc(); // wait for key
            }  
          }
          pc.printf("\r\nShow all segs done\r\n");                    
#endif 
          break;          
        }

        case '3' : {

#if(1)
//test to show all chars
          pc.printf("Show all alpha chars\r\n");
          wait(1);          
          ROBOTDYN.cls(); 
         
          for (int i=0; i<26; i++) {
            ROBOTDYN.printf("%c", char(i + 'A'));
//            ROBOTDYN.printf("%c", char(i + 'a'));            
            wait(0.25);                      
          }
          pc.printf("Show all alpha chars done\r\n");                    
#endif            

#if(0)
//test to show all chars
          pc.printf("Show all chars\r\n");
          wait(1);          
          ROBOTDYN.cls(); 

          for (int i=FONT_7S_START; i<FONT_7S_END; i++) {
            ROBOTDYN.printf("%c", char(i));
//            wait(0.25);
            cmd = pc.getc(); // wait for key            
          }
          pc.printf("Show all chars done\r\n");                    
#endif            
          break;          
        }
        case '4': {
#if(0)
//test to show all digits (base is 10)
          pc.printf("Show all digits\r\n");
          wait(1);          
          ROBOTDYN.cls(); 

          for (int i=0; i<ROBOTDYN_NR_DIGITS; i++) {

            for (int cnt=0; cnt<10; cnt++) {
              ROBOTDYN.locate(i);
              ROBOTDYN.printf("%0d", cnt);

//              wait(0.5);
              cmd = pc.getc(); // wait for key
            }  
          }
          pc.printf("\r\nShow all digits done\r\n");                    
#endif                

#if(1)
//test to show all digits (base is 0x10)
          pc.printf("Show all hex digits\r\n");
          wait(1);          
          ROBOTDYN.cls(); 

          ROBOTDYN.printf("%06x", 0x01ABCD);
          cmd = pc.getc(); // wait for key                                     
          ROBOTDYN.printf("%06x", 0x0);
                    
          for (int i=0; i<ROBOTDYN_NR_DIGITS; i++) {

            for (int cnt=0; cnt<0x10; cnt++) {
              ROBOTDYN.locate(i);
              ROBOTDYN.printf("%0x", cnt);

//              wait(0.5);
              cmd = pc.getc(); // wait for key
            }  
          }
          pc.printf("\r\nShow all hex digits done\r\n");          
#endif                
          break;          
        }
        case '5':  { 
#if(1)
//test to show all icons
          pc.printf("Show all icons\r\n");
          ROBOTDYN.cls(true); // Also clear all Icons
          
          float delay=0.1;
          // Icons on             
          ROBOTDYN.setIcon(TM1637_ROBOTDYN::DP1); wait(delay);
          ROBOTDYN.setIcon(TM1637_ROBOTDYN::DP2); wait(delay);
          ROBOTDYN.setIcon(TM1637_ROBOTDYN::DP3); wait(delay);
          ROBOTDYN.setIcon(TM1637_ROBOTDYN::DP4); wait(delay);
          ROBOTDYN.setIcon(TM1637_ROBOTDYN::DP5); wait(delay);
          ROBOTDYN.setIcon(TM1637_ROBOTDYN::DP6); wait(delay);                                                  

          wait(delay);
 
          // Icons off      
          ROBOTDYN.clrIcon(TM1637_ROBOTDYN::DP6); wait(delay);
          ROBOTDYN.clrIcon(TM1637_ROBOTDYN::DP5); wait(delay);
          ROBOTDYN.clrIcon(TM1637_ROBOTDYN::DP4); wait(delay);
          ROBOTDYN.clrIcon(TM1637_ROBOTDYN::DP3); wait(delay);
          ROBOTDYN.clrIcon(TM1637_ROBOTDYN::DP2); wait(delay);
          ROBOTDYN.clrIcon(TM1637_ROBOTDYN::DP1); wait(delay);                                        

//          wait(1);          
//          ROBOTDYN.cls(); // clear all, preserve Icons
          pc.printf("Show all icons done\r\n");                    
#endif           
          break;
        }                          

        case '6': {
          ROBOTDYN.cls(); // clear all, preserve Icons

#if(1)
          ROBOTDYN.locate(0);
         
          for (int cnt=0; cnt<=0xFF; cnt++) {
            ROBOTDYN.locate(4);
            ROBOTDYN.printf("%02x", cnt);           
            wait(0.2);
          }  
#endif

//          ROBOTDYN.writeData(hello_str);
//          ROBOTDYN.printf("hello");                  

          break;          
        }
        case '7': {
          ROBOTDYN.cls(); // clear all, preserve Icons
          ROBOTDYN.printf("%1.4f", -0.1234);  // test decimal point display
          wait(0.5);
          ROBOTDYN.cls(); // clear all, preserve Icons          
          ROBOTDYN.printf("%3.3f", 123.456);  // test decimal point display
          break;          
        }

        case '8': {

#if(1)
//test to show KITT
          pc.printf("Show KITT scanner\r\n");

          
//                          012345
const char KITT[][7]   = {{"8     "},
                          {"38    "},
                          {" 38   "},
                          {"  38  "},
                          {"   38 "},
                          {"    38"},
                          {"     3"},
                          {"      "},
                          {"     8"},
                          {"    8E"},
                          {"   8E "},
                          {"  8E  "},
                          {" 8E   "},
                          {"8E    "},
                          {"E     "},
                          {"      "}
                        };     
         
          ROBOTDYN.cls(); // clear all, preserve Icons
          
          while (!pc.readable()) { // wait for key            
            for (int i=0; i < (sizeof(KITT) / 7) ; i++) {
              ROBOTDYN.locate(0);
              ROBOTDYN.printf("%s", KITT[i]);
              wait(0.1);
            }
          }
          cmd = pc.getc(); // read key           
          pc.printf("Show KITT done\r\n");
#endif
          break;
        }  


        case '9': {
          ROBOTDYN.cls(true); // clear all, including Icons      
          break;
        }  

        case 'A': {

          ROBOTDYN.cls(); // clear all, preserve Icons
//          ROBOTDYN.writeData(bye_str);
          ROBOTDYN.printf("Bye");          
       
          break;
        } 

        default : {
          break;
        }         
     
      } //switch cmd

      myled = !myled;
      wait(0.3);      
    } //while
}
#endif


#if (EYEWINK_TEST == 1)
// EYEWINK TM1637 6 Digit display with 5 keys test
#include "Font_7Seg.h"

Serial pc(USBTX, USBRX);
DigitalOut myled(LED1); //NOTE: On F401 LED1 is Pin D13, which is SCK!

// DisplayData_t size is 6 bytes (6 Grids @ 8 Segments) 
TM1637::DisplayData_t all_str   = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF};  
TM1637::DisplayData_t cls_str   = {0x00, 0x00, 0x00, 0x00, 0x00, 0x00};  
TM1637::DisplayData_t hello_str = {C7_H, C7_I, 0x00, 0x00, 0x00, 0x00};
TM1637::DisplayData_t bye_str   = {C7_B, C7_Y, C7_E, 0x00, 0x00, 0x00};

// KeyData_t size is 1 bytes  
TM1637::KeyData_t keydata; 

//TM1637_EYEWINK declaration
//New Constructor
TM1637_EYEWINK EYEWINK(p6, p7);       //LPC1768
//TM1637_EYEWINK EYEWINK(D9, D10);      //F401

void show_menu() {
//    pc.printf("0:   Exit\n\r");
    pc.printf("1:   All\n\r");    
    pc.printf("2:   Show all segs\r\n");
    pc.printf("3:   Show all chars\n\r");
    pc.printf("4:   Show all digits\n\r");
    pc.printf("5:   Show all icons\n\r");
    pc.printf("6:   Counter\n\r");
    pc.printf("7:   Floats\n\r");
    pc.printf("8:   Kitt\n\r");
    pc.printf("9:   Cls\n\r");
    pc.printf("A:   Bye\n\r");
    pc.printf("k:   Key\n\r");    
}


char cmd, bits;
int main() {
    
    pc.printf("Hello World\r\n"); //    
    
    EYEWINK.cls(); 
    EYEWINK.writeData(all_str);
    wait(2);
    EYEWINK.setBrightness(TM1637_BRT3);    
    wait(1);
    EYEWINK.setBrightness(TM1637_BRT0);        
    wait(1);
    EYEWINK.setBrightness(TM1637_BRT4);            

    wait(1);
    EYEWINK.cls(true); 
    EYEWINK.writeData(hello_str); 
    
    char cmd2 = '0';
    while (1) {
      
      show_menu();
      cmd2 = pc.getc();

      switch (cmd2) {
        case '1' : {
                      pc.printf("all\r\n");
                      EYEWINK.cls(); 
                      EYEWINK.writeData(all_str);
                      break;
        }

        case '2' : {
#if(1)
//test to show all segs
          pc.printf("Show all segs\r\n");
          wait(1);          
          EYEWINK.cls(); 
         
          for (int i=0; i<TM1637_DISPLAY_MEM; i++) {            
            for (int bit=0; bit<8; bit++) {
              EYEWINK.cls(); 

              bits = 0x01 << bit;              
              EYEWINK.writeData(bits, i);

              pc.printf("Idx = %d, Bits = 0x%02x\r\n", i, bits);              
//              wait(0.5);
              cmd = pc.getc(); // wait for key
            }  
          }
          pc.printf("\r\nShow all segs done\r\n");                    
#endif 
          break;          
        }

        case '3' : {

#if(1)
//test to show all chars
          pc.printf("Show all alpha chars\r\n");
          wait(1);          
          EYEWINK.cls(); 
         
          for (int i=0; i<26; i++) {
            EYEWINK.printf("%c", char(i + 'A'));
//            EYEWINK.printf("%c", char(i + 'a'));            
            wait(0.25);                      
          }
          pc.printf("Show all alpha chars done\r\n");                    
#endif            

#if(0)
//test to show all chars
          pc.printf("Show all chars\r\n");
          wait(1);          
          EYEWINK.cls(); 

          for (int i=FONT_7S_START; i<FONT_7S_END; i++) {
            EYEWINK.printf("%c", char(i));
//            wait(0.25);
            cmd = pc.getc(); // wait for key            
          }
          pc.printf("Show all chars done\r\n");                    
#endif            
          break;          
        }
        case '4': {
#if(0)
//test to show all digits (base is 10)
          pc.printf("Show all digits\r\n");
          wait(1);          
          EYEWINK.cls(); 

          for (int i=0; i<EYEWINK_NR_DIGITS; i++) {

            for (int cnt=0; cnt<10; cnt++) {
              EYEWINK.locate(i);
              EYEWINK.printf("%0d", cnt);

//              wait(0.5);
              cmd = pc.getc(); // wait for key
            }  
          }
          pc.printf("\r\nShow all digits done\r\n");                    
#endif                

#if(1)
//test to show all digits (base is 0x10)
          pc.printf("Show all hex digits\r\n");
          wait(1);          
          EYEWINK.cls(); 

          EYEWINK.printf("%06x", 0x01ABCD);
          cmd = pc.getc(); // wait for key                                     
          EYEWINK.printf("%06x", 0x0);
                    
          for (int i=0; i<EYEWINK_NR_DIGITS; i++) {

            for (int cnt=0; cnt<0x10; cnt++) {
              EYEWINK.locate(i);
              EYEWINK.printf("%0x", cnt);

//              wait(0.5);
              cmd = pc.getc(); // wait for key
            }  
          }
          pc.printf("\r\nShow all hex digits done\r\n");          
#endif                
          break;          
        }
        case '5':  { 
#if(1)
//test to show all icons
          pc.printf("Show all icons\r\n");
          EYEWINK.cls(true); // Also clear all Icons
          
          float delay=0.1;
          // Icons on             
          EYEWINK.setIcon(TM1637_EYEWINK::DP1); wait(delay);
          EYEWINK.setIcon(TM1637_EYEWINK::DP2); wait(delay);
          EYEWINK.setIcon(TM1637_EYEWINK::DP3); wait(delay);
          EYEWINK.setIcon(TM1637_EYEWINK::DP4); wait(delay);
          EYEWINK.setIcon(TM1637_EYEWINK::DP5); wait(delay);
          EYEWINK.setIcon(TM1637_EYEWINK::DP6); wait(delay);                                                  

          wait(delay);
 
          // Icons off      
          EYEWINK.clrIcon(TM1637_EYEWINK::DP6); wait(delay);
          EYEWINK.clrIcon(TM1637_EYEWINK::DP5); wait(delay);
          EYEWINK.clrIcon(TM1637_EYEWINK::DP4); wait(delay);
          EYEWINK.clrIcon(TM1637_EYEWINK::DP3); wait(delay);
          EYEWINK.clrIcon(TM1637_EYEWINK::DP2); wait(delay);
          EYEWINK.clrIcon(TM1637_EYEWINK::DP1); wait(delay);                                        

//          wait(1);          
//          EYEWINK.cls(); // clear all, preserve Icons
          pc.printf("Show all icons done\r\n");                    
#endif           
          break;
        }                          

        case '6': {
          EYEWINK.cls(); // clear all, preserve Icons

#if(1)
          EYEWINK.locate(0);
         
          for (int cnt=0; cnt<=0xFF; cnt++) {
            EYEWINK.locate(4);
            EYEWINK.printf("%02x", cnt);           
            wait(0.2);
          }  
#endif

//          EYEWINK.writeData(hello_str);
//          EYEWINK.printf("hello");                  

          break;          
        }
        case '7': {
          EYEWINK.cls(); // clear all, preserve Icons
          EYEWINK.printf("%1.4f", -0.1234);  // test decimal point display
          wait(0.5);
          EYEWINK.cls(); // clear all, preserve Icons          
          EYEWINK.printf("%3.3f", 123.456);  // test decimal point display
          break;          
        }

        case '8': {

#if(1)
//test to show KITT
          pc.printf("Show KITT scanner\r\n");

          
//                          012345
const char KITT[][7]   = {{"8     "},
                          {"38    "},
                          {" 38   "},
                          {"  38  "},
                          {"   38 "},
                          {"    38"},
                          {"     3"},
                          {"      "},
                          {"     8"},
                          {"    8E"},
                          {"   8E "},
                          {"  8E  "},
                          {" 8E   "},
                          {"8E    "},
                          {"E     "},
                          {"      "}
                        };     
         
          EYEWINK.cls(); // clear all, preserve Icons
          
          while (!pc.readable()) { // wait for key            
            for (int i=0; i < (sizeof(KITT) / 7) ; i++) {
              EYEWINK.locate(0);
              EYEWINK.printf("%s", KITT[i]);
              wait(0.1);
            }
          }
          cmd = pc.getc(); // read key           
          pc.printf("Show KITT done\r\n");
#endif
          break;
        }  


        case '9': {
          EYEWINK.cls(true); // clear all, including Icons      
          break;
        }  

        case 'A': {

          EYEWINK.cls(); // clear all, preserve Icons
//          EYEWINK.writeData(bye_str);
          EYEWINK.printf("Bye");          
       
          break;
        } 

        case 'k': {
          if (EYEWINK.getKeys(&keydata)) {
            pc.printf("Keydata = 0x%02X\r\n", keydata);
          } 
          break;
        }       

        default : {
          break;
        }         
     
      } //switch cmd

#if(1)
      // Check and read keydata
      if (EYEWINK.getKeys(&keydata)) {
        pc.printf("Keydata = 0x%02X\r\n", keydata);

        if (keydata == TM1637_SW1_BIT) { //sw1 0xEF 
          EYEWINK.cls(); 
          EYEWINK.printf("----01");
        }  

        if (keydata == TM1637_SW2_BIT) { //sw2  0x6F
          EYEWINK.cls(); 
          EYEWINK.printf("----02");          
        }  

        if (keydata == TM1637_SW3_BIT) { //sw3 0xAF 
          EYEWINK.cls(); 
          EYEWINK.printf("----03");
        }  

        if (keydata == TM1637_SW4_BIT) { //sw4 0x2F
          EYEWINK.cls(); 
          EYEWINK.printf("----04");          
        }  

        if (keydata == TM1637_SW5_BIT) { //sw5 0xCF 
          EYEWINK.cls(); 
          EYEWINK.printf("----05");
        }  

        if (keydata == TM1637_SW6_BIT) { //sw6 0x4F
          EYEWINK.cls(); 
          EYEWINK.printf("----06");          
        }  

      } // Check keydata
#endif


      myled = !myled;
      wait(0.3);      
    } //while
}
#endif



#if (TM1637_TEST == 1)
// Direct TM1637 Test

Serial pc(USBTX, USBRX);
DigitalOut myled(LED1);

// DisplayData_t size is 6 bytes (6 Grids @ 8 Segments) 
TM1637::DisplayData_t all_str   = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF};  
TM1637::DisplayData_t cls_str   = {0x00, 0x00, 0x00, 0x00, 0x00, 0x00};  

// KeyData_t size is 1 bytes  
TM1637::KeyData_t keydata; 

// TM1637 declaration
//Old Constructor
//TM1637 TM1637(p5,p6,p7);  //LPC1768
//New Constructor
TM1637 TM1637(p6,p7);     //LPC1768

void show_menu() {
//    pc.printf("0:   Exit\n\r");
    pc.printf("1:   All\n\r");    
    pc.printf("2:   Cls\n\r");        
    pc.printf("3:   Show all segs\r\n");
}


char cmd, bits;
int main() {
    
    pc.printf("Hello World\r\n"); //    
    
    TM1637.cls(); 
    TM1637.writeData(all_str);
    wait(2);
    TM1637.setBrightness(TM1637_BRT3);    
    wait(1);
    TM1637.setBrightness(TM1637_BRT0);        
    wait(1);
    TM1637.setBrightness(TM1637_BRT4);            
    
    while (1) {
     
      cmd = pc.getc();

      switch (cmd) {
        case '1' : 
          TM1637.cls(); 
          TM1637.writeData(all_str);
         break;    
               
        case '2' :       
          TM1637.cls(); 
          TM1637.writeData(cls_str);
         break;    
               
        case '3' :

#if(1)
//test to show all segs
          pc.printf("Show all segs\r\n");
          wait(1);          
          TM1637.cls(); 
         
          for (int i=0; i<TM1637_DISPLAY_MEM; i++) {            
            for (int bit=0; bit<8; bit++) {
              TM1637.cls(); 

              bits = 0x01 << bit;              
              TM1637.writeData(bits, i);

              pc.printf("Idx = %d, Bits = 0x%02x\r\n", i, bits);              
//              wait(0.5);
              cmd = pc.getc(); // wait for key
            }  
          }
          pc.printf("Show all segs done\r\n");                    
#endif 
         break;    
                       
        default : 
          break;          
     
      } //switch cmd

      // Check and read keydata
      if (TM1637.getKeys(&keydata)) {
        pc.printf("Keydata = 0x%02x\r\n", keydata);
 
        if (keydata == TM1637_SW9_BIT) { //sw9  
          TM1637.cls(); 
          TM1637.writeData(0xFF, 1);
          TM1637.writeData(0xFF, 2);          
        }  
      } // Check keydata

      myled = !myled;
      wait(0.3);      
    } //while
}
#endif
```
