El programa comienza en un estado inicial y luego entra en un bucle definido por la variable while True. A partir de ahí, el programa transita entre los tres estados posibles, dentro de los intervalos de tiempo asignados. Funciona con un sistema de if que verifica si el intervalo ha sido cumplido. Si se presiona el botón A, el programa lo detecta y cambia al siguiente estado después de un intervalo de 2000 ms. Si no se presiona ningún botón durante el tiempo asignado, el programa no avanza al siguiente estado, repitiendo el ciclo en bucle. Básicamente, el programa tiene dos posibles salidas para cambiar de estado.

Práctica de vectores:

Los vectores de prueba tienen un estado inicial de "enojado", con otros tres estados posibles: "feliz", "triste" y "confundido". Funcionan de la siguiente manera:

Vector de prueba 1: De enojado a feliz y luego a triste

Estado inicial: El micro:bit comienza mostrando la cara enojada. Se presiona el botón A para cambiar a la cara feliz. Se presiona el botón B para cambiar a la cara triste. Si no se presiona ningún botón durante 2 segundos, el micro:bit vuelve a mostrar la cara enojada.
Vector de prueba 2: De enojado a confundido por sacudida

Estado inicial: El micro:bit comienza mostrando la cara enojada. Se detecta una sacudida, lo que hace que el micro:bit cambie a la cara confundida. Después de 2 segundos sin presionar ningún botón, el micro:bit vuelve a la cara enojada.
Vector de prueba 3: Ciclo completo (enojado → feliz → triste → confundido → enojado)

Estado inicial: El micro:bit comienza mostrando la cara enojada. Se presiona el botón A para cambiar a la cara feliz. Se presiona el botón B para cambiar a la cara triste. Se detecta una sacudida y el micro:bit cambia a la cara confundida. Después de 2 segundos, el micro:bit vuelve a mostrar la cara enojada.
