## Descripción
Matryoshka dolls are a set of traditional Russian wooden dolls of decreasing size placed one inside another. Was it snowman? Or woman?
## Solución
- El archivo `dolls.jpg` implementa la metáfora de las muñecas rusas (*matrioshkas*), albergando múltiples capas de archivos de imagen y archivos ZIP anidados de forma recursiva en su interior:
  1. `dolls.jpg` contiene un archivo ZIP con `base_images/2_c.jpg`.
  2. `2_c.jpg` contiene un archivo ZIP con `base_images/3_c.jpg`.
  3. `3_c.jpg` contiene un archivo ZIP con `base_images/4_c.jpg`.
  4. `4_c.jpg` contiene un archivo ZIP con `flag.txt`.
- Para extraer todas las capas de manera recursiva en Kali Linux, podemos usar `binwalk` o un script en Python:
  ```bash
  # Opción 1: Extracción recursiva automática con binwalk
  binwalk -e -M dolls.jpg

  # Opción 2: Extracción directa en memoria con Python
  python3 -c '
  import zipfile, io

  def unpack(data):
      pk = data.find(b"PK\x03\x04")
      if pk != -1:
          z = zipfile.ZipFile(io.BytesIO(data[pk:]))
          for name in z.namelist():
              content = z.read(name)
              if "flag.txt" in name:
                  print(content.decode())
                  return
              unpack(content)

  with open("dolls.jpg", "rb") as f:
      unpack(f.read())
  '
  ```
- Al descomprimir la capa final dentro de `4_c.jpg`, se obtiene el archivo `flag.txt` con la bandera:
```text
academy{ZNTyvSDXRNO1d0xCkBRMhAoiafpCTvgW}
```
## Notas Adicionales
- La técnica de embeber archivos comprimidos ZIP tras la cabecera o datos de una imagen es habitual en retos forenses de esteganografía.
- Herramientas utilizadas en Kali Linux:
  - `binwalk`: Analizador de firmas y extractor forense de contenido binario embebido.
  - `python3` (módulo `zipfile`): Para procesar y desempaquetar la secuencia de archivos comprimidos.
## Referencias
- [binwalk Documentation](https://github.com/ReFirmLabs/binwalk)
- [ZIP File Format Specification - PKWARE](https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT)
