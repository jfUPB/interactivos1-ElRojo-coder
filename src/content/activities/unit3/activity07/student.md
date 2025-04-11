## Actividad 7 

### codigo 

```js
'use strict';

// Constantes de la bomba
const MIN_TIME = 10;
const MAX_TIME = 60;
const INITIAL_TIME = 20;
const ARMED_SEQUENCE = ["A", "B", "A", "SHAKE"];

const STATE_CONFIG = "CONFIG";
const STATE_ARMED = "ARMED";
const STATE_EXPLODED = "EXPLODED";

// Variables de la bomba
let state = STATE_CONFIG;
let time_left = INITIAL_TIME;
let disarm_sequence = [];
let last_update = 0; // Usaremos millis() para el temporizador

// Variables para la conexión serial y botones
let port;
let connectBtn;
let btnA, btnB, btnShake;

function setup() {
  createCanvas(400, 400);
  background(220);
  textSize(32);
  textAlign(CENTER, CENTER);

  // Configuración del puerto serial
  port = createSerial();
  connectBtn = createButton('Connect to micro:bit');
  connectBtn.position(10, 420);
  connectBtn.mousePressed(connectBtnClick);

  // Botones para simular los controles de la bomba
  btnA = createButton('Button A');
  btnA.position(10, 450);
  btnA.mousePressed(handleButtonA);

  btnB = createButton('Button B');
  btnB.position(120, 450);
  btnB.mousePressed(handleButtonB);

  btnShake = createButton('SHAKE');
  btnShake.position(230, 450);
  btnShake.mousePressed(handleShake);

  // Inicializamos el temporizador
  last_update = millis();
}

function draw() {
  background(220);

  // Mostrar estado y tiempo restante en pantalla
  fill(0);
  text("State: " + state, width / 2, 50);
  text("Time Left: " + time_left, width / 2, 100);

  // Si la bomba está armada, actualizamos la cuenta atrás
  if (state === STATE_ARMED) {
    let current_time = millis();
    if (current_time - last_update >= 1000) {
      time_left--;
      last_update = current_time;
      if (time_left <= 0) {
        state = STATE_EXPLODED;
      }
    }
  }

  // Mostrar explosión si la bomba ha explotado
  if (state === STATE_EXPLODED) {
    fill(255, 0, 0);
    textSize(64);
    text("BOOM!", width / 2, height / 2);
  }

  // Actualizar el estado del botón de conexión según el puerto serial
  if (!port.opened()) {
    connectBtn.html('Connect to micro:bit');
  } else {
    connectBtn.html('Disconnect');
  }

  // Ejemplo de lectura de datos del puerto serial
  if (port.availableBytes() > 0) {
    let dataRx = port.read(1);
    // Si se recibe 'B', se cambia el fondo a un color aleatorio (sólo a modo de ejemplo)
    if (dataRx == 'B') {
      background(random(255));
    }
  }
}

// Funciones para simular los botones de la bomba

function handleButtonA() {
  if (state === STATE_CONFIG && time_left < MAX_TIME) {
    time_left++;
  } else if (state === STATE_ARMED) {
    disarm_sequence.push("A");
    checkDisarmSequence();
  }
}

function handleButtonB() {
  if (state === STATE_CONFIG && time_left > MIN_TIME) {
    time_left--;
  } else if (state === STATE_ARMED) {
    disarm_sequence.push("B");
    checkDisarmSequence();
  }
}

function handleShake() {
  if (state === STATE_CONFIG) {
    state = STATE_ARMED;
    last_update = millis();
  } else if (state === STATE_EXPLODED) {
    // Reinicia la bomba luego de la explosión
    state = STATE_CONFIG;
    time_left = INITIAL_TIME;
    disarm_sequence = [];
  } else if (state === STATE_ARMED) {
    disarm_sequence.push("SHAKE");
    checkDisarmSequence();
  }
}

// Función para validar la secuencia de desarme
function checkDisarmSequence() {
  if (arraysEqual(disarm_sequence, ARMED_SEQUENCE)) {
    // Secuencia correcta: desactivar la bomba
    state = STATE_CONFIG;
    time_left = INITIAL_TIME;
    disarm_sequence = [];
  } else if (disarm_sequence.length > ARMED_SEQUENCE.length) {
    disarm_sequence = [];
  }
}

// Función auxiliar para comparar arrays
function arraysEqual(a, b) {
  if (a.length !== b.length) return false;
  for (let i = 0; i < a.length; i++) {
    if (a[i] !== b[i]) return false;
  }
  return true;
}

// Funciones para conectar/desconectar el puerto serial

function connectBtnClick() {
  if (!port.opened()) {
    port.open('MicroPython', 115200);
  } else {
    port.close();
  }
}
```
