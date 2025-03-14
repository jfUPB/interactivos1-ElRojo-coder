let port;
let connectBtn;
let aBtn, bBtn, sBtn, tBtn;

function setup() {
  createCanvas(400, 400);
  background(220);

  // Inicializa la comunicación serial
  port = createSerial();

  // Botón para conectar o desconectar del micro:bit
  connectBtn = createButton('Conectar al micro:bit');
  connectBtn.position(80, 300);
  connectBtn.mousePressed(connectBtnClick);

  // Botones para enviar comandos a la bomba
  aBtn = createButton('A');
  aBtn.position(80, 350);
  aBtn.mousePressed(() => sendButtonClick('A'));

  bBtn = createButton('B');
  bBtn.position(160, 350);
  bBtn.mousePressed(() => sendButtonClick('B'));

  sBtn = createButton('S');
  sBtn.position(240, 350);
  sBtn.mousePressed(() => sendButtonClick('S'));

  tBtn = createButton('T');
  tBtn.position(320, 350);
  tBtn.mousePressed(() => sendButtonClick('T'));
}

function draw() {
  // Actualiza el texto del botón de conexión según el estado del puerto
  if (!port.opened()) {
    connectBtn.html('Conectar al micro:bit');
  } else {
    connectBtn.html('Desconectar');
  }
}

function connectBtnClick() {
  // Conecta o desconecta el puerto serial
  if (!port.opened()) {
    port.open('MicroPython', 115200);
  } else {
    port.close();
  }
}

function sendButtonClick(letter) {
  // Envía el carácter seleccionado al micro:bit
  port.write(letter);
}
