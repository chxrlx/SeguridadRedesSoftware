## Descripción
We found this file. Recover the flag.

## Solución
- El archivo `c0rrupt-mystery` se encuentra dañado y no es reconocido directamente como imagen por el sistema (`file c0rrupt-mystery` indica `data`).
- Al examinar la estructura binaria y cabeceras con `xxd`, se observa que corresponde a una imagen en formato PNG con múltiples corrupciones en sus cabeceras y chunks:
  1. **Firma mágica PNG (offset 0x00 - 0x07)**: Los primeros 8 bytes están alterados (`89 65 4e 34 0d 0a b0 aa`), debiendo ser la cabecera estándar `89 50 4E 47 0D 0A 1A 0A`.
  2. **Tipo de chunk IHDR (offset 0x0C - 0x0F)**: El nombre del chunk inicial aparece como `C"DR` (`43 22 44 52`), el cual debe corregirse a `IHDR` (`49 48 44 52`).
  3. **Chunk pHYs (offset 0x46)**: El primer byte de datos del chunk tiene el valor corrupto `0xaa`, debiendo ser `0x00` para que su checksum CRC coincida.
  4. **Longitud del primer chunk IDAT (offset 0x53 - 0x56)**: La longitud aparece como `aa aa ff a5`, debiendo ser `00 00 ff a5` (`65445` bytes).
  5. **Tipo del primer chunk IDAT (offset 0x57 - 0x5A)**: El tipo del chunk aparece como `\xabDET` (`ab 44 45 54`), debiendo ser `IDAT` (`49 44 41 54`).
- Aplicamos la corrección de estos bytes en Kali Linux mediante un script en Python:
  ```bash
  python3 -c '
  import struct

  with open("c0rrupt-mystery", "rb") as f:
      d = bytearray(f.read())

  # 1. Cabecera PNG
  d[0:8] = b"\x89PNG\r\n\x1a\n"

  # 2. Nombre del chunk IHDR
  d[12:16] = b"IHDR"

  # 3. Datos del chunk pHYs
  d[0x46] = 0x00

  # 4. Longitud del primer chunk IDAT (0xffa5)
  d[0x53:0x57] = struct.pack(">I", 0xffa5)

  # 5. Nombre del primer chunk IDAT
  d[0x57:0x5b] = b"IDAT"

  with open("c0rrupt_fixed.png", "wb") as f:
      f.write(d)
  '
  ```
- Al abrir la imagen reparada `c0rrupt_fixed.png`, se puede leer la bandera:
```text
academy{c0rrupt10n_1847995}
```

## Notas Adicionales
- La especificación PNG requiere que cada chunk cuente con un campo de longitud (4 bytes), tipo de chunk (4 bytes), datos del chunk y un código de redundancia cíclica (CRC32 de 4 bytes).
- Herramientas utilizadas en Kali Linux:
  - `xxd`: Para la inspección visual de las cabeceras y offsets en hexadecimal.
  - `python3`: Para aplicar los parches binarios exactos en los bytes alterados.
  - `pngcheck`: Utilidad para diagnosticar errores estructurales y validar la integridad de chunks PNG.

## Referencias
- [PNG (Portable Network Graphics) Specification - W3C](https://www.w3.org/TR/PNG/)
- [pngcheck documentation](http://www.libpng.org/pub/png/apps/pngcheck.html)
- [xxd(1) - Linux man page](https://man7.org/linux/man-pages/man1/xxd.1.html)