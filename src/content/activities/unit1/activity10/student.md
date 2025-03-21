## actividad 10 

### codigo 

``` js
let port; 
let connectBtn; 
let squareColor = [234, 0, 0]; // Color inicial del cuadrado (rojo)

function setup() {
    createCanvas(400, 400);
    background(220);
    
  
    port = createSerial();
    
  
    connectBtn = createButton('Connect to micro:bit');
    connectBtn.position(80, 300);
    connectBtn.mousePressed(connectBtnClick);
    
    
    fill(squareColor);
    rect(150, 150, 100, 100);
}

function draw() {
   
    if (port.availableBytes() > 0) {
        let dataRx = port.read(1); // Lee 1 byte de datos
        
        if (dataRx == 'A') {
            squareColor = [255, 0, 0]; // Rojo si se presiona el botón A
        } else if (dataRx == 'B') {
            squareColor = [0, 255, 0]; // Verde si se presiona el botón B
        }
    }

    
    background(220); 
    fill(squareColor); 
    rect(150, 150, 100, 100); 
}

function connectBtnClick() {
   
    if (!port.opened()) {
        port.open('MicroPython', 115200); 
    } else {
        port.close(); 
    }
}
```

![image](https://github.com/user-attachments/assets/6b1d4df8-85f4-438a-a798-5790f352a4ef)

![image](https://github.com/user-attachments/assets/421a93ba-fbe6-42a8-89cc-216650e7c613)


