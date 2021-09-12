```
//epromV5d
//checked with mbed, not with os

//linux terminal:
//1. keresd meg a tty device-t: ls /dev/ttyACM*
//(valszeg megadja: /dev/ttyACM0)
//majd: screen /dev/ttyACM0
//kilepni: Ctrl-a d

//a _24LCXXX betoltheto innen:
//https://os.mbed.com/users/bant62/code/_24LCXXX/
//Hiroshi munkai kozul az: _24LCXXX

//ket kiserlet:
//elso izben bytot irunk-olvasunk egy bizonyos cimre
//masodik: tobb bytot (nbyte) irunk-olvasunk egy bizonyos cimre
//lpc1768 muxik
//probaltam atmel 24c256-al, 256kbit, 32768x8bit, 1.7-5.5Vcc
//jumpers: wp=write protect, A0,A1,A2 adress: x50-x57
//cimzes a kovetkezo keppen: 1,0,1,0,A2,A1,A0,RW
//tehat az elso het bit samit, pl. 1010000=x50
//ha WP Vcc-n van, irasvedett az eprom

#include "mbed.h"
#include "_24LCXXX.h"

//eredeti: LPC1114FN28
//most: LPC1768
I2C i2c(p28,p27); //I2C i2c(dp5,dp27);        // sda, scl
Serial pc(USBTX, USBRX); //Serial pc(dp16, dp15); // tx, rx
_24LCXXX eeprom(&i2c, 0x50);

DigitalOut myled(LED1); //DigitalOut myled(LED1);

int main() {
    pc.printf("\r\n\n\n+++++++++++++++++++++++++\r\n");
    pc.baud(9600);
    char data1;
    int data6, data7, data8;
    int data3 = 11511;
    int data4 = 22622;
    int data5 = 33733;
    
    pc.printf("memoroy byte data write!\r\n");
    eeprom.byte_write(0, 2);
    eeprom.byte_write(1, 49);
    eeprom.byte_write(2, 7);
    eeprom.byte_write(3, 220);
    pc.printf("end\r\n");
    
    eeprom.nbyte_read( 0, &data1, 1 );
    pc.printf("0:%02x ",data1);
    eeprom.nbyte_read( 1, &data1, 1 );
    pc.printf("1:%02x ",data1);
    eeprom.nbyte_read( 2, &data1, 1 );
    pc.printf("2:%02x ",data1);
    eeprom.nbyte_read( 3, &data1, 1 );
    pc.printf("3:%02x ",data1);
    pc.printf("\r\nend\r\n");   
    pc.printf("------------------------\r\n");
    pc.printf("memoroy int data write!\r\n");
    
//************************ nbyte write-read    
    eeprom.nbyte_write( 10, &data3, sizeof(int));
    //pc.printf("end\r\n");
    
    eeprom.nbyte_write( 20, &data4, sizeof(int));
    //pc.printf("end\r\n");
    
    eeprom.nbyte_write( 30, &data5, sizeof(int));
    pc.printf("end\r\n");

    eeprom.nbyte_read( 10, &data6, sizeof(int) );
    pc.printf("int_data6:%d ",data6);
    pc.printf("\r\nend\r\n");

    eeprom.nbyte_read( 20, &data7, sizeof(int) );
    pc.printf("int_data7:%d ",data7);
    pc.printf("\r\nend\r\n");

    eeprom.nbyte_read( 30, &data8, sizeof(int) );
    pc.printf("int_data8:%d ",data8);
    pc.printf("\r\nend\r\n\n\n");
    
    while(1) {
        myled = 1;
        wait(0.2);
        myled = 0;
        wait(0.2);
    }
}
```
