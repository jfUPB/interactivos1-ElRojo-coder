``` py
from microbit import *
import music
import utime

# Constantes de configuración
MIN_TIME = 10          # Tiempo mínimo en segundos
MAX_TIME = 60          # Tiempo máximo en segundos
INITIAL_TIME = 20      # Tiempo inicial en segundos
ARMED_SEQUENCE = ["A", "B", "A", "S"]  # Secuencia para desarmar la bomba

# Estados de la máquina de estados
STATE_CONFIG = "CONFIG"
STATE_ARMED = "ARMED"
STATE_EXPLODED = "EXPLODED"

# Variables globales
current_state = STATE_CONFIG
time_left = INITIAL_TIME
disarm_sequence = []
last_update = utime.ticks_ms()
event = None

display.scroll(str(time_left))
uart.init(baudrate=115200)

def tareaEventos():
    global event
    # Manejo de Botón A (Evento 'A')
    if button_a.was_pressed():
        event = 'A'
    # Manejo de Botón B (Evento 'B')
    if button_b.was_pressed():
        event = 'B'
    # Manejo del gesto de shake (Evento 'S')
    if accelerometer.was_gesture("shake"):
        event = 'S'
    # Manejo del toque en el logo (Evento 'T')
    if pin_logo.is_touched():
        event = 'T'
    # Manejo de entrada serial (comandos "A", "B", "S", "T")
    if uart.any():
        data = uart.read(1)
        if data:
            if data[0] == ord("A"):
                event = "A"
            elif data[0] == ord("B"):
                event = "B"
            elif data[0] == ord("S"):
                event = "S"
            elif data[0] == ord("T"):
                event = "T"

def tareaBomba():
    global current_state, time_left, disarm_sequence, last_update, event

    # Estado: CONFIGURACIÓN
    if current_state == STATE_CONFIG:
        if event == 'A':
            if time_left < MAX_TIME:
                time_left += 1
                display.show("+1")
                uart.write("Tiempo restante: {} segundos\n".format(time_left))
                display.show(time_left)
        elif event == 'B':
            if time_left > MIN_TIME:
                time_left -= 1
                display.show("-1")
                uart.write("Tiempo restante: {} segundos\n".format(time_left))
                display.show(time_left)
        # Con shake o toque se arma la bomba
        elif event in ('S', 'T'):
            current_state = STATE_ARMED
            last_update = utime.ticks_ms()
            uart.write("Bomba armada\n")
        event = None

    # Estado: ARMA LA BOMBA (ARMED)
    elif current_state == STATE_ARMED:
        # Actualización de la cuenta regresiva cada segundo
        current_time = utime.ticks_ms()
        if utime.ticks_diff(current_time, last_update) >= 1000:
            time_left -= 1
            uart.write("Tiempo restante: {} segundos\n".format(time_left))
            display.show(time_left)
            last_update = current_time
            if time_left <= 0:
                current_state = STATE_EXPLODED
                display.show(Image.SKULL)
                uart.write("¡Bomba explotó!\n")
                music.play(music.ENTERTAINER)
        
        # Manejo de la secuencia para desarme
        if event in ('A', 'B', 'S'):
            disarm_sequence.append(event)
            uart.write("Secuencia: {}\n".format(disarm_sequence))
            if disarm_sequence == ARMED_SEQUENCE:
                current_state = STATE_CONFIG
                time_left = INITIAL_TIME
                disarm_sequence = []
                display.scroll(str(time_left))
                uart.write("Modo de configuracion\n")
            elif len(disarm_sequence) > len(ARMED_SEQUENCE):
                disarm_sequence = []  # Reiniciar la secuencia si no coincide
            event = None

    # Estado: EXPLOTADA
    elif current_state == STATE_EXPLODED:
        # Se resetea la bomba si se recibe un shake
        if event == 'S':
            current_state = STATE_CONFIG
            time_left = INITIAL_TIME
            disarm_sequence = []
            display.scroll(str(time_left))
            uart.write("Modo de configuracion\n")
            event = None

while True:
    tareaEventos()  # Leer eventos de sensores y puerto serial
    tareaBomba()    # Procesar la bomba según los eventos


```

