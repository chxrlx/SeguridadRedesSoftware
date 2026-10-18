## Descripción
Files can always be changed in a secret way. Can you find the flag? cat.jpg

## Solución
- Analizamos los metadatos de la imagen `cat.jpg` utilizando la herramienta `exiftool`:
  ```bash
  exiftool cat.jpg
  ```
- En el campo `License` de los metadatos, encontramos una cadena de texto codificada en Base64:
  ```text
  License                         : YWNhZGVteXt0aGVfbTN0YWRhdGFfMXNfbW9kaWZpZWR9
  ```
- Decodificamos la cadena Base64 mediante la terminal:
  ```bash
  echo "YWNhZGVteXt0aGVfbTN0YWRhdGFfMXNfbW9kaWZpZWR9" | base64 -d
  ```
- También es posible extraer y decodificar el valor directamente en una sola línea de comandos:
  ```bash
  exiftool -s3 -License cat.jpg | base64 -d
  ```
- Obtenemos la bandera:
```text
academy{the_m3tadata_1s_modified}
```

## Notas Adicionales
- Los metadatos de un archivo (como EXIF, IPTC o XMP) almacenan información sobre la creación, autoría, derechos de autor o configuración técnica del medio. A menudo se utilizan en desafíos forenses y de esteganografía para ocultar mensajes codificados sin alterar visualmente la imagen.
- Herramientas utilizadas en Kali Linux:
  - `exiftool`: Extracción e inspección de metadatos EXIF/XMP.
  - `base64`: Decodificación de cadenas en formato Base64.

## Referencias
- [ExifTool by Phil Harvey](https://exiftool.org/)
- [exiftool(1) - Linux man page](https://manpages.debian.org/testing/libimage-exiftool-perl/exiftool.1p.en.html)
- [Base64 Decoding - Linux Manual](https://manpages.debian.org/testing/coreutils/base64.1.en.html)
