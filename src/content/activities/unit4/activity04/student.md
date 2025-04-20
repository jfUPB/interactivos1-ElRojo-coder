

#### 1. ¿Para qué se usan las imágenes SVG en preload()? ¿Qué representan?
```js
Copiar
Editar
lineModule[1] = loadImage("02.svg");
…  
lineModule[4] = loadImage("05.svg");
```
Representan “módulos de línea”: son distintos assets gráficos (pequeños trozos de trazo) que, al presionar el botón A del micro:bit, se rotan y pintan enlazados formando patrones.

Ventaja de preload: al cargarlos antes de setup(), garantizas que estén listos para ser “tintados” y rotados sin retrasos durante el dibujo.

#### 2. ¿Por qué usar una máquina de estados (state machine)?
Tiene dos comportamientos muy distintos:

Esperar a que el micro:bit se conecte

Leer y procesar datos una vez conectado

Sin máquina de estados, tu draw() estaría lleno de if (microBitConnected)… else … y sería difícil de mantener. Con:

```js

const STATES = {
  WAIT_MICROBIT_CONNECTION: "WAIT",
  RUNNING:                "RUNNING"
};
let appState = STATES.WAIT_MICROBIT_CONNECTION;
```
cada frame solo hace lo que “le toca” en el estado actual, y el cambio de estado sucede en un único lugar.

#### 3. ¿Qué ocurre al hacer click en el botón “Connect to micro:bit”?
```js

function connectBtnClick() {
  if (!port.opened()) {
    port.open("MicroPython", 115200);
  } else {
    port.close();
  }
}
```
Si está cerrado → abre el puerto en modo “MicroPython” a 115 200 bps.

Si ya está abierto → lo cierra.
Así alternas conexión y desconexión.

#### 4. ¿Para qué abrir el puerto serial? ¿Qué pasa si está cerrado?
Abrir el puerto permite que el navegador reciba los bytes que el micro:bit envía por USB.

Si el puerto está cerrado:

El micro:bit seguirá enviando datos, pero el navegador no los recibirá: no hay “escucha” en el otro extremo.

port.availableBytes() devolverá 0 siempre, y readUntil() nunca leerá nada.

5. ¿Qué sucede si el micro:bit no envía el “\n” al final del paquete?
La línea:

js
```
let data = port.readUntil("\n");
```
espera hasta encontrar el caracter de fin de línea.

Sin “\n” → readUntil() se quedará esperando indefinidamente ese delimitador, bloqueando la lectura y acumulando bytes sin procesar.

Solución típica: asegurarse en el código del micro:bit de incluir siempre \n, o usar un timeout/lectura por bloques fijos.

#### 6 ¿Por qué se suma windowWidth/2 y windowHeight/2 a los valores X, Y?
```js

microBitX = int(values[0]) + windowWidth/2;
microBitY = int(values[1]) + windowHeight/2;
```
El micro:bit envía valores X,Y relativos a su centro (o a un rango centrado en 0).

Al sumar la mitad del ancho/alto de la ventana, trasladas el origen (0,0) al centro de la pantalla, de modo que un “0” del micro:bit aparezca en el centro de tu canvas.

#### 7. ¿Cómo verificar que los eventos de “keypressed” y “keyreleased” se están generando?
En updateButtonStates ya hay print("A pressed") y print("B released"). Al abrir la consola de p5.js:

Presiona y suelta los botones del micro:bit.

Deberías ver esos mensajes en la consola una sola vez por transición.

También podrías dibujar un círculo o cambiar un color en pantalla dentro de esos bloques para verlo gráficamente.

#### 8. ¿Qué hace updateButtonStates y por qué guarda el estado anterior?
```js

if (newAState === true && prevmicroBitAState === false) { … }
// …
prevmicroBitAState = newAState;
```
Detecta flancos (edges): solo genera el evento cuando A pasa de “false” a “true” (borde de subida) y B de “true” a “false” (borde de bajada).

Sin el estado anterior:

A estaría “true” en todos los frames mientras lo mantienes pulsado → dispararías el evento continuamente.

No podrías distinguir “pulsado justo ahora” de “sigue pulsado”.

#### 10. Diferencias entre el sketch original y el nuevo

Aspecto	Sketch original	Nuevo sketch con micro:bit
Eventos de ratón	Usaba mousePressed() para click local	Se eliminan (el micro:bit controla la posición y disparo)
Barra de espacio	Tenía un caso en keyReleased para " "	Ya no aparece (la interacción es por botones A/B)
Interfaz de conexión	No existía botón	Ahora hay createButton("Connect to micro:bit")
Gestión de estados	Todo corría en un solo estado RUNNING	Máquina de estados con WAIT_MICROBIT_CONNECTION y RUNNING
Serial	No había librería ni código de puerto	Se usa p5.webserial para abrir, leer y cerrar
Preload de SVGs	Igual	Igual
11. ¿Qué significa “No se están recibiendo 4 datos…” y por qué aparece solo al inicio?
Ese mensaje se imprime cuando:

``` js

if (values.length != 4) {
  print("No se están recibiendo 4 datos del micro:bit");
}
```
Causa: al abrir la conexión, puede que llegue un fragmento parcial (por ejemplo solo "123,45," sin el último \n o sin los cuatro campos completos) y split(",") devuelva menos de 4 elementos.

Ocurre en pocos frames (normalmente al inicio) porque luego el búfer se alinea y cada línea completa llega correctamente con las 4 partes y el \n.

Soluciones posibles:

Ignorar los primeros fragmentos:

```js

if (values.length == 4) {
  // procesar…
} // no imprimir nada en el else
Vaciar el búfer al conectar:
```
```js
port.readUntil("\n"); // descarta línea parcial inicial
```

Añadir un contador para solo mostrar el mensaje tras varios frames consecutivos que fallen.

### Conclusión:
– Con el patrón de máquina de estados controlas limpiamente la fase de “espera de conexión” y la de “procesamiento de datos”.
– Al detectar flancos con variables previas evitas disparos repetidos.
– Y dispones de todas las construcciones (serial, preload, button, draw) para interactuar con tu micro:bit y generar gráficos en p5.js basados en sus sensores y botones.
