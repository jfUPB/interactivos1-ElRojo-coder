from microbit import *
import music
import utime

# Constantes de configuración
MIN_TIME = 10  # Tiempo mínimo en segundos
MAX_TIME = 60  # Tiempo máximo en segundos
INITIAL_TIME = 20  # Tiempo inicial en segundos
ARMED_SEQUENCE = ["A", "B"]  # Secuencia para desarmar la bomba

# Estados de la máquina de estados
STATE_CONFIG = "CONFIG"
STATE_ARMED = "ARMED"
STATE_EXPLODED = "EXPLODED"

# Variables
state = STATE_CONFIG
time_left = INITIAL_TIME
disarm_sequence = []
last_update = utime.ticks_ms()

# Inicialización
display.scroll(str(time_left))
uart.init(baudrate=115200)

while True:
    # Manejo de Botón A (UP)
    if button_a.was_pressed():
        if state == STATE_CONFIG and time_left < MAX_TIME:
            time_left += 1
            display.show("+1")
            uart.write("Tiempo restante: {} segundos\n".format(time_left))
            display.show(time_left)
        elif state == STATE_ARMED:
            disarm_sequence.append("A")
            if disarm_sequence == ARMED_SEQUENCE:
                state = STATE_CONFIG
                time_left = INITIAL_TIME
                disarm_sequence = []
                display.scroll(str(time_left))
                uart.write("Modo de configuración\n")
            elif len(disarm_sequence) > len(ARMED_SEQUENCE):
                disarm_sequence = []

    # Manejo de Botón B (DOWN)
    if button_b.was_pressed():
        if state == STATE_CONFIG and time_left > MIN_TIME:
            time_left -= 1
            display.show("-1")
            uart.write("Tiempo restante: {} segundos\n".format(time_left))
            display.show(time_left)
        elif state == STATE_ARMED:
            disarm_sequence.append("B")
            if disarm_sequence == ARMED_SEQUENCE:
                state = STATE_CONFIG
                time_left = INITIAL_TIME
                disarm_sequence = []
                display.scroll(str(time_left))
                uart.write("Modo de configuración\n")
            elif len(disarm_sequence) > len(ARMED_SEQUENCE):
                disarm_sequence = []

    # Manejo del gesto de shake (ARMED)
    if accelerometer.was_gesture("shake"):
        if state == STATE_CONFIG:
            state = STATE_ARMED
            last_update = utime.ticks_ms()
            uart.write("Bomba armada\n")
        elif state == STATE_EXPLODED:
            state = STATE_CONFIG
            time_left = INITIAL_TIME
            disarm_sequence = []
            display.scroll(str(time_left))
            uart.write("Modo de configuración\n")

    # Manejo de entrada serial
    if uart.any():
        data = uart.read().decode('utf-8').strip()
        if data == "u":
            if state == STATE_CONFIG and time_left < MAX_TIME:
                time_left += 1
                uart.write("Tiempo restante: {} segundos\n".format(time_left))
                display.show(time_left)
        elif data == "d":
            if state == STATE_CONFIG and time_left > MIN_TIME:
                time_left -= 1
                uart.write("Tiempo restante: {} segundos\n".format(time_left))
                display.show(time_left)
        elif data == "s":
            if state == STATE_CONFIG:
                state = STATE_ARMED
                last_update = utime.ticks_ms()
                uart.write("Bomba armada\n")

    # Actualización de la cuenta regresiva
    if state == STATE_ARMED:
        current_time = utime.ticks_ms()
        if utime.ticks_diff(current_time, last_update) >= 1000:
            time_left -= 1
            uart.write("Tiempo restante: {} segundos\n".format(time_left))
            display.show(time_left)
            last_update = current_time
            if time_left <= 0:
                state = STATE_EXPLODED
                uart.write("¡Bomba explotó!\n")
                display.show(Image.SKULL)
                music.play(music.ENTERTAINER)
