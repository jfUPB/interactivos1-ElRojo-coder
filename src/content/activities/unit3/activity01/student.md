## Actividad 1
### Código
``` ph
from microbit import * 
import utime

ROJO = 0
AMARILLO = 1
VERDE = 2

class Semaforo:
    def __init__(self, tiempo_rojo, tiempo_amarillo, tiempo_verde):
        self.tiempos = {ROJO: tiempo_rojo, AMARILLO: tiempo_amarillo, VERDE: tiempo_verde}
        self.state = ROJO
        self.start_time = utime.ticks_ms()  # Tiempo de inicio para controlar la duración de cada luz
    
    def update(self):
        current_time = utime.ticks_ms()
        
       
        if utime.ticks_diff(current_time, self.start_time) > self.tiempos[self.state]:
            self.state = (self.state + 1) % 3  # Cambia al siguiente estado en ciclo
            self.start_time = current_time
            
            if self.state == ROJO:
                display.show("R")
            elif self.state == AMARILLO:
                display.show("A")
            elif self.state == VERDE:
                display.show("V")


semaforo1 = Semaforo(5000, 2000, 3000)
semaforo2 = Semaforo(3000, 1000, 2000)
semaforo3 = Semaforo(4000, 3000, 2000)

while True:
    semaforo1.update()
    semaforo2.update()
    semaforo3.update()
    utime.sleep(0.1)
```
### ventajas 
La clase encapsula la lógica del semáforo, evitando la repetición del código para cada uno.

Puedes crear múltiples semáforos con diferentes tiempos sin necesidad de escribir la misma lógica varias veces.

El código se vuelve más estructurado y fácil de entender.

Cada semáforo es una instancia independiente con sus propias propiedades y métodos.
