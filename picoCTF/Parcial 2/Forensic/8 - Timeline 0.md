## Descripción
Can you find the flag in this disk image? Wrap what you find in the academy{} flag format. Download the disk image here.

## Solución
- Descomprimimos la imagen de la partición comprimida con `gzip`:
  ```bash
  gzip -dc partition4.img.gz > partition4.img
  ```
- Identificamos el sistema de archivos con el comando `file`:
  ```bash
  file partition4.img
  # partition4.img: Linux rev 1.0 ext4 filesystem data, UUID=7a00e9da-98f8-4f0f-b257-95edf422d902 ...
  ```
- Dado el nombre del reto (*Timeline*), generamos una línea de tiempo forense de todos los archivos y metadatos temporales (MACB: Modified, Accessed, Changed, Birth) utilizando las herramientas del conjunto The Sleuth Kit (`fls` y `mactime`):
  ```bash
  fls -r -m / partition4.img > body.txt
  mactime -b body.txt > timeline.txt
  ```
- Al analizar la línea de tiempo cronológica, resalta un archivo anómalo cuyo timestamp fue manipulado al año 1985 (técnica conocida como *timestomping*), en contraste con el resto del sistema de archivos correspondiente a 2024–2025:
  ```text
  0|/bin/bcab|4945|r/rrw-r--r--|0|0|41|473446800|473446800|473446800|473446800
  ```
- El inodo 4945 corresponde al archivo `/bin/bcab` con fecha `1985-01-01 12:00:00 (EST)`.
- Extraemos el contenido del inodo 4945 directamente mediante la utilidad `icat`:
  ```bash
  icat partition4.img 4945
  ```
  Obteniendo la cadena en Base64:
  ```text
  NzFtMzExbjNfMHU3MTEzcl9oM3JfNDNhMmU3YWYK
  ```
- Decodificamos el texto en Base64:
  ```bash
  echo "NzFtMzExbjNfMHU3MTEzcl9oM3JfNDNhMmU3YWYK" | base64 -d
  # 71m311n3_0u7113r_h3r_43a2e7af
  ```
- Envolviendo el resultado en el formato `academy{}` requerido:
```text
academy{71m311n3_0u7113r_h3r_43a2e7af}
```

## Notas Adicionales
- El análisis de línea de tiempo (*timeline analysis*) es una de las técnicas analíticas más potentes en informática forense, ya que permite detectar discrepancias temporales, artefactos fuera de secuencia temporal (*timestomp outliers*) y reconstruir con exactitud las acciones realizadas en un sistema.
- En este caso, el atacante intentó camuflar el archivo en `/bin` alterando sus sellos de tiempo hacia una época muy distante (`1985-01-01`), pero esto genera una firma temporal destacada en el análisis cronológico ordenado.
- Herramientas utilizadas:
  - `gzip`: Descompresión de la imagen de disco.
  - `fls`, `mactime`, `icat` (The Sleuth Kit): Extracción de metadatos del sistema de archivos ext4, generación de la línea de tiempo y extracción de inodos.
  - `base64`: Decodificación de la carga útil.

## Referencias
- [The Sleuth Kit (TSK) Documentation](https://wiki.sleuthkit.org/index.php?title=TSK_Tool_Overview)
- [SANS Digital Forensics: Timeline Analysis](https://www.sans.org/white-papers/34290/)
