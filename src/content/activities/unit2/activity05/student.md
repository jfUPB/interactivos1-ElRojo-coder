## Actividad 5

### codigo java
``` js
let port;
let connectBtn;
let sendBtn;

function setup() {
    createCanvas(400, 400);
    background(220);

    // Configuración del puerto serial
    port = createSerial();

    // Botón para conectar al micro:bit
    connectBtn = createButton('Connect to micro:bit');
    connectBtn.position(80, 300);
    connectBtn.mousePressed(connectBtnClick);

    // Botón "Corazón" que enviará la señal 'C' al micro:bit
    sendBtn = createButton('Corazón');
    sendBtn.position(220, 300);
    sendBtn.mousePressed(sendBtnClick);
}

function draw() {
  
}

// Función para conectar o desconectar el micro:bit
function connectBtnClick() {
    if (!port.opened()) {
        port.open('MicroPython', 115200);  // Conecta al micro:bit
    } else {
        port.close();  
    }
}


function sendBtnClick() {
    port.write('C');  // Envía 'C' al micro:bit para mostrar el corazón
}
```

### codigo python 

``` py
from microbit import *

# Configura la UART
uart.init(baudrate=115200)

while True:
    if uart.any():  # Verifica si hay datos disponibles
        data = uart.read(1)  # Lee 1 byte
        if data == b'C':  # Si recibe 'C', muestra el corazón
            display.show(Image.HEART)
        else:
            display.clear()  # Limpia la pantalla si recibe otro valor
    else:
        sleep(100)  # Espera 100ms para no sobrecargar el procesador
```
