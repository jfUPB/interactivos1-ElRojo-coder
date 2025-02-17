## Actividad 9 
### código
``` ph
from microbit import *
import utime

# Definir los estados del semáforo
ROJO = 0
AMARILLO = 1
VERDE = 2

# Clase que representa la máquina de estados del semáforo
class Semaforo:
    def __init__(self):
        self.state = ROJO 
        self.start_time = utime.ticks_ms()  # Tiempo de inicio para controlar la duración de cada luz
    
    def update(self):
        current_time = utime.ticks_ms()
        
        # Comprobar si ha pasado el tiempo suficiente para cambiar de estado
        if self.state == ROJO:
            if utime.ticks_diff(current_time, self.start_time) > 5000:  
                self.state = AMARILLO  
                self.start_time = current_time  
                display.show("A") 
                
        elif self.state == AMARILLO:
            if utime.ticks_diff(current_time, self.start_time) > 2000:  
                self.state = VERDE  
                self.start_time = current_time  
                display.show("V") 
        
        elif self.state == VERDE:
            if utime.ticks_diff(current_time, self.start_time) > 5000:  
                self.state = ROJO  
                self.start_time = current_time 
                display.show("R")  

semaforo = Semaforo()


while True:
    semaforo.update() 
    utime.sleep(0.1)  
```
### Explicación 
#### Estados:

ROJO: El semáforo está en rojo (el coche debe detenerse).
AMARILLO: El semáforo está en amarillo (preparándose para cambiar a verde).
VERDE: El semáforo está en verde (el coche puede avanzar).
#### Eventos:

El evento que provoca el cambio de estado es el paso del tiempo. Cada estado del semáforo tiene un intervalo de tiempo asignado:
El semáforo permanece en ROJO durante 5 segundos.
El semáforo permanece en AMARILLO durante 2 segundos.
El semáforo permanece en VERDE durante 5 segundos.
#### Acciones:

Cuando el evento ocurre (es decir, cuando el tiempo asignado para cada estado ha pasado), el semáforo cambia de estado y se muestra el color correspondiente en la pantalla del micro:bit.
Acción para ROJO: Después de 5 segundos, el semáforo cambia a amarillo y muestra "Y" en la pantalla.
Acción para AMARILLO: Después de 2 segundos, el semáforo cambia a verde y muestra "V" en la pantalla.
Acción para VERDE: Después de 5 segundos, el semáforo cambia a rojo y muestra "R" en la pantalla.
### Máquina de Estados:
#### Estado ROJO:

Evento: El temporizador ha alcanzado 5 segundos.
Acción: Cambiar a estado AMARILLO y mostrar "A" en el micro:bit.
#### Estado AMARILLO:

Evento: El temporizador ha alcanzado 2 segundos.
Acción: Cambiar a estado VERDE y mostrar "V" en el micro:bit.
#### Estado VERDE:

Evento: El temporizador ha alcanzado 5 segundos.
Acción: Cambiar a estado ROJO y mostrar "R" en el micro:bit.
