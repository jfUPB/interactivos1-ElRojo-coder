## Actividad 2 


### Código 
``` JS
'use strict';

var tileCount = 20;
var actRandomSeed = 0;

var circleAlpha = 130;
var circleColor;

function setup() {
  createCanvas(600, 600);
  noFill();
  circleColor = color(0, 0, 0, circleAlpha);
}

function draw() {
  translate(width / tileCount / 2, height / tileCount / 2);

  background(255);

  randomSeed(actRandomSeed);

  stroke(circleColor);
  strokeWeight(mouseY / 60);

  for (var gridY = 0; gridY < tileCount; gridY++) {
    for (var gridX = 0; gridX < tileCount; gridX++) {

      var posX = width / tileCount * gridX;
      var posY = height / tileCount * gridY;

      var shiftX = random(-mouseX, mouseX) / 20;
      var shiftY = random(-mouseX, mouseX) / 20;

      ellipse(posX + shiftX, posY + shiftY, mouseY / 15, mouseY / 15);
    }
  }
}

function mousePressed() {
  actRandomSeed = random(100000);
}

function keyReleased() {
  if (key == 's' || key == 'S') saveCanvas(gd.timestamp(), 'png');
}
```

#### resumen

Se dibuja una cuadrícula de 20×20 círculos.

Cada círculo está desplazado aleatoriamente dentro de su celda, con la “intensidad” de ese desplazamiento controlada por la posición horizontal del ratón.

El grosor y tamaño de los círculos cambian según la posición vertical del ratón.

Al hacer clic, cambia la “semilla” aleatoria, generando un nuevo patrón.

Con la tecla S, puedes guardar la imagen resultante.

Este código produce un efecto dinámico y “orgánico” de puntos vibrantes que reaccionan al ratón y permiten capturar composiciones únicas.

#### parte clave del código 
``` JS
  for (var gridY = 0; gridY < tileCount; gridY++) {
    for (var gridX = 0; gridX < tileCount; gridX++) {

      var posX = width / tileCount * gridX;
      var posY = height / tileCount * gridY;

      var shiftX = random(-mouseX, mouseX) / 20;
      var shiftY = random(-mouseX, mouseX) / 20;

      ellipse(posX + shiftX, posY + shiftY, mouseY / 15, mouseY / 15);
    }
  }
}
```
-  Dos bucles anidados recorren filas (gridY) y columnas (gridX).

-  posX, posY calculan la posición base de cada celda.

-  shiftX, shiftY desplazan esa posición de forma aleatoria, con magnitud dependiente de la posición horizontal del ratón (mouseX). Así, al mover el ratón en X, varía cuánto “vuelan” los círculos de su centro de celda.

-  ellipse(...) dibuja un círculo en esa posición desplazada. El tamaño del círculo (mouseY/15) depende de la posición vertical del ratón.

