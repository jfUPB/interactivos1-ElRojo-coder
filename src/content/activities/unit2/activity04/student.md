## Actividad 4 

el experimento era para comprobar si un estado era unico o era ciclico/infinito

la hipotesis era que mientras que no hubiera una orden de parar no iba a parar de repetir la acción mientras esta sea afirmativa 
### codigo 
``` js
let port;
let connectBtn;
let sendBtn;
let currentColor;

function setup() {
    createCanvas(400, 400);
    background(220);
    port = createSerial();
    connectBtn = createButton('Connect to micro:bit');
    connectBtn.position(80, 300);
    connectBtn.mousePressed(connectBtnClick);
    
    sendBtn = createButton('Send Love');
    sendBtn.position(220, 300);
    sendBtn.mousePressed(sendBtnClick);
    
    fill('white');
    ellipse(width / 2, height / 2, 100, 100);
}

function draw() {
    // Leer datos del puerto serial
    if (port.availableBytes() > 0) {
        let dataRx = port.read(1);

        if (dataRx == 'B') {
            // Cambiar a un color aleatorio cada vez que se presiona el botón B
            currentColor = color(random(255), random(255), random(255));
        }

        // Actualizamos el fondo y el color del círculo
        background(220);
        fill(currentColor);
        ellipse(width / 2, height / 2, 100, 100);  // Dibuja el círculo con el nuevo color
    }

    if (!port.opened()) {
        connectBtn.html('Connect to micro:bit');
    }
    else {
        connectBtn.html('Disconnect');
    }
}

// Función para conectar o desconectar el micro:bit
function connectBtnClick() {
    if (!port.opened()) {
        port.open('MicroPython', 115200);  // Abrir puerto serial
    } else {
        port.close();  // Cerrar puerto serial
    }
}

// Función para enviar datos desde la interfaz
function sendBtnClick() {
    port.write('h');  // Enviar un mensaje al micro:bit (puedes cambiar este valor)
}
```
esa es el codigo de java para p5js

### codigo microbit

``` py
from microbit import *

# Configuramos la UART para la comunicación serial
uart.init(baudrate=115200)

while True:
    if button_b.is_pressed():
        uart.write('B')  # Enviar 'B' cuando se presiona el botón B
        sleep(100)  # Esperar para evitar lecturas rápidas
    else:
        sleep(100)
