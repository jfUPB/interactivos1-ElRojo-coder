## Describe cómo se están comunicando el micro:bit y el sketch de p5.js. ¿Qué datos envía el micro:bit?
los datos desde python se envian en esta parte del codigo:
``` js
xValue = accelerometer.get_x() yValue = accelerometer.get_y() aState = button_a.is_pressed() bState = button_b.is_pressed()
```
con esto lee los datos que se van a enviar
en el codigo de p5 se esta usando una libreria para la comunicación serial desde la funcion draw, se verifica si el puerto esta abierto y recibiendo datos, los datos
que se envian se almacenan en las variables microBitX, microBitY, microBitAstate, microBitBstate.
## ¿Cómo es la estructura del protocolo ASCII usado?
la estructura y lenguaje de ASCII donde se hace uso de los caracrteres como simolos informaticos,esto paa no confundir cuando se usa un caracter o el uso de los mismo
numeros.

## Muestra y explica la parte del código de p5.js donde lee los datos del micro:bit y los transforma en coordenadas de la pantalla.

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
En este segmento del código es donde se realiza la lectura de los datos. La función port.readUntil("\n") se encarga de capturar la información que envía el micro:bit. Luego,
se verifica que se hayan recibido los cuatro valores esperados, lo cual permite asegurarse de que los datos estén completos y sin errores. Finalmente, los valores que llegan como texto se convierten a formato numérico para poder utilizarlos en el sketch.
## ¿Cómo se generan los eventos A pressed y B released que se generan en p5.js a partir de los datos que envía el micro:bit?
por medio de los eventos false o true, se lee si esta sindo presionado o no
if (newAState === true && prevmicroBitAState === false)
