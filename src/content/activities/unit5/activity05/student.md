### ¿Cómo se comunican el micro:bit y el sketch de p5.js? ¿Qué datos envía el micro:bit?

La comunicación entre el micro:bit y el sketch de p5.js se realiza mediante comunicación serial. En el código MicroPython del micro:bit, se utilizan los siguientes comandos para leer los sensores y botones:

```python

xValue = accelerometer.get_x()
yValue = accelerometer.get_y()
aState = button_a.is_pressed()
bState = button_b.is_pressed()
```
Estos datos corresponden a:

xValue: inclinación en el eje X.

yValue: inclinación en el eje Y.

aState: estado del botón A (True o False).

bState: estado del botón B (True o False).
### ¿Cómo es la estructura del protocolo ASCII usado?
El micro:bit envía los datos en formato ASCII, lo que significa que cada dato se transmite como una cadena de caracteres legibles (texto). La estructura del mensaje es simple: los valores están separados por comas y terminan con un salto de línea (\n). Por ejemplo:

### ¿Qué parte del código de p5.js lee los datos del micro:bit y cómo los transforma?
Esta es la parte del código en p5.js que se encarga de leer los datos y procesarlos:

``` js
if (port.availableBytes() > 0) {
  let data = port.readUntil("\n");
  if (data) {
    data = data.trim();
    let values = data.split(",");
    if (values.length == 4) {
      microBitX = int(values[0]) + windowWidth / 2;
      microBitY = int(values[1]) + windowHeight / 2;
      microBitAState = values[2].toLowerCase() === "true";
      microBitBState = values[3].toLowerCase() === "true";
      updateButtonStates(microBitAState, microBitBState);
    } else {
      print("No se están recibiendo 4 datos del micro:bit");
    }
  }
}
```
### ¿Cómo se detectan los eventos de los botones A y B en p5.js?
Los eventos como “botón A presionado” o “botón B liberado” se detectan comparando el estado actual con el estado anterior de cada botón. Por ejemplo:

``` js
if (newAState === true && prevmicroBitAState === false) {
  // Evento: botón A presionado
}
```
Si el estado anterior era false y el nuevo estado es true, significa que el botón se acaba de presionar.

De manera similar, si era true y pasa a false, significa que el botón se acaba de soltar.
