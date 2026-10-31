## Descripción
This file seems broken... or is it? Maybe a couple of bytes could make all the difference. Can you figure out how to bring it back to life? Download the file here.

## Solución
- Al analizar los primeros bytes del archivo `file` con un visor hexadecimal (`xxd` / `hexdump`):
  ```bash
  head -c 32 file | xxd
  ```
- Observamos la siguiente cabecera:
  ```text
  00000000: 5c78 ffe0 0010 4a46 4946 0001 0100 0001  \x....JFIF......
  ```
- Los bytes `4A 46 49 46` corresponden a la firma del estándar JFIF (JPEG File Interchange Format), precedido por el marcador de segmento `FF E0 00 10`.
- Sin embargo, los dos primeros bytes del archivo (el marcador SOI o *Start of Image*) fueron alterados a `5C 78` (que en ASCII representa los caracteres `\x`) en lugar del número mágico estándar de JPEG: `FF D8`.
- Verificamos además que el final del archivo conserva íntegro el marcador EOI (*End of Image*), correspondiente a los bytes `FF D9`:
  ```bash
  tail -c 4 file | xxd
  # 00000000: a00f ffd9                                ...
  ```
- Restauramos los dos primeros bytes dañados reemplazando `5C 78` por `FF D8` mediante Python o `hexedit`:
  ```python
  with open("file", "rb") as f:
      data = bytearray(f.read())

  # Restaurar marcador SOI de JPEG
  data[0:2] = b"\xff\xd8"

  with open("recovered.jpg", "wb") as f:
      f.write(data)
  ```
  O usando `printf` y `dd` en bash:
  ```bash
  printf '\xff\xd8' | dd of=file conv=notrunc bs=1 count=2
  ```
- Al abrir la imagen reconstruida (`recovered.jpg`), se observa la bandera en el centro de la imagen:
```text
academy{r3st0r1ng_th3_by73s_a6f66bd7}
```

## Notas Adicionales
- La reparación de cabeceras binarias (Magic Bytes / File Signatures) es una técnica fundamental en análisis forense digital cuando un atacante corrompe intencionalmente los primeros bytes para evadir el análisis automatizado de herramientas como `file`.
- Para el formato JPEG/JFIF, la especificación exige que todo archivo comience con `FF D8` (SOI) y finalice con `FF D9` (EOI).
- Herramientas utilizadas:
  - `xxd` / `hexdump`: Inspección hexadecimal del archivo.
  - Python 3 / `dd`: Modificación y restauración precisa de bytes.
  - Visor de imágenes para visualizar la imagen recuperada.

## Referencias
- [JPEG File Layout and Markers - W3C](https://www.w3.org/Graphics/JPEG/jfif3.pdf)
- [List of file signatures - Wikipedia](https://en.wikipedia.org/wiki/List_of_file_signatures)
