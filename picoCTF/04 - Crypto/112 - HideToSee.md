## Descripción

How about some hide and seek heh? Look at this image here.

## Solución

- La imagen muestra una rueda del cifrado Atbash, pero la bandera no está escrita directamente en la imagen visible.
- Primero comprobamos si el JPEG contiene datos ocultos mediante `steghide`:
  ```bash
  steghide info atbash.jpg
  ```
- El archivo tiene una capacidad de datos ocultos de aproximadamente 2.4 KB. Extraemos el contenido usando una contraseña vacía:
  ```bash
  steghide extract -sf atbash.jpg -p ""
  ```
- Esto genera el archivo `encrypted.txt`, cuyo contenido es:
  ```text
  zxzwvnb{zgyzhs_xizxp_wxyx498z}
  ```
- La rueda de la imagen indica que debemos aplicar Atbash. Para letras minúsculas, el mapeo es `a <-> z`, `b <-> y`, ..., `m <-> n`. Los números, guiones bajos y llaves permanecen sin cambios.
- Se puede decodificar con Python:
  ```python
  import string

  encrypted = "zxzwvnb{zgyzhs_xizxp_wxyx498z}"
  atbash = str.maketrans(
      string.ascii_lowercase,
      string.ascii_lowercase[::-1],
  )

  print(encrypted.translate(atbash))
  ```
- El resultado es:
  ```text
  academy{atbash_crack_dcbc498a}
  ```

## Notas Adicionales

- `steghide` permite ocultar datos dentro de imágenes JPEG; en este reto el payload puede extraerse sin proporcionar una contraseña.
- Atbash es un cifrado por sustitución monoalfabética en el que el alfabeto se invierte. Por ejemplo, `z` se convierte en `a`, `x` en `c` y `g` en `t`.
- La pista visual de la rueda Atbash sirve para identificar la segunda etapa después de extraer el archivo oculto.

## Referencias

- [Atbash - Wikipedia](https://en.wikipedia.org/wiki/Atbash)
- [steghide](https://steghide.sourceforge.net/)
