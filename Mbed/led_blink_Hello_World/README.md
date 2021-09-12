```
//by fauxmaux
//Blinky demo for the STM32F407VET6 boards
//onboard led on STM32F407VGT6 and serial is working
//See http://wiki.stm32duino.com/index.php?title=STM32F407.
//Use "Seed Arch Max" as target platform for the mbed online compiler.
//http://www.cplusplus.com/doc/tutorial/classes/

#include "mbed.h"

#define ALAP           1    //LPC1768
#define BLUEPILL       0    //stm32f103C8T6
#define ARCHMAX        0    //STM32F407VET6

#if ARCHMAX //https://os.mbed.com/users/hudakz/code/STM32F407VET6_Hello/shortlog/
#define MUKODO_LED1 PE_0    //onboard led
Serial pc(PA_9, PA_10);     //Tx, Rx on stm32f407 Serial Tx, Rx to --> rx-white, tx-green on usb2serial cable
#endif

#if BLUEPILL                        //https://os.mbed.com/users/hudakz/code/STM32F103C8T6_Hello/
    #define MUKODO_LED1 PC_13       //onboard led
Serial pc(PA_9, PA_10);       //Tx, Rx
    //Serial bt(PB_10, PB_11);      //Tx, Rx
#endif

#if ALAP
//DigitalOut led1(LED1);
Serial pc(USBTX,USBRX);
    #define MUKODO_LED1 LED1        //onboard led
    #define MUKODO_LED2 LED2        //onboard led
    #define MUKODO_LED3 LED3        //onboard led
    #define MUKODO_LED4 LED4        //onboard led
#endif

class Ledek{
    private:
        DigitalOut  _pin_kimenet;
    public:
        Ledek(PinName _athozat) : _pin_kimenet (_athozat) {}

void villog(void)
        {
        _pin_kimenet = 1;
        wait(0.2);
        _pin_kimenet = 0;
        wait(0.2);
        }
void fent(void)
        {
        _pin_kimenet = 1;
        }
void lent(void)
        {
        _pin_kimenet = 0;
        }
     };

Ledek zold(MUKODO_LED1);
Ledek sarga(MUKODO_LED2);
Ledek piros(MUKODO_LED3);
Ledek kek(MUKODO_LED4);

int main()
    {
    while(1) {
            zold.fent();     
            sarga.fent();   
            piros.lent();
            kek.lent();
            wait(3);
        for (int i=1; i<=20; i++)
        {
            zold.villog();     
            sarga.villog();   
            piros.villog();
                pc.printf("Blink %d\r\n", i);
        }
        kek.fent() ;
        wait(3);
    }
}
```
