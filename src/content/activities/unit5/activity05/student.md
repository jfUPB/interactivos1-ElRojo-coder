
![image](https://github.com/user-attachments/assets/cdaf441a-7d18-47ef-95bb-6dec708c1872)


### ¿Por qué fue necesario introducir framing en el protocolo binario?
El framing en el protocolo binario cumple una función similar a la del carácter \n en el protocolo ASCII: separa los datos en paquetes identificables, indicando claramente dónde comienza y termina un mensaje. Esto es fundamental, ya que en la comunicación binaria los datos se transmiten como una secuencia continua de bytes, sin separadores visibles.
Sin framing, el receptor no podría distinguir con certeza cuándo empieza o termina un paquete de datos, lo que causaría errores en la interpretación.

### ¿Cómo funciona el framing?
#### El framing se implementa utilizando:

Un carácter de sincronización (por ejemplo, 0xaa en hexadecimal) que señala el inicio de un paquete de datos. Es como una “alarma” que indica que se va a recibir información importante.

Checksum: un valor que permite verificar la integridad del mensaje. Sirve para detectar errores comparando un valor calculado a partir de los datos recibidos con el valor enviado por el micro:bit.

#### En la función readSerialData() del programa en p5.js:
### ¿Qué hace la función concat y por qué se utiliza?
La función concat() une dos arreglos: el serialBuffer (que contiene datos previamente recibidos) y newData (los nuevos bytes que acaban de llegar). Esto se hace porque a veces los paquetes no llegan completos en una sola lectura. concat permite acumular los datos hasta que se tenga un paquete completo sin perder información.

### ¿Por qué el bucle recorre el buffer solo si tiene 8 o más bytes?
Porque el protocolo definido requiere que cada paquete contenga exactamente 8 bytes. El programa espera a tener al menos esa cantidad antes de intentar procesarlo, asegurándose así de tener todos los datos necesarios.

### ¿Qué significa 0xaa en el código?
0xaa es una constante en hexadecimal que representa el número 170 en decimal. Se utiliza como byte de sincronización, marcando el inicio de un paquete de datos binario.

### ¿Qué hacen las funciones shift y continue, y por qué se usan?

shift() elimina el primer byte del buffer. Se utiliza para descartar datos hasta encontrar el byte de sincronización (0xaa) que indica el comienzo correcto del mensaje.

continue hace que el programa salte al siguiente ciclo del bucle, ignorando el resto de las instrucciones actuales. Se usa para que el programa siga revisando el buffer hasta encontrar un paquete válido.

### ¿Qué hace break en este contexto?
break se utiliza para salir del bucle una vez que se ha identificado y procesado correctamente un paquete de datos. Luego, el programa espera a que lleguen más bytes para continuar.

### ¿Para qué sirven slice y splice?

slice() crea una copia parcial del arreglo, sin modificar el original. Se usa para extraer algunos datos específicos (por ejemplo, los bytes útiles del paquete).

splice() modifica directamente el arreglo original, eliminando o reemplazando elementos. Es útil para limpiar el buffer una vez procesado un paquete completo.

### ¿Cómo opera la función reduce?
La función reduce() es una técnica de programación funcional en JavaScript que acumula los valores de un arreglo en un solo resultado.
En este caso, se utiliza para calcular el checksum, sumando todos los valores del arreglo dataBytes (los bytes de datos del paquete). Esta suma se compara luego con el checksum recibido para verificar si el paquete es válido.

### ¿Por qué se compara el checksum calculado con el recibido?
La comparación sirve como verificación de integridad. Si ambos valores coinciden, se asume que los datos fueron recibidos correctamente.
Si no coinciden, significa que hubo un error durante la transmisión, y se descarta el paquete. Esta verificación evita trabajar con datos corruptos.

### ¿Qué es un DataView y para qué se utiliza?
DataView es una interfaz de JavaScript que permite leer y escribir datos en un ArrayBuffer utilizando diferentes tipos de datos (como enteros, flotantes, etc.).
Es especialmente útil cuando se trabaja con datos binarios, ya que permite interpretar secuencias de bytes como valores significativos.

### ¿Por qué es necesario hacer conversiones y no usar directamente los datos del buffer?
Porque los datos binarios en el buffer están en su forma cruda (sin formato). Para que el programa pueda entender y usar esos datos (por ejemplo, como coordenadas o estados de botones), es necesario convertirlos a tipos de datos legibles y útiles, como enteros o booleanos.
Sin estas conversiones, los datos no tendrían sentido y no podrían ser usados correctamente por el programa.

