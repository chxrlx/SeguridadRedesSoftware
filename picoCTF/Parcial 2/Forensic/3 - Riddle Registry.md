## Descripción
Hi, intrepid investigator! 📄🔍 You've stumbled upon a peculiar PDF filled with what seems like nothing more than garbled nonsense. But beware! Not everything is as it appears. Amidst the chaos lies a hidden treasure—an elusive flag waiting to be uncovered. Find the PDF file here Hidden Confidential Document and uncover the flag within the metadata.

## Solución
- Siguiendo la indicación de la descripción sobre buscar en los metadatos del documento, inspeccionamos el archivo `confidential.pdf` utilizando la herramienta `exiftool`:
  ```bash
  exiftool confidential.pdf
  ```
- Entre los campos de metadatos del PDF, el campo `Author` contiene una cadena codificada en Base64:
  ```text
  Producer                        : PyPDF2
  Author                          : YWNhZGVteXtwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV8zY2M3Y2VhOX0=
  ```
- Decodificamos el valor del campo `Author` con el comando `base64`:
  ```bash
  echo "YWNhZGVteXtwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV8zY2M3Y2VhOX0=" | base64 -d
  ```
- También es posible extraerlo y decodificarlo directamente en un solo paso:
  ```bash
  exiftool -s3 -Author confidential.pdf | base64 -d
  ```
- Obteniendo la bandera:
```text
academy{puzzl3d_m3tadata_f0und!_3cc7cea9}
```

## Notas Adicionales
- Los archivos PDF incorporan estructuras de metadatos estandarizadas (como el diccionario `Info` y flujos XMP) que permiten registrar autoría, títulos, creador y fechas. Frecuentemente en retos de esteganografía y análisis forense, los atacantes o creadores del reto incrustan información confidencial u ofuscada en estos campos.
- Herramientas utilizadas:
  - `exiftool`: Herramienta para lectura y escritura de metadatos en múltiples formatos de archivo.
  - `base64`: Utilidad estándar para codificación y decodificación en Base64.

## Referencias
- [ExifTool by Phil Harvey](https://exiftool.org/)
- [Adobe PDF Reference - Document Information Dictionary](https://opensource.adobe.com/dc-acrobat-sdk-docs/pdfstandards/PDF32000_2008.pdf)
