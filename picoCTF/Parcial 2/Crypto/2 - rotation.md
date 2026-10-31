## Descripción
You will find the flag after decrypting this file Download the encrypted flag here.

## Solución
- Leemos el contenido del archivo `encrypted.txt`:
  ```text
  ikilmug{z0bib1wv_l3kzgxb3l_i4008m11}
  ```
- Observamos que el texto mantiene la estructura de la bandera (`ikilmug{...}`) y el reto se denomina `rotation`, lo cual indica el uso de un cifrado por rotación (Cifrado César / ROT-N).
- Calculando la diferencia entre el primer carácter cifrado (`i`, posición 8) y el primer carácter esperado del prefijo (`a`, posición 0), determinamos que se aplicó una rotación de $+8$ posiciones hacia adelante (por lo que para descifrar debemos rotar $-8$ o equivalentemente $+18 \pmod{26}$).
- Podemos descifrarlo mediante el comando `tr` en la terminal:
  ```bash
  cat encrypted.txt | tr 'i-za-hI-ZA-H' 'a-zA-Z'
  ```
- O mediante el siguiente script en Python probando o aplicando el desplazamiento:
  ```python
  with open("encrypted.txt") as f:
      cipher = f.read().strip()

  decrypted = "".join(
      chr((ord(c) - ord("a") - 8) % 26 + ord("a")) if c.islower() else
      chr((ord(c) - ord("A") - 8) % 26 + ord("A")) if c.isupper() else c
      for c in cipher
  )
  print(decrypted)
  ```
- Al ejecutar la rotación obtenemos la bandera:
```text
academy{r0tat1on_d3crypt3d_a4008e11}
```

## Notas Adicionales
- El cifrado por rotación (Cifrado César) desplaza cada letra del alfabeto un número fijo de posiciones módulo 26, dejando intactos los dígitos y símbolos especiales. Al existir únicamente 25 desplazamientos no triviales posibles, es vulnerable a fuerza bruta inmediata o a ataques de texto claro conocido (*Known-Plaintext Attack*) usando el prefijo `academy{`.
- Herramientas utilizadas:
  - `tr` (GNU coreutils) y Python 3.

## Referencias
- [Caesar cipher - Wikipedia](https://en.wikipedia.org/wiki/Caesar_cipher)
