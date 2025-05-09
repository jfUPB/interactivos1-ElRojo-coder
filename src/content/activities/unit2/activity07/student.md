## Actividad 7 

### Código 

``` ph
from microbit import *
import music


uart.init(baudrate=115200)


display.show(Image.HAPPY)

while True:
    # Si se presiona el botón A
    if button_a.is_pressed():
        uart.write('A') 
        display.show("A")  
        music.play(music.POWER_UP)  
        sleep(500) 

    # Si se presiona el botón B
    if button_b.is_pressed():
        uart.write('B')  
        display.show("B")  
        music.play(music.POWER_DOWN)  
        sleep(500)  

    # Si se detecta una sacudida
    if accelerometer.was_gesture('shake'):
        uart.write('C')  
        display.show("C")  
        music.play(music.JUMP_UP)  
        sleep(500)
```


### explicacióm

usé el codigo del punto anterior y solo le agregué los sonidos a cada botón y a la sacudida 
