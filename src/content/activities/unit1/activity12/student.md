## actividad 12

### codigo 
``` js
let port;
let connectBtn;
let hBtn, oBtn, lBtn, aBtn;

function setup() {
    createCanvas(400, 400);
    background(220);

    port = createSerial();
    connectBtn = createButton('Connect to micro:bit');
    connectBtn.position(80, 300);
    connectBtn.mousePressed(connectBtnClick);

    hBtn = createButton('A');
    hBtn.position(80, 350);
    hBtn.mousePressed(() => sendButtonClick('A'));

    oBtn = createButton('B');
    oBtn.position(160, 350);
    oBtn.mousePressed(() => sendButtonClick('B'));

    lBtn = createButton('C');
    lBtn.position(240, 350);
    lBtn.mousePressed(() => sendButtonClick('C'));

    aBtn = createButton('D');
    aBtn.position(320, 350);
    aBtn.mousePressed(() => sendButtonClick('D'));
}

function draw() {
    if (!port.opened()) {
        connectBtn.html('Connect to micro:bit');
    } else {
        connectBtn.html('Disconnect');
    }
}

function connectBtnClick() {
    if (!port.opened()) {
        port.open('MicroPython', 115200);
    } else {
        port.close();
    }
}

function sendButtonClick(letter) {
    port.write(letter);
}
```
while True:

        data = uart.read(1)
        if data:
            if data[0] == ord('A'):
                display.show(Image.HAPPY)
                sleep(500)
                

            if data[0] == ord('B'):
                display.show(Image.SAD)
                sleep(500)
                
            if data[0] == ord('C'):
                display.show(Image.ANGRY)
                sleep(500)
            
            if data[0] == ord('D'):
                display.show(Image.SURPRISED)
                sleep(500)

```
