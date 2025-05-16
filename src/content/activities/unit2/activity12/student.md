## Actividad 12 

### Código 

``` ph
from microbit import *
import music
import utime

# Constantes de configuración
MIN_TIME = 10  # Tiempo mínimo en segundos
MAX_TIME = 60  # Tiempo máximo en segundos
INITIAL_TIME = 20  # Tiempo inicial en segundos

# Estados de la máquina de estados
STATE_CONFIG = "CONFIG"
STATE_ARMED = "ARMED"
STATE_EXPLODED = "EXPLODED"

# Variables
state = STATE_CONFIG
time_left = INITIAL_TIME
last_update = utime.ticks_ms()  # Inicialización

display.scroll(str(time_left))

while True:
    # Manejo de Botón A (UP)
    if button_a.was_pressed():
        if state == STATE_CONFIG and time_left < MAX_TIME:
            time_left += 1
            display.show("+1")
            display.show(time_left)

    # Manejo de Botón B (DOWN)
    if button_b.was_pressed():
        if state == STATE_CONFIG and time_left > MIN_TIME:
            time_left -= 1
            display.show("-1")
            display.show(time_left)

    # Manejo del gesto de shake (ARMED)
    if accelerometer.was_gesture("shake"):
        if state == STATE_CONFIG:
            state = STATE_ARMED
            last_update = utime.ticks_ms()
        elif state == STATE_EXPLODED:
            state = STATE_CONFIG
            time_left = INITIAL_TIME
            display.scroll(str(time_left))

    # Actualización de la cuenta regresiva
    if state == STATE_ARMED:
        current_time = utime.ticks_ms()
        if utime.ticks_diff(current_time, last_update) >= 1000:
            time_left -= 1
            display.show(time_left)
            last_update = current_time
            if time_left <= 0:
                state = STATE_EXPLODED
                display.show(Image.SKULL)
                music.play(music.ENTERTAINER)

```
