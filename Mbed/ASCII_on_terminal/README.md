```
//by fauxmaux
//run on LPC1768 or BLUEPILL (NUCLEO-F103RB)
//print ascii characters on terminal

#include "mbed.h"
#include "Terminal.h" //https://os.mbed.com/users/simon/code/Terminal/docs/85184c13476c/Terminal_8h_source.html
//http://www.isthe.com/chongo/tech/comp/ansi_escapes.html
//https://www.ascii-code.com/

//select board:
#define BLUEPILL       0    //stm32f103
#define ALAP           1    //LPC1768

#if BLUEPILL
Terminal term(PA_2, PA_3);  // tx, rx usb-serial cable: PA_2=rx(white), PA_3=tx(green)
//Serial term(PA_2, PA_3);  // tx, rx usb-serial cable: PA_2=rx(white), PA_3=tx(green)
#endif

#if ALAP
Terminal term(USBTX,USBRX);
//Serial term(USBTX,USBRX);
#endif


int a=1;

int main() {
    //wait(5);    //wait for connecting to teraterm
    for(int i=127; i<=254; i++) {
    printf("%d %x %o :: %c      ", i,i,i,i);
    if (a % 3 == 0) {       //csakhogy harmassaval irja ki
        printf(" \n\n\r");
        a=0;
        }
    a++;
    }
}
```
