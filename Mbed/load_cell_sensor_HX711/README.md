```
#include "mbed.h"
#include "HX711.h"

HX711 scale(PA_1,PA_0,128); //(pinData PA_1, pinSCK PA_0, gain [128|32|64])
//DigitalIn mybutton(USER_BUTTON);//Boutton
Serial pc(PA_2, PA_3); // tx, rx

int main() 
{
    pc.printf("+++++ start ++++\r\n");
    int weight; // suly;

    while(1) 
    {
        weight = scale.averageValue(10);
        pc.printf("------------- new\r\n");
        pc.printf("weight= %d\r\n", weight);
        wait(1);
    }
}
```
