## actividad 6

### código
``` ph
from microbit import *

# Inicialización de la comunicación UART
uart.init(baudrate=115200)

# Mostrar una imagen inicial (mariposa)
display.show(Image.BUTTERFLY)

while True:
    # Si se presiona el botón A
    if button_a.is_pressed():
        uart.write('A') 
        display.show("A")  
        sleep(500) 

    # Si se presiona el botón B
    if button_b.is_pressed():
        uart.write('B')  
        display.show("B")  
        sleep(500)  

    # Si se detecta una sacudida
    if accelerometer.was_gesture('shake'):
        uart.write('C')  
        display.show("C")  
        sleep(500)  
```

### funcionamiento

cuando se presiona el botón A del microbit aparece la letra A en el microbit cuando se presiona el botón B aparece la B en el microbit y cuando se sacude aparece la C 