``` py
from microbit import *
import music
import utime

# Constantes de configuración
MIN_TIME = 10  # Tiempo mínimo en segundos
MAX_TIME = 60  # Tiempo máximo en segundos
INITIAL_TIME = 20  # Tiempo inicial en segundos
ARMED_SEQUENCE = ["A", "B", "A", "S"]  # Secuencia para desarmar la bomba

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

# Variables de eventos
event = None  # Almacena el evento que ha ocurrido

# Función que maneja la bomba
def tareaBomba():
    global state, time_left, disarm_sequence, last_update, event

    # Manejo de eventos
    if event == 'A':  # Evento del botón A o comando A por puerto serial
        if state == STATE_CONFIG and time_left < MAX_TIME:
            time_left += 1
            display.show("+1")
            uart.write("Tiempo restante: {} segundos\n".format(time_left))
            display.show(time_left)
        elif state == STATE_ARMED:
            disarm_sequence.append("A")
            print(disarm_sequence)  # Para depuración, muestra la secuencia
            if disarm_sequence == ARMED_SEQUENCE:
                state = STATE_CONFIG
                time_left = INITIAL_TIME
                disarm_sequence = []
                display.scroll(str(time_left))
                uart.write("Modo de configuración\n")
            elif len(disarm_sequence) > len(ARMED_SEQUENCE):
                disarm_sequence = []  # Resetear secuencia si no coincide
    
    elif event == 'B':  # Evento del botón B o comando B por puerto serial
        if state == STATE_CONFIG and time_left > MIN_TIME:
            time_left -= 1
            display.show("-1")
            uart.write("Tiempo restante: {} segundos\n".format(time_left))
            display.show(time_left)
        elif state == STATE_ARMED:
            disarm_sequence.append("B")
            print(disarm_sequence)  # Para depuración, muestra la secuencia
            if disarm_sequence == ARMED_SEQUENCE:
                state = STATE_CONFIG
                time_left = INITIAL_TIME
                disarm_sequence = []
                display.scroll(str(time_left))
                uart.write("Modo de configuración\n")
            elif len(disarm_sequence) > len(ARMED_SEQUENCE):
                disarm_sequence = []  # Resetear secuencia si no coincide

    elif event == 'S':  # Evento de shake o comando S por puerto serial
        if state == STATE_CONFIG:
            state = STATE_ARMED
            last_update = utime.ticks_ms()
            uart.write("Bomba armada\n")
        elif state == STATE_ARMED:
            disarm_sequence.append("S")
            print(disarm_sequence)  # Para depuración, muestra la secuencia
            if disarm_sequence == ARMED_SEQUENCE:
                state = STATE_CONFIG
                time_left = INITIAL_TIME
                disarm_sequence = []
                display.scroll(str(time_left))
                uart.write("Modo de configuración\n")
            elif len(disarm_sequence) > len(ARMED_SEQUENCE):
                disarm_sequence = []  # Resetear secuencia si no coincide
    
    elif event == 'T':  # Evento de touch o comando T por puerto serial
        if state == STATE_CONFIG:
            state = STATE_ARMED
            last_update = utime.ticks_ms()
            uart.write("Bomba armada por touch\n")

    event = None  # Resetear evento después de procesarlo

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

# Función que maneja los eventos de sensores y puerto serial
def tareaEventos():
    global event

    # Manejo de Botón A (Evento 'A')
    if button_a.was_pressed():
        event = 'A'

    # Manejo de Botón B (Evento 'B')
    if button_b.was_pressed():
        event = 'B'

    # Manejo del gesto de shake (Evento 'S')
    if accelerometer.was_gesture("shake"):
        event = 'S'

    if pin_logo.is_touched():
        event = 'T'

    # Manejo de entrada serial (comandos "A", "B", "S", "T")
    if uart.any():
        data = uart.read(1)
        if data: 
            if data[0] == ord("A"):
                event = "A"
            elif data[0] == ord("B"):
                event = "B"
            elif data[0] == ord("S"):
                event = "S"
            elif data[0] == ord("T"):
                event = "T"

while True:
    tareaEventos()  # Leer eventos de sensores y puerto serial
    tareaBomba()    # Procesar la bomba según los eventos



```






