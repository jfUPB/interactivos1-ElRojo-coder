

## actividad 09
### 1. Conexión y envío de comandos desde p5.js
#### Vector 1.1: Conectar al micro:bit
Precondición: micro:bit encendida y con el programa en ejecución, p5.js abierto con la app.
Estado actual (p5.js): No conectado (port.opened() = false).
Evento: Clic en “Conectar al micro:bit”.
Acciones esperadas (p5.js):
Abre el puerto serial con port.open('MicroPython', 115200).
El botón cambia a “Desconectar”.
Estado siguiente (p5.js): port.opened() = true.
#### Vector 1.2: Desconectar del micro:bit
Precondición: Puerto abierto.
Estado actual (p5.js): port.opened() = true.
Evento: Clic en “Desconectar”.
Acciones esperadas (p5.js):
Cierra el puerto serial con port.close().
El botón vuelve a mostrar “Conectar al micro:bit”.
Estado siguiente (p5.js): port.opened() = false.
(Estas pruebas validan que el botón de conexión en p5.js funciona y cambia su estado visual.)

### 2. Estado CONFIG en micro:bit
(Por defecto, el código MicroPython inicia en STATE_CONFIG con time_left = 20.)

#### Vector 2.1: Aumentar tiempo (evento “A”) desde p5.js
Estado actual (micro:bit): CONFIG
Evento: Se hace clic en el botón “A” en la app p5.js → envía 'A' por serial → micro:bit lo recibe en tareaEventos().
Acciones esperadas (micro:bit):
Si time_left < MAX_TIME, incrementa time_left en 1.
Muestra “+1” y luego el nuevo valor en el display.
Envía por UART: "Tiempo restante: XX segundos\n".
Estado siguiente (micro:bit): Permanece en CONFIG.
#### Vector 2.2: Disminuir tiempo (evento “B”) desde p5.js
Estado actual (micro:bit): CONFIG
Evento: Botón “B” en la app p5.js → envía 'B' por serial.
Acciones esperadas (micro:bit):
Si time_left > MIN_TIME, decrementa time_left en 1.
Muestra “-1” y luego el nuevo valor en el display.
Envía por UART: "Tiempo restante: XX segundos\n".
Estado siguiente (micro:bit): Permanece en CONFIG.
#### Vector 2.3: Límite de tiempo al máximo (evento “A”)
Estado actual: CONFIG, time_left = 60.
Evento: Recibir 'A'.
Acciones esperadas:
Detecta que time_left == MAX_TIME; no debe incrementar más allá de 60.
Permanece en 60.
Estado siguiente: CONFIG (sin cambio).
#### Vector 2.4: Límite de tiempo al mínimo (evento “B”)
Estado actual: CONFIG, time_left = 10.
Evento: Recibir 'B'.
Acciones esperadas:
Detecta que time_left == MIN_TIME; no debe decrementar por debajo de 10.
Permanece en 10.
Estado siguiente: CONFIG.
#### Vector 2.5: Armar la bomba (evento “S” o “T”)
Estado actual: CONFIG.
Evento:
Desde p5.js: clic en “S” (envía 'S')
O bien, en micro:bit físico, un shake real.
O clic en “T” (envía 'T') o un toque real en el pin_logo.
Acciones esperadas:
Transición a STATE_ARMED.
last_update se reinicia (utime.ticks_ms()).
Envía por UART: "Bomba armada\n".
Estado siguiente: ARMED.
### 3. Estado ARMED en micro:bit
#### Vector 3.1: Conteo regresivo
Estado actual: ARMED, con time_left = 20 (por ejemplo).
Evento: Pasa el tiempo (1 segundo).
Acciones esperadas:
Cada 1000 ms, decrementa time_left en 1.
Muestra el nuevo valor en el display.
Envía por UART: "Tiempo restante: XX segundos\n".
Estado siguiente: Permanece en ARMED hasta que time_left llegue a 0 o ocurra otro evento.
#### Vector 3.2: Desarmar con secuencia [A, B, A, S]
Estado actual: ARMED, con time_left > 0.
Evento: Recibir 'A', 'B', 'A', 'S' en ese orden, sea por los botones p5.js o por los botones/gestos del micro:bit.
Acciones esperadas:
Cada vez que llega un evento en ARMED, se añade a disarm_sequence.
Si en algún momento disarm_sequence == ["A","B","A","S"], entonces:
Cambia a STATE_CONFIG.
time_left se restablece a INITIAL_TIME (20).
disarm_sequence se vacía.
Se muestra time_left en el display.
Envía por UART: "Modo de configuracion\n".
Estado siguiente: CONFIG.
#### Vector 3.3: Secuencia errónea
Estado actual: ARMED.
Evento: Se ingresa una secuencia que no coincide con ["A","B","A","S"] (por ejemplo, [A, B, B]).
Acciones esperadas:
Cada evento se va añadiendo a disarm_sequence.
Al notar que disarm_sequence.length > len(ARMED_SEQUENCE) (o que no coincide con el patrón), la secuencia se reinicia (disarm_sequence = []).
No cambia de estado ni de tiempo.
Estado siguiente: Permanece en ARMED.
#### Vector 3.4: Explosión por llegar a 0
Estado actual: ARMED, time_left = 1.
Evento: Pasa 1 segundo.
Acciones esperadas:
time_left decrementa a 0.
Transición a STATE_EXPLODED.
Muestra Image.SKULL en el display.
Envía por UART: "¡Bomba explotó!\n".
Reproduce música con music.play(music.ENTERTAINER).
Estado siguiente: EXPLODED.
### 4. Estado EXPLODED en micro:bit
#### Vector 4.1: Reset tras explosión (evento “S”)
Estado actual: EXPLODED.
Evento: Recibir 'S' (ya sea por un shake real o desde p5.js).
Acciones esperadas:
Cambia a STATE_CONFIG.
time_left se pone en INITIAL_TIME (20).
disarm_sequence se vacía.
Muestra time_left en el display.
Envía por UART: "Modo de configuracion\n".
Estado siguiente: CONFIG.
#### Vector 4.2: Ignorar otros eventos en EXPLODED
Estado actual: EXPLODED.
Evento: Recibir 'A', 'B', 'T', o una secuencia incompleta.
Acciones esperadas:
El único evento que se maneja en EXPLODED es 'S' para resetear.
No hace nada con 'A', 'B', 'T'.
Estado siguiente: Permanece en EXPLODED.
### 5. Pruebas combinadas y de “flujo completo”
#### Vector 5.1: Flujo típico (p5.js + micro:bit)
Conectar en p5.js (Vector 1.1).
micro:bit en CONFIG con time_left = 20.
En p5.js, pulsar 'A' dos veces (tiempo sube a 22).
En p5.js, pulsar 'S' → micro:bit pasa a ARMED.
Esperar ~10 segundos (tiempo_left ~12).
Ingresar la secuencia 'A', 'B', 'A', 'S' (desarme correcto).
micro:bit vuelve a CONFIG con time_left = 20.
Desconectar en p5.js (Vector 1.2).
#### Vector 5.2: Flujo de explosión
Conectar p5.js → micro:bit en CONFIG.
Ajustar tiempo a 10 con pulsaciones 'B' (si fuera >10).
Pulsar 'S' → pasa a ARMED, time_left = 10.
No ingresar secuencia ni sacudir → esperar 10 segundos.
micro:bit explota (STATE_EXPLODED), muestra calavera y manda mensaje UART.
Pulsar 'S' → se resetea a CONFIG, time_left = 20.
