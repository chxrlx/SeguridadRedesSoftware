## Descripción
How about some hide and seek?

## Solución
- Descomprimimos el archivo `unknown.zip` para extraer la imagen contenida:
  ```bash
  unzip unknown.zip
  ```
  Se extrae el archivo `ukn_reality.jpg`.
- Analizamos los metadatos de la imagen utilizando `exiftool`:
  ```bash
  exiftool ukn_reality.jpg
  ```
- En el campo `Attribution URL`, encontramos una cadena codificada en formato Base64:
  ```text
  Attribution URL                 : YWNhZGVteXtNRTc0RDQ3QV9ISUREM05fYTgxZDk0MzF9Cg==
  ```
- Decodificamos la cadena Base64 mediante la terminal:
  ```bash
  echo "YWNhZGVteXtNRTc0RDQ3QV9ISUREM05fYTgxZDk0MzF9Cg==" | base64 -d
  ```
- También es posible extraer y decodificar el valor en una sola línea de comandos:
  ```bash
  exiftool -s3 -AttributionURL ukn_reality.jpg | base64 -d
  ```
- Obtenemos la bandera:
```text
academy{ME74D47A_HIDD3N_a81d9431}
```

## Notas Adicionales
- Los metadatos de imágenes (como EXIF y XMP) permiten almacenar atributos como derechos de autor, licencias o URLs de atribución. Estos campos son de uso frecuente en retos de esteganografía y análisis forense para ocultar mensajes codificados sin modificar los datos de los píxeles.
- Herramientas utilizadas en Kali Linux:
  - `unzip`: Para extraer el archivo comprimido ZIP.
  - `exiftool`: Para la lectura e inspección de los metadatos XMP del archivo JPEG.
  - `base64`: Para la decodificación del texto en Base64.

## Referencias
- [ExifTool by Phil Harvey](https://exiftool.org/)
- [exiftool(1) - Linux man page](https://manpages.debian.org/testing/libimage-exiftool-perl/exiftool.1p.en.html)
- [Base64 Decoding - Linux Manual](https://manpages.debian.org/testing/coreutils/base64.1.en.html)
