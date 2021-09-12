```
#include "mbed.h"
#include "SHT21_ncleee.h" //https://os.mbed.com/users/graeme88/code/SHT21_ncleee/file/03bbabb7b0b0/SHT21_ncleee.h/
#include "stm32f103c8t6.h"

DigitalOut myled(PC_13);
Serial pc(PA_2, PA_3); // tx, rx usb-serial cable: PA_2=rx(yellow), PA_3=tx(green)
I2C i2c(PB_9,PB_8);       //sda, scl on stm32f103
SHT21 sht(&i2c);

int main() {
    float temperature, humidity; //variabile de tip real [numerele cu virgula], (în engleza punctul zecimal)
    pc.printf("Hello World...\n\r Testing temp & hum\n\r");
    wait(1);

    while(1) {
        temperature = sht.readTemp();
        humidity = sht.readHumidity();
        pc.printf("Temperature=%.2f%cC Humidity=%.2f%%\n\r",temperature,248,humidity);
        myled = 1;
        wait(0.5);
        myled = 0;
        wait(1);
    }
}
```
