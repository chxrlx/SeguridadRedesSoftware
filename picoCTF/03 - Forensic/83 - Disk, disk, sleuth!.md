## Descripción
Use srch_strings from the sleuthkit and some terminal-fu to find a flag in this disk

## Solución
- El archivo proporcionado `dds1-alpine.flag.img.gz` es una imagen de disco comprimida con gzip.
- Siguiendo las instrucciones del reto, podemos descomprimir la imagen y utilizar la utilidad `srch_strings` de The Sleuth Kit (o directamente a través de una tubería con `zcat` sin descomprimir en disco) y filtrar por el formato de la bandera:
  ```bash
  # Opción 1: Descomprimir y buscar con srch_strings
  gzip -dk dds1-alpine.flag.img.gz
  srch_strings dds1-alpine.flag.img | grep -o "academy{[^}]*}"

  # Opción 2: En una sola línea mediante tubería directa con zcat
  zcat dds1-alpine.flag.img.gz | srch_strings | grep -o "academy{[^}]*}"
  ```
- Al ejecutar la búsqueda, se localiza la cadena correspondiente a la bandera:
```text
academy{f0r3ns1c4t0r_n30phyt3_6502313d}
```

## Notas Adicionales
- `srch_strings` es una utilidad forense incluida en The Sleuth Kit (TSK) diseñada para extraer secuencias de caracteres imprimibles de archivos de imagen de disco sin procesar.
- Herramientas utilizadas en Kali Linux:
  - `gzip` / `zcat`: Para descomprimir y transmitir el flujo de datos del archivo comprimido.
  - `srch_strings`: Herramienta de The Sleuth Kit para extracción de cadenas en imágenes de disco.
  - `grep`: Para filtrar y aislar el patrón de la bandera.

## Referencias
- [The Sleuth Kit (TSK) Documentation](https://www.sleuthkit.org/sleuthkit/docs.php)
- [srch_strings(1) - Linux man page](https://manpages.debian.org/testing/sleuthkit/srch_strings.1.en.html)
