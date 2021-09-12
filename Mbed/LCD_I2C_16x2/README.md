```
//ez muxik lpc1768on, backlight inv kell, es pcf8574a, azaz SYDZ tipust kell beallitani
//proba: stm103f-en is muxik 20181113
//library: https://developer.mbed.org/users/wim/code/TextLCD/
//lenyeg:
//lcd-RS pcf-p0
//lcd-RW pcf-p1
//lcd-E  pcf-p2
//lcd-BL pcf-p3
//lcd-D4 pcf-p4
//lcd-D5 pcf-p5
//lcd-D6 pcf-p6
//lcd-D7 pcf-p7
//i2c cim: 0x7e, beallithato az A0A1A2 segitsegevel: 0x70-ra is - a jo cim: 0x4E
//i2c sykenner szerint a cim: 0x4E

//i2c scanner syerint a cim: 78-4E, 79-4f
//hasznalni kell ay elsot: 0x4e

#include "mbed.h"
#include "TextLCD.h"
//Serial pc(USBTX, USBRX);                   // Serial connection through USB
//Terminal pc(PA_2, PA_3);  // tx, rx usb-serial cable: PA_2=rx(white), PA_3=tx(green)
//I2C i2c_lcd(PB_9, PB_8);                   // I2C setup
//I2C i2c_lcd(p9,p10); // SDA, SCL on LPC1768
I2C i2c_lcd(PB_9,PB_8);       //sda, scl stm32f103-an
TextLCD_I2C lcd(&i2c_lcd, 0x4E, TextLCD::LCD16x2);          //2C bus, PCF8574 Slaveaddress, LCD Type
 
int main() {
 lcd.printf("cnc controller \n");
}
```
