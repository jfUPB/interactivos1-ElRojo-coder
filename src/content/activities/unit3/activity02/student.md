## Actividad 2
### código
``` ph
# Constantes
MIN_TIME = 10
MAX_TIME = 60
DESACTIVATION_CODE = ["A", "B", "A", "SHAKE"]

from microbit import *
import music
import utime


MIN_TIME = 10  
MAX_TIME = 60  
INITIAL_TIME = 20  
ARMED_SEQUENCE = ["A", "B", "A", "SHAKE"]  

STATE_CONFIG = "CONFIG"
STATE_ARMED = "ARMED"
STATE_EXPLODED = "EXPLODED"


state = STATE_CONFIG
time_left = INITIAL_TIME
disarm_sequence = []
last_update = utime.ticks_ms()

display.scroll(str(time_left))

while True:
    if button_a.was_pressed():
        if state == STATE_CONFIG and time_left < MAX_TIME:
            time_left += 1
            display.show("+1")
            display.show(time_left)
        elif state == STATE_ARMED:
            disarm_sequence.append("A")
            if disarm_sequence == ARMED_SEQUENCE:
                state = STATE_CONFIG
                time_left = INITIAL_TIME
                disarm_sequence = []
                display.scroll(str(time_left))
            elif len(disarm_sequence) > len(ARMED_SEQUENCE):
                disarm_sequence = []

    if button_b.was_pressed():
        if state == STATE_CONFIG and time_left > MIN_TIME:
            time_left -= 1
            display.show("-1")
            display.show(time_left)
        elif state == STATE_ARMED:
            disarm_sequence.append("B")
            if disarm_sequence == ARMED_SEQUENCE:
                state = STATE_CONFIG
                time_left = INITIAL_TIME
                disarm_sequence = []
                display.scroll(str(time_left))
            elif len(disarm_sequence) > len(ARMED_SEQUENCE):
                disarm_sequence = []
    
    if accelerometer.was_gesture("shake"):
        if state == STATE_CONFIG:
            state = STATE_ARMED
            last_update = utime.ticks_ms()
        elif state == STATE_EXPLODED:
            state = STATE_CONFIG
            time_left = INITIAL_TIME
            disarm_sequence = []
            display.scroll(str(time_left))
        elif state == STATE_ARMED:
            disarm_sequence.append("SHAKE")
            if disarm_sequence == ARMED_SEQUENCE:
                state = STATE_CONFIG
                time_left = INITIAL_TIME
                disarm_sequence = []
                display.scroll(str(time_left))
            elif len(disarm_sequence) > len(ARMED_SEQUENCE):
                disarm_sequence = []
    
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
