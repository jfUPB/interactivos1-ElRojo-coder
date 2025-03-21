## Actividad 8

### codigo p5js 
``` js
let port; let connectBtn; let aBtn, bBtn, sBtn, tBtn;

function setup() { createCanvas(400, 400); background(220);

// Inicializa la comunicación serial port = createSerial();

// Botón para conectar o desconectar del micro:bit connectBtn = createButton(‘Conectar al micro:bit’); connectBtn.position(80, 300); connectBtn.mousePressed(connectBtnClick);

// Botones para enviar comandos a la bomba aBtn = createButton(‘A’); aBtn.position(80, 350); aBtn.mousePressed(() => sendButtonClick(‘A’));

bBtn = createButton(‘B’); bBtn.position(160, 350); bBtn.mousePressed(() => sendButtonClick(‘B’));

sBtn = createButton(‘S’); sBtn.position(240, 350); sBtn.mousePressed(() => sendButtonClick(‘S’));

tBtn = createButton(‘T’); tBtn.position(320, 350); tBtn.mousePressed(() => sendButtonClick(‘T’)); }

function draw() { // Actualiza el texto del botón de conexión según el estado del puerto if (!port.opened()) { connectBtn.html(‘Conectar al micro:bit’); } else { connectBtn.html(‘Desconectar’); } }

function connectBtnClick() { // Conecta o desconecta el puerto serial if (!port.opened()) { port.open(‘MicroPython’, 115200); } else { port.close(); } }

function sendButtonClick(letter) { // Envía el carácter seleccionado al micro:bit port.write(letter); }
```

### Codigo python

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
