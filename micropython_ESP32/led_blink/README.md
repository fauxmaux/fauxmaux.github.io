```
#to use with Thonny http://https://thonny.org/blog/2018/06/05/thonny_and_micropython.html
from machine import Pin
from time import sleep
led = Pin(16,Pin.OUT)
while True:
    led.value(not led.value())
    sleep(0.5)
```
