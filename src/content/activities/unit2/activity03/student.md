## Actividad 3

### Entradas:
#### Botón A (Pin 0):

 Descripción:
Un botón físico ubicado en el frente del micro:bit. Se utiliza para activar eventos o leer pulsaciones. Es una entrada digital, ya que detecta dos estados: presionado o no presionado.
#### Botón B (Pin 1):

Descripción: Otro botón físico similar al Botón A, ubicado también en el micro:bit. Se puede usar en combinación con el botón A o de manera independiente para controlar diferentes funciones en proyectos.
#### Pines Analógicos (Pin 2, 3, 4, etc.):

Descripción: Los pines analógicos permiten leer valores de voltaje de sensores que devuelven datos continuos, como potenciómetros o sensores de temperatura. El valor leído oscila entre 0 y 1023, lo que permite una lectura más precisa que una simple entrada digital.

#### Sensor de Temperatura (Interno):

Descripción: El micro:bit tiene un sensor de temperatura integrado que puede ser usado para medir la temperatura ambiente en grados Celsius. Su salida es analógica, y se puede leer utilizando las funciones de micro:bit.

### Salidas:
#### Pantalla de LED (5x5):

Descripción: El micro:bit tiene una matriz de 5x5 LEDs que se puede utilizar para mostrar números, letras, imágenes y patrones. Es una salida visual que permite mostrar información o feedback al usuario.

#### Pines de Salida Digital (Pin 0, 1, 2, etc.):

Descripción: Estos pines se pueden configurar para emitir una señal digital, es decir, un voltaje de 0 o 3.3V. Son útiles para encender y apagar LEDs externos, activar relés, motores, entre otros.

#### Salida Analógica (Pin 0, 1, 2, etc.):

Descripción: Los pines del micro:bit pueden emitir señales analógicas a través de una técnica llamada PWM (modulación por ancho de pulso). Esto permite, por ejemplo, controlar la intensidad de la luz en un LED o la velocidad de un motor.

#### Conexión Bluetooth (Radio):

Descripción: El micro:bit puede enviar y recibir datos de manera inalámbrica a través de Bluetooth. Puedes usar esta funcionalidad para enviar mensajes entre dispositivos o comunicarte con otros micro:bits, smartphones, etc. Es una salida de datos en forma de comunicación inalámbrica.
### Funciones de entradas en MicroPython:

#### 1 machine.Pin (Entrada Digital)
 Esta función se utiliza para leer valores de pines digitales.
Puedes configurar el pin como entrada y luego leer su estado (HIGH o LOW).

Ejemplo:

```python
Copiar
import machine

# Configuramos el pin 15 como entrada
pin_entrada = machine.Pin(15, machine.Pin.IN)

 # Leemos el estado del pin
estado = pin_entrada.value()

if estado == 1:
    print("El pin está en estado alto")
else:
    print("El pin está en estado bajo")
```

#### 2 machine.ADC (Entrada Analógica)
MicroPython permite leer señales analógicas mediante el convertidor analógico a digital (ADC). Puedes usar esta función para obtener valores entre 0 y 1023.

Ejemplo:

```python
Copiar
import machine

# Configuramos el pin 34 como entrada analógica
adc = machine.ADC(machine.Pin(34))

# Leemos el valor analógico (0 a 1023)
valor_analogico = adc.read()

print("Valor leído desde el pin analógico:", valor_analogico)

```
#### 3 machine.I2C (Lectura por bus I2C)
Esta función permite leer datos de sensores o dispositivos conectados a un bus I2C. Puedes configurarlo como maestro o esclavo.

Ejemplo:

```python
Copiar
import machine
import time

# Configuramos el bus I2C en los pines 21 (SDA) y 22 (SCL)
i2c = machine.I2C(0, scl=machine.Pin(22), sda=machine.Pin(21))

# Leemos los dispositivos conectados al bus I2C
dispositivos = i2c.scan()
print("Dispositivos en el bus I2C:", dispositivos)
```

### Funciones de salidas en MicroPython:

#### 1 machine.Pin (Salida Digital)
Similar a la entrada digital, pero configurada para enviar una señal de salida. Se utiliza para encender/apagar dispositivos como LEDs, relés, etc.

Ejemplo:

```python
Copiar
import machine
import time

# Configuramos el pin 16 como salida
pin_salida = machine.Pin(16, machine.Pin.OUT)

# Encendemos el pin (HIGH)
pin_salida.value(1)
time.sleep(1)

# Apagamos el pin (LOW)
pin_salida.value(0)
```

#### 2 machine.ADC (Entrada Analógica)
MicroPython permite leer señales analógicas mediante el convertidor analógico a digital (ADC). Puedes usar esta función para obtener valores entre 0 y 1023.

Ejemplo:

```python
Copiar
import machine

# Configuramos el pin 34 como entrada analógica
adc = machine.ADC(machine.Pin(34))

# Leemos el valor analógico (0 a 1023)
valor_analogico = adc.read()

print("Valor leído desde el pin analógico:", valor_analogico)
```
