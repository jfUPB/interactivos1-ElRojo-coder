## Actividad 8 

### El código realiza las siguientes acciones:

Inicialización de los píxeles:

Se crean dos objetos Pixel con coordenadas específicas y un estado inicial de apagado (0).
Se les asigna un intervalo de tiempo para alternar su estado (1 segundo para el píxel 1 y 0.5 segundos para el píxel 2).
Actualizar el estado de los píxeles:

En cada ciclo del programa, se ejecuta el método update() para ambos píxeles.
El método update() verifica el tiempo transcurrido y cambia el estado del píxel entre apagado (0) y encendido (9) según el intervalo definido.
Control de tiempo:

Si el tiempo especificado por el intervalo ha transcurrido, el píxel cambia su estado y se actualiza en la pantalla del micro:bit.
Mostrar el nuevo estado:

Cuando el estado del píxel cambia, se actualiza visualmente en la pantalla utilizando display.set_pixel(), lo que cambia la intensidad del píxel en la pantalla.
### preguntas
#### 1.¿Puedes identificar algún estado?
Sí, los estados del programa son "Init" y "WaitTimeout". En "Init", el píxel se configura y luego se cambia a "WaitTimeout", donde espera que pase un intervalo de tiempo antes de cambiar su estado.

#### 2.¿Puedes identificar algún evento?
El evento en el código es el paso del tiempo. El programa está esperando a que transcurra el tiempo (utime.ticks_diff()) para cambiar el estado del píxel entre apagado y encendido.

#### 3.¿Puedes identificar alguna acción? 

Actualizar el estado del píxel (de apagado a encendido o viceversa).
Mostrar el píxel con el nuevo estado en la pantalla del micro:bit usando display.set_pixel().
