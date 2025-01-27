## Actividad 9  

### Generando patrones visuales 

cree una generación de particulas de colores las cuales tienen pocisiones aleatorias dentro de unos rangos y unos colores aleatorios en base al codigo RGB, en esta se puede modificar el tamaño de las particulas  
la separación y el espacio en el que pueden aparecer   

``` js
function setup() {
  createCanvas(800, 800);
  noLoop();
}

function draw() {
  background(255);
  for (let i = 0; i < 1000; i++) {
    let x = random(width);
    let y = random(height);
    let size = random(5, 32);
    let r = map(sin(x * 0.01), -1, 1, 0, 255);
    let g = map(cos(y * 0.01), -1, 1, 0, 255);
    let b = map(sin(x * 0.01) * cos(y * 0.018), -1, 1, 0, 232);
    fill(r, g, b, 150);
    noStroke();
    ellipse(x, y, size, size);
  }
}
```

![image](https://github.com/user-attachments/assets/e42b2bfc-e623-4b35-942a-54307f239dd4)

![image](https://github.com/user-attachments/assets/5383601d-5b7d-40f5-ac03-f0b4e1bf0c77)

#### funciones 
la primera función "r" es la que da el primer rango de colores con Seno 
la segunda función "g" es la que da otro color dentro de la posibilidad 
la tercera función utiliza seno multiplicado por coseno para darle la posición a las particulas 
