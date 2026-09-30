## Descripción
I stopped using YellowPages and moved onto WhitePages... but the page they gave me is all blank!

## Solución
- Al abrir e inspeccionar el archivo `whitepages.txt`, aparentemente se encuentra completamente en blanco.
- Al analizar sus bytes mediante un visor hexadecimal como `xxd`:
  ```bash
  head -c 50 whitepages.txt | xxd
  ```
  Se descubre que el archivo contiene dos tipos distintos de espacios en blanco:
  - Espacio de ancho eme en UTF-8 (`\xe2\x80\x83` correspondiente a Unicode `U+2003` *EM SPACE*).
  - Espacio estándar ASCII (`\x20` o `' '`).
- Esta técnica corresponde a esteganografía por espacios en blanco (*whitespace steganography*), donde cada tipo de espacio representa un bit (`0` o `1`).
- Mapeando `\xe2\x80\x83` a `0` y `\x20` a `1`, convertimos la secuencia de bits resultante a texto ASCII en Python:
  ```bash
  python3 -c '
  with open("whitepages.txt", "rb") as f:
      d = f.read().replace(b"\xe2\x80\x83", b"0").replace(b" ", b"1")
  print("".join(chr(int(d[i:i+8], 2)) for i in range(0, len(d), 8)))
  '
  ```
- Al ejecutar la decodificación, se obtiene el texto con la bandera:
```text
academy{not_all_spaces_are_created_equal_d4c2f3dcae81af992c8e86ddecdf2dd5}
```

## Notas Adicionales
- La bandera (*"not all spaces are created equal"*) hace alusión a la diversidad de caracteres de espacio en el estándar Unicode frente al espacio ASCII tradicional.
- Herramientas utilizadas en Kali Linux:
  - `xxd`: Herramienta para volcado e inspección hexadecimal.
  - `python3`: Para decodificación y transformación de la cadena binaria a caracteres ASCII.

## Referencias
- [Unicode Character 'EM SPACE' (U+2003)](https://www.fileformat.info/info/unicode/char/2003/index.htm)
- [Whitespace Steganography](https://en.wikipedia.org/wiki/Steganography)
- [xxd(1) - Linux man page](https://man7.org/linux/man-pages/man1/xxd.1.html)