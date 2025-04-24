## Actividad 5
### enlace a la aplicación ooriginal 
http://www.generative-gestaltung.de/2/sketches/?01_P/P_2_1_3_04

#### Código sin modificar 
```js
'use strict';

var count = 0;
var tileCountX = 6;
var tileCountY = 6;

var drawMode = 1;

function setup() {
  createCanvas(windowWidth, windowHeight);
  rectMode(CENTER);
}

function draw() {
  clear();
  noFill();

  count = mouseX / 10 + 10;
  var para = mouseY / height;

  var tileWidth = width / tileCountX;
  var tileHeight = height / tileCountY;

  for (var gridY = 0; gridY <= tileCountY; gridY++) {
    for (var gridX = 0; gridX <= tileCountX; gridX++) {

      var posX = tileWidth * gridX + tileWidth / 2;
      var posY = tileHeight * gridY + tileHeight / 2;

      push();
      translate(posX, posY);

      // switch between modules
      switch (drawMode) {
      case 1:
        stroke(0);
        for (var i = 0; i < count; i++) {
          rect(0, 0, tileWidth, tileHeight);
          scale(1 - 3 / count);
          rotate(para * 0.1);
        }
        break;
      case 2:
        noStroke();
        for (var i = 0; i < count; i++) {
          var gradient = lerpColor(color(0, 0), color(166, 141, 5), i / count);
          fill(gradient, i / count * 200);
          rotate(QUARTER_PI);
          rect(0, 0, tileWidth, tileHeight);
          scale(1 - 3 / count);
          rotate(para * 1.5);
        }
        break;
      case 3:
        noStroke();
        for (var i = 0; i < count; i++) {
          var gradient = lerpColor(color(0, 130, 164), color(255), i / count);
          fill(gradient, 170);

          push();
          translate(4 * i, 0);
          ellipse(0, 0, tileWidth / 4, tileHeight / 4);
          pop();

          push();
          translate(-4 * i, 0);
          ellipse(0, 0, tileWidth / 4, tileHeight / 4);
          pop();

          scale(1 - 1.5 / count);
          rotate(para * 1.5);
        }
        break;
      }

      pop();

    }
  }
}

function keyReleased() {
  if (key == 's' || key == 'S') saveCanvas(gd.timestamp(), 'png');
  if (key == '1') drawMode = 1;
  if (key == '2') drawMode = 2;
  if (key == '3') drawMode = 3;
  if (keyCode == DOWN_ARROW) tileCountY = max(tileCountY - 1, 1);
  if (keyCode == UP_ARROW) tileCountY += 1;
  if (keyCode == LEFT_ARROW) tileCountX = max(tileCountX - 1, 1);
  if (keyCode == RIGHT_ARROW) tileCountX += 1;
}
```

##### Librerias necesarias

````
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>p5.js + micro:bit</title>
  <!-- 1) p5.js -->
  <script src="https://cdn.jsdelivr.net/npm/p5/lib/p5.min.js"></script>
  <!-- 2) p5.webserial (de gohai) -->
  <script src="https://unpkg.com/@gohai/p5.webserial@^1/libraries/p5.webserial.js"></script>
</head>
<body>
  <!-- 3) Tu sketch -->
  <script src="sketch.js"></script>
</body>
</html>

````
#### Código modificado
```js
'use strict';

let count = 0, para = 0, drawMode = 1;
let lastA = false, lastB = false;
let port, connectBtn;

function setup() {
  createCanvas(windowWidth, windowHeight);
  rectMode(CENTER);

  // Init WebSerial
  port = createSerial();
  connectBtn = createButton("Connect");
  connectBtn.position(10, 10);
  connectBtn.mousePressed(toggleConnection);
}

function toggleConnection() {
  if (!port.opened()) {
    port.open("MicroPython", 115200);
  } else {
    port.close();
  }
}

function draw() {
  background(255);

  if (port.opened()) {
    connectBtn.html("Disconnect");
    readMicrobitData();
  } else {
    connectBtn.html("Connect");
  }

  drawGrid();
}

function readMicrobitData() {
  if (port.availableBytes() > 0) {
    let data = port.readUntil("\n");
    if (!data) return;
    let parts = data.trim().split(",");
    if (parts.length !== 4) return;

    // parse
    let x = int(parts[0]), y = int(parts[1]);
    let a = parts[2].toLowerCase()==="true", b = parts[3].toLowerCase()==="true";

    // map to visual params
    count = map(x, -1024, 1024, 5, 50);
    para  = map(y, -1024, 1024, -1, 1);

    // edge detection
    if (a && !lastA) drawMode = drawMode>1 ? drawMode-1 : 3;
    if (b && !lastB) drawMode = drawMode<3 ? drawMode+1 : 1;

    lastA = a;
    lastB = b;
  }
}

function drawGrid() {
  noFill();
  let cols = 6, rows = 6;
  let tileW = width/cols, tileH = height/rows;

  for (let j = 0; j <= rows; j++) {
    for (let i = 0; i <= cols; i++) {
      push();
      translate(i*tileW + tileW/2, j*tileH + tileH/2);
      switch(drawMode) {
        case 1:
          stroke(0);
          for (let k=0; k<count; k++){
            rect(0,0,tileW,tileH);
            scale(1 - 3/count);
            rotate(para * 0.1);
          }
          break;
        case 2:
          noStroke();
          for (let k=0; k<count; k++){
            let col = lerpColor(color(0), color(166,1,5), k/count);
            fill(col, k/count*200);
            rotate(QUARTER_PI);
            rect(0,0,tileW,tileH);
            scale(1 - 3/count);
            rotate(para * 1.5);
          }
          break;
        case 3:
          noStroke();
          for (let k=0; k<count; k++){
            let col = lerpColor(color(0,130,164), color(255), k/count);
            fill(col,170);
            push(); translate(4*k,0);  ellipse(0,0,tileW/4,tileH/4); pop();
            push(); translate(-4*k,0); ellipse(0,0,tileW/4,tileH/4); pop();
            scale(1 - 1.5/count);
            rotate(para * 1.5);
          }
          break;
      }
      pop();
    }
  }
}
```

#### como se ve sin mover el microbit 

![image](https://github.com/user-attachments/assets/9ad81a2c-107e-41eb-9c89-be69d19e5b6d)

#### como se ve inclinado 30°-45°
![image](https://github.com/user-attachments/assets/7013215d-bfeb-474f-a45f-b678f01b37aa)

#### Acción Botón A 
Cambia al siguiente algoritmo: en este caso, se trata de un algoritmo de círculos intrínsecos que se generan según la inclinación. En su estado sin modificar, solo se ven dos.
#### Ejemplo 
![image](https://github.com/user-attachments/assets/2b3f1ba1-2067-4af2-a1cd-ac1deedb58cb

#### Como se ve en su estado mas alterado 
![image](https://github.com/user-attachments/assets/35754132-771d-4e0a-b255-50038e5daec0)

#### Acción Botón B

Cambia al patron anterior, si se parte desde el inicial serán unos cuadrados rojos conectados entre si los cuales siguen el principio opuesto de los dos anteriores pues este empieza modificado y moviendolo se eliminaran cuadrados y se verá mas pulido 

#### sin modificación 
![image](https://github.com/user-attachments/assets/b5926f19-d250-4fe1-af2f-d7bc1a687f0f)

#### Moviendolo buscando la "perfección"
![image](https://github.com/user-attachments/assets/64c8994f-7152-4f44-8e31-cdb86fd228c4)

