## Vectores de prueba 
#### 1. Transición de Inicio a Configuración
Estado actual: Inicio
Evento: (Automático o “Comenzar” — según tu implementación)
Acciones esperadas: Se inicializa el sistema, se fija un tiempo por defecto (por ejemplo, 30s) y se muestra en pantalla.
Estado siguiente: Estado de Configuración

#### 2. Ajuste de tiempo en Configuración (Aumentar tiempo con A)
Estado actual: Estado de Configuración
Evento: A (pulsación para aumentar)
Acciones esperadas:
Incrementar el tiempo en +15s.
Si el tiempo resultante excede 60s, se debe limitar a 60s (no sobrepasar el máximo).
Estado siguiente: Estado de Configuración

#### 3. Ajuste de tiempo en Configuración (Disminuir tiempo con B)
Estado actual: Estado de Configuración
Evento: B (pulsación para disminuir)
Acciones esperadas:
Disminuir el tiempo en -15s.
Si el tiempo resultante es menor a 10s, se debe limitar a 10s (no bajar del mínimo).
Estado siguiente: Estado de Configuración

#### 4. Permanecer en Configuración (varias pulsaciones A/B)
Estado actual: Estado de Configuración
Evento: A repetido varias veces (p.ej. 3 veces seguidas)
Acciones esperadas:
Cada pulsación de A aumenta el tiempo +15s hasta llegar a 60s (máximo).
El sistema debe seguir mostrando el tiempo actualizado.
Estado siguiente: Estado de Configuración
(Análogo con pulsaciones B repetidas para verificar la disminución correcta y el límite de 10s.)

#### 5. Iniciar cuenta regresiva (Shake / T (logo Touch))
Estado actual: Estado de Configuración
Evento: Shake o T (logo Touch) (según tu diagrama, indica que se inicia la cuenta regresiva)
Acciones esperadas:
Comenzar la cuenta regresiva con el tiempo configurado.
Estado siguiente: Estado Armado
#### 6. Iniciar secuencia de desarme en Configuración (opcional, si aplica)
(Dependiendo de la implementación, es posible que la “Secuencia de desarme” solo tenga sentido en el Estado Armado. Si tu diagrama permite que se ingrese la secuencia [A, B, A, S] desde Configuración para “desarmar” antes de armar, entonces:)

Estado actual: Estado de Configuración
Evento: Ingresar la secuencia [A, B, A, S]
Acciones esperadas:
El sistema reconoce la secuencia completa.
“Desarma” la bomba (posiblemente se ignore o se quede en un estado seguro, dependiendo de tu lógica).
Estado siguiente: Podría permanecer en Estado de Configuración o ir a un estado “Desarmado” (si existe), según tu diseño.
#### 7. De Configuración a Armado y luego a Explotado (sin sacudir de nuevo)
Estado actual: Estado de Configuración
Evento: Shake → pasa a Estado Armado.
Acción: Inicia la cuenta regresiva (ej. 30s configurados).
Estado siguiente: Estado Armado
Luego, en Estado Armado, se dejan pasar los segundos hasta que el tiempo llegue a 0 sin realizar ningún otro evento.
Acciones esperadas: Cada segundo, el sistema decrementa el tiempo. Al llegar a 0 → Explota.
Estado siguiente: Estado Explotado
(Este vector de prueba cubre la ruta “normal” de armar la bomba y dejar que explote sin intervención.)

#### 8. Reinicio del sistema (Shake durante la cuenta regresiva)
Estado actual: Estado Armado (cuenta regresiva activa)
Evento: Shake (de nuevo, durante la cuenta regresiva)
Acciones esperadas:
El diagrama indica que un nuevo “Shake” reinicia el sistema (regresa a Estado de Configuración o a Inicio, según tu diseño exacto).
Estado siguiente: Estado de Configuración (o Inicio, si así lo definiste)
#### 9. Secuencia de desarme parcial y luego Shake
Estado actual: Estado Armado
Evento: El usuario intenta la secuencia parcial [A, B] (pero no la completa con [A, S]), y luego hay un Shake.
Acciones esperadas:
Se ignora la secuencia incompleta (no pasa nada especial).
El Shake puede reiniciar el sistema o no, dependiendo de la lógica (según el diagrama, “Shake” durante Armado → reinicia).
Estado siguiente: Estado de Configuración (si se reinicia) o permanece en Estado Armado (si tu lógica decide que no hay reinicio tras un Shake en medio de la secuencia).
(El diagrama parece indicar que sí se reinicia.)
#### 10. Secuencia de desarme completa en Estado Armado (desactivar la bomba)
Estado actual: Estado Armado
Evento: Ingresar la secuencia [A, B, A, S] completa mientras la cuenta regresiva está activa.
Acciones esperadas:
El sistema reconoce la secuencia como “Desarme”.
Se detiene la cuenta regresiva y la bomba queda desactivada.
Estado siguiente: (Posiblemente un estado “Desarmado” o regresa a “Configuración” o “Inicio”, según tu diseño.)
#### 11. Explosión (Tiempo = 0)
Estado actual: Estado Armado
Evento: El tiempo llega a 0 (no se ha sacudido de nuevo ni se ha ingresado la secuencia de desarme).
Acciones esperadas:
Se dispara la explosión: mostrar calavera, reproducir música, etc.
Estado siguiente: Estado Explotado
(Esta prueba verifica específicamente la transición por “Tiempo = 0 / Explota”.)

#### 12. Repetir secuencia de desarme tras haber fallado una vez
Estado actual: Estado Armado
Evento: El usuario intenta una secuencia errónea (por ejemplo, [A, S, A]) y luego intenta [A, B, A, S].
Acciones esperadas:
La primera secuencia se ignora (no es correcta).
La segunda secuencia, si se ingresa correctamente antes de llegar a 0, desarma la bomba.
Estado siguiente: Desarmado (o Estado de Configuración/Inicio, según tu diseño)
#### 13. Asegurar límites de tiempo (máximo 60s)
Estado actual: Estado de Configuración
Evento: Presionar A repetidas veces hasta intentar superar 60s.
Acciones esperadas:
El sistema nunca debe mostrar un valor mayor a 60s.
Estado siguiente: Permanece en Estado de Configuración
#### 14. Asegurar límites de tiempo (mínimo 10s)
Estado actual: Estado de Configuración
Evento: Presionar B repetidas veces hasta intentar bajar de 10s.
Acciones esperadas:
El sistema no debe bajar de 10s.
Estado siguiente: Estado de Configuración
#### 15. Verificar que en “Estado Explotado” no se acepten más acciones
Estado actual: Estado Explotado
Evento: Cualquier entrada (A, B, Shake, secuencia de desarme, etc.)
Acciones esperadas:
Ignorar todas las entradas (o mostrar algún mensaje de que la bomba ya explotó).
Estado siguiente: Permanece en Estado Explotado
