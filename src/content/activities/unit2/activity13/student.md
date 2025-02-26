## Actividad 13 

### Posibles mejoras:
#### Manejo de errores:

Actualmente, el sistema es muy básico. Si un usuario presiona repetidamente los botones, puede llegar a estados no deseados, como una bomba que no explota nunca o un contador que se incrementa o decrementa demasiado rápido.
Mejora: Se podrían agregar límites más estrictos sobre el comportamiento de los botones, o incluso un sistema de confirmación que asegure que las acciones del usuario son intencionadas y no accidentales.
Confirmación de activación de la bomba:

El sistema solo permite incrementar o decrementar el tiempo en pasos de 1 segundo, lo cual podría no ser ideal para situaciones donde se requiere un ajuste más preciso.
Mejora: Implementar un sistema que permita al usuario seleccionar intervalos mayores (por ejemplo, cambiar el tiempo en incrementos de 5 o 10 segundos) para facilitar la configuración, especialmente si se trabaja con un tiempo límite más largo.

Aunque se muestra el tiempo restante en la pantalla y se emite una señal sonora al explotar, no hay indicaciones claras de lo que está ocurriendo entre los cambios de estado.
Mejora: Añadir más retroalimentación visual en el estado ARMED para indicar que la bomba está armada, tal vez con una imagen o un color que varíe. También podría agregarse una señal sonora que indique que la bomba está armada.
Estados adicionales o transiciones:


