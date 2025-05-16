## Actividad 11 

### codigo 

``` js
let port; 
let connectBtn; 
let ellipseX = 200; 
let ellipseY = 200; 
let ellipseRadius = 50; 

function setup() {
    createCanvas(400, 400);
    background(220);
    
    port = createSerial(); 
    
    connectBtn = createButton('Connect to micro:bit');
    connectBtn.position(80, 300);
    connectBtn.mousePressed(connectBtnClick);
}

function draw() {
    if (port.availableBytes() > 0) {
        let dataRx = port.read(1); 
        
        if (dataRx == 'A') {
            ellipseX -= 10; 
        } else if (dataRx == 'B') {
            ellipseX += 10; 
        }
    }

    
    ellipseX = constrain(ellipseX, ellipseRadius, width - ellipseRadius);

    background(220); 
    fill(255, 0, 0); 
    ellipse(ellipseX, ellipseY, ellipseRadius * 2, ellipseRadius * 2); 
}

function connectBtnClick() {
    if (!port.opened()) {
        port.open('MicroPython', 115200); 
    } else {
        port.close(); 
    }
}
```

