## Descripción
A message has come in but it seems to be all scrambled. Luckily it seems to have the key at the beginning. Can you crack this substitution cipher? Download the message here.

## Solución
- El archivo `message.txt` contiene un texto cifrado mediante un cifrado de sustitución monoalfabética clásica.
- En la primera línea del archivo se proporciona la clave de sustitución ordenada de 26 letras:
  ```text
  HRZUEVABDLIOJYTGNXCMWPKSQF
  ```
  La cual mapea directamente a las 26 letras del alfabeto estándar en inglés:
  ```text
  ABCDEFGHIJKLMNOPQRSTUVWXYZ
  ```
- Al final del archivo se observa la estructura del mensaje cifrado con la bandera:
  ```text
  Mbe voha dc: hzhuejq{5WR5717W710Y_3P0OW710Y_4VRU1ER7}
  ```
- Construimos una tabla de traducción en Python (conservando mayúsculas y minúsculas) para descifrar el texto completo:
  ```python
  import string

  with open('message.txt') as f:
      lines = f.read().splitlines()

  key = lines[0].strip()
  alphabet = string.ascii_uppercase

  # Mapeo de la clave de sustitución al alfabeto en claro
  trans_table = str.maketrans(
      key + key.lower(),
      alphabet + alphabet.lower()
  )

  full_text = '\n'.join(lines[2:])
  decrypted_text = full_text.translate(trans_table)
  print(decrypted_text)
  ```
- El texto descifrado corresponde a un fragmento del cuento *"The Gold-Bug"* de Edgar Allan Poe y concluye con:
  ```text
  The flag is: academy{5UB5717U710N_3V0LU710N_4FBD1EB7}
  ```
- Bandera obtenida:
```text
academy{5UB5717U710N_3V0LU710N_4FBD1EB7}
```

## Notas Adicionales
- El cifrado de sustitución monoalfabética reemplaza cada carácter del texto en claro por otro carácter fijado por un alfabeto permutado.
- Dado que en este reto la clave completa venía incluida explícitamente en la primera línea del archivo, no fue necesario realizar análisis de frecuencia de letras ni emparejamiento de n-gramas.
- Herramientas utilizadas:
  - Python 3 (`str.maketrans`, `translate`): Mapeo y sustitución de caracteres monoalfabéticos.

## Referencias
- [Monoalphabetic Substitution Cipher - Wikipedia](https://en.wikipedia.org/wiki/Substitution_cipher#Simple_substitution)
- [The Gold-Bug by Edgar Allan Poe](https://www.gutenberg.org/ebooks/2147)
- [Python str.maketrans documentation](https://docs.python.org/3/library/stdtypes.html#str.maketrans)
