## Descripción
This file doesn't look like much... just a bunch of 1s and 0s. But maybe it's not just random noise. Can you recover anything meaningful from this? Download the file here.

## Solución
- Al examinar el archivo `digits.bin`, observamos que contiene 71,744 caracteres de texto plano compuestos exclusivamente por dígitos binarios (`0` y `1`):
  ```bash
  head -c 64 digits.bin
  # 1111111111011000111111111110000000000000000100000100101001000110...
  ```
- Agrupando los primeros bits en bloques de 8 bits (1 byte) y convirtiéndolos a hexadecimal:
  - `11111111` $\rightarrow$ `0xFF`
  - `11011000` $\rightarrow$ `0xD8` (Firma SOI de JPEG: `FF D8`)
  - `11111111` $\rightarrow$ `0xFF`
  - `11100000` $\rightarrow$ `0xE0`
  - `00000000` $\rightarrow$ `0x00`
  - `00010000` $\rightarrow$ `0x10`
  - `01001010` $\rightarrow$ `0x4A` ('J')
  - `01000110` $\rightarrow$ `0x46` ('F')
  - `01001001` $\rightarrow$ `0x49` ('I')
  - `01000110` $\rightarrow$ `0x46` ('F')
- La secuencia binaria representa directamente los bytes de una imagen en formato JPEG/JFIF.
- Desarrollamos un script en Python para empaquetar los 71,744 bits en 8,968 bytes ($71744 / 8 = 8968$) y generar el archivo binario correspondiente:
  ```python
  with open("digits.bin", "r") as f:
      bitstring = "".join(f.read().split())

  byte_data = bytearray()
  for i in range(0, len(bitstring), 8):
      byte_data.append(int(bitstring[i : i + 8], 2))

  with open("digits.jpg", "wb") as f:
      f.write(byte_data)
  ```
- Al abrir la imagen reconstruida (`digits.jpg`), encontramos la bandera escrita en el centro de la imagen:
```text
academy{h1dd3n_1n_th3_b1n4ry_6d751a5b}
```

## Notas Adicionales
- Con frecuencia, los datos binarios son transportados o representados en formatos de texto (como cadenas ASCII de unos y ceros, Base64 o codificación hexadecimal) para atravesar medios que restringen la transmisión de bytes nulos o no imprimibles.
- Identificar los patrones de cabecera estándar (como los números mágicos `FF D8 FF E0` para JFIF) permite reconocer de inmediato el formato original al analizar cadenas binarias sin procesar.
- Herramientas utilizadas:
  - Script en Python 3 para la conversión bit a byte.
  - Visor de imágenes para examinar el resultado gráfico.

## Referencias
- [JPEG File Format and Markers](https://en.wikipedia.org/wiki/JPEG_File_Interchange_Format)
- [Python int(x, base=2) and bytearray](https://docs.python.org/3/library/functions.html#int)
