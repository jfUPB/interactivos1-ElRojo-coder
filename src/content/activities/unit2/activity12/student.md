## Actividad 12 

### Código 

``` ph
# Constantes
MIN_TIME = 10
MAX_TIME = 60
DESACTIVATION_CODE = ["A", "B", "A", "B", "A", "A", "A"]

# Variables
state = "config"
remaining_time = 20
current_code_index = 0

# Función para manejar el estado de la bomba
def handle_state():
  global state, remaining_time, current_code_index

  # Leer la entrada del usuario
  input = "" # Reemplazar con la lectura real del usuario (botones del Micro:bit)

  # Actualizar el estado de la bomba
  if state == "config":
    if "A" in input:
      remaining_time = min(remaining_time + 1, MAX_TIME)
    elif "B" in input:
      remaining_time = max(remaining_time - 1, MIN_TIME)
    elif "A" in input and "B" in input:
      state = "armed"
      remaining_time = remaining_time
      current_code_index = 0

  elif state == "armed":
    if remaining_time == 0:
      state = "exploded"
    elif "A" in input and "B" in input:
      if DESACTIVATION_CODE[current_code_index] == "A":
        current_code_index += 1
      else:
        current_code_index = 0

      if current_code_index == len(DESACTIVATION_CODE):
        state = "config"
        current_code_index = 0
    else:
      remaining_time -= 1
      # Mostrar el tiempo restante en la pantalla del Micro:bit
  

while True:
  # Leer la entrada del usuario
 

  # Actualizar el estado de la bomba
  handle_state()

  # Mostrar el tiempo restante en la pantalla del Micro:bit

```
