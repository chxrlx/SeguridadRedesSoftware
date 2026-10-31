## Descripción
Attackers have hidden information in a very large mass of data in the past, maybe they are still doing it. Download the data here.

## Solución
- El archivo proporcionado `anthem.flag.txt` contiene una gran cantidad de texto (el texto de la novela *Anthem* de Ayn Rand).
- Para buscar la bandera oculta dentro del texto sin necesidad de inspeccionarlo manualmente, utilizamos la herramienta `grep` filtrando por el patrón común de las banderas `academy{`:
  ```bash
  grep -n -i "academy{" anthem.flag.txt
  ```
- El comando localiza la bandera en la línea 933 del archivo:
  ```text
  933:      we think that the men of academy{gr3p_15_@w3s0m3_375ef577}
  ```
- Extrayendo la cadena correspondiente:
```text
academy{gr3p_15_@w3s0m3_375ef577}
```

## Notas Adicionales
- La herramienta `grep` (Global Regular Expression Print) permite realizar búsquedas de texto y patrones basados en expresiones regulares en archivos de gran tamaño con alta eficiencia y velocidad.
- Herramientas utilizadas:
  - `grep` (búsqueda con coincidencias exactas e insensibles a mayúsculas/minúsculas).

## Referencias
- [grep(1) Linux manual page](https://man7.org/linux/man-pages/man1/grep.1.html)
