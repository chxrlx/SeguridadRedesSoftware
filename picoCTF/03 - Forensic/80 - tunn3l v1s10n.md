## Descripción
We found this file. Recover the flag.

## Solución
- El archivo `tunn3l_v1s10n` carece de extensión y no es reconocido directamente por el comando `file`.
- Al examinar sus primeros bytes con `xxd`, encontramos la firma `BM` (`42 4D`), correspondiente al formato de imagen BMP (Bitmap), pero con varios campos alterados:
  1. **Offset de inicio de datos de píxeles (offset 0x0A)**: Aparece con el valor corrupto `0xba 0xd0` (`bad0`), debiendo ser `54` bytes (`0x36 0x00 0x00 0x00`), que corresponde a la cabecera BMP (14 bytes) + cabecera DIB (40 bytes).
  2. **Tamaño de la cabecera DIB (offset 0x0E)**: Aparece también como `0xba 0xd0`, debiendo ser `40` bytes (`0x28 0x00 0x00 0x00` para BITMAPINFOHEADER).
  3. **Altura de la imagen (offset 0x16)**: El archivo tiene una altura declarada de solo `306` píxeles (`0x32 0x01`), mostrando únicamente el mensaje trampa *"NOT THE FLAG"*. Al calcular la altura real a partir del tamaño del archivo (`2,893,454` bytes):
     - Bytes de cabecera: 54 bytes
     - Tamaño de datos: 2,893,400 bytes
     - Ancho: 1134 píxeles $\rightarrow$ alineado a múltiplo de 4 bytes = 3404 bytes por fila
     - Altura real: $2893400 / 3404 = 850$ píxeles (`0x0352` $\rightarrow$ little-endian `\x52\x03\x00\x00`).
- Aplicamos la corrección de la cabecera y dimensiones en Kali Linux mediante Python:
  ```bash
  python3 -c '
  import struct

  with open("tunn3l_v1s10n", "rb") as f:
      d = bytearray(f.read())

  # 1. Offset de datos de imagen (54 bytes)
  d[0x0A:0x0E] = struct.pack("<I", 54)

  # 2. Tamaño de cabecera DIB (40 bytes)
  d[0x0E:0x12] = struct.pack("<I", 40)

  # 3. Altura real completa (850 píxeles)
  d[0x16:0x1A] = struct.pack("<I", 850)

  with open("tunn3l_v1s10n_fixed.bmp", "wb") as f:
      f.write(d)
  '
  ```
- Al abrir la imagen reparada `tunn3l_v1s10n_fixed.bmp`, en la parte superior se observa el texto con la bandera completa:
```text
academy{qu1t3_a_v13w_2020}
```

## Notas Adicionales
- El nombre del reto (*"tunn3l v1s10n"*) hace alusión al recorte artificial de la altura de la imagen en la cabecera, impidiendo ver la parte superior donde reside la bandera.
- Herramientas utilizadas en Kali Linux:
  - `xxd`: Para la inspección visual de las cabeceras y offsets en hexadecimal.
  - `python3` (módulo `struct`): Para desempaquetar y reescribir los valores binarios en formato little-endian.

## Referencias
- [BMP File Format Specification](https://en.wikipedia.org/wiki/BMP_file_format)
- [xxd(1) - Linux man page](https://man7.org/linux/man-pages/man1/xxd.1.html)
