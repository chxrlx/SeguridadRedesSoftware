## Descripción
Every file gets a flag. The SOC analyst saw one image been sent back and forth between two people. They decided to investigate and found out that there was more than what meets the eye here.

## Solución
- Analizamos el archivo `flag.png` con `exiftool` o `binwalk` para verificar si contiene datos adicionales concatenados tras el delimitador final de la imagen (`IEND`):
  ```bash
  binwalk flag.png
  ```
  Salida:
  ```text
  DECIMAL       HEXADECIMAL     DESCRIPTION
  --------------------------------------------------------------------------------
  0             0x0             PNG image, 700 x 450, 8-bit/color RGB, non-interlaced
  75            0x4B            Zlib compressed data, default compression
  15929         0x3E39          Zip archive data, at least v1.0 to extract, name: secret/
  15994         0x3E7A          Zip archive data, at least v1.0 to extract, compressed size: 5349, uncompressed size: 5349, name: secret/flag.png
  21578         0x544A          End of Zip archive, footer length: 22
  ```
- Observamos que en el offset `15929` (`0x3E39`) se encuentra un archivo ZIP incrustado que contiene el directorio `secret/` y el archivo `secret/flag.png`.
- Extraemos el archivo ZIP embebido (podemos utilizar `binwalk -e` o directamente `unzip`):
  ```bash
  unzip flag.png
  ```
  O alternativamente extraer únicamente la imagen oculta:
  ```bash
  unzip -p flag.png secret/flag.png > secret_flag.png
  ```
- Al abrir o inspeccionar las cadenas de la imagen extraída `secret/flag.png`:
  ```bash
  strings secret/flag.png | grep -o "academy{[^}]*}"
  ```
- Se obtiene la bandera:
```text
academy{Hiddinng_An_imag3_within_@n_ima9e_c55a7afe}
```

## Notas Adicionales
- La técnica de concatenar un archivo ZIP al final de una imagen PNG (después del bloque `IEND`) es una forma común de esteganografía y políglotas de archivos, ya que los visualizadores de imágenes leen hasta el bloque `IEND` ignorando los bytes sobrantes, mientras que las herramientas de descompresión como `unzip` escanean el archivo buscando las firmas de cabecera ZIP (`PK\x03\x04`).
- Herramientas utilizadas en Kali Linux:
  - `binwalk`: Análisis y detección de firmas de archivos incrustados.
  - `unzip`: Extracción de archivos comprimidos en formato ZIP.
  - `strings` / `grep`: Extracción y filtrado de la cadena de texto de la bandera.

## Referencias
- [PNG (Portable Network Graphics) Specification - Chunk Specifications](https://www.w3.org/TR/png/#11IEND)
- [Binwalk Documentation](https://github.com/ReFirmLabs/binwalk)
- [unzip(1) - Linux man page](https://manpages.debian.org/testing/unzip/unzip.1.en.html)
