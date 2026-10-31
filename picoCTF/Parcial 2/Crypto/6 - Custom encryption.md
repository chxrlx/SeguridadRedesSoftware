## Descripción
Can you get sense of this code file and write the function that will decode the given encrypted file content.

Find the encrypted file here flag_info and code file might be good to analyze and get the flag.

## Solución
- Analizamos el proceso de cifrado en `custom_encryption.py`:
  1. Se definen los parámetros públicos $p = 97$ y $g = 31$, junto con los valores $a = 95$ y $b = 31$ impresos en `enc_flag`.
  2. Se calcula la clave compartida tipo Diffie-Hellman:
     $$\text{shared\_key} = g^{a \cdot b} \bmod p = 31^{95 \times 31} \bmod 97 = 48$$
  3. La función `dynamic_xor_encrypt(plain_text, "trudeau")` invierte el texto original (`plaintext[::-1]`) y aplica una operación XOR carácter por carácter con la clave repetida `"trudeau"`.
  4. Finalmente, la función `encrypt(semi_cipher, shared_key)` multiplica el valor ASCII de cada carácter resultante por `shared_key * 311`.
- Para invertir el cifrado y recuperar el mensaje original:
  1. Dividimos cada número de la lista `cipher` entre `shared_key * 311` ($48 \times 311 = 14928$).
  2. Aplicamos XOR a cada valor con el carácter correspondiente de `"trudeau"`.
  3. Invertimos la cadena resultante (`[::-1]`).
- Implementamos el siguiente script de descifrado en Python:
  ```python
  p = 97
  g = 31
  a = 95
  b = 31
  text_key = "trudeau"
  cipher = [
      134352, 343344, 238848, 1194240, 0, 1298736, 1059888, 1134528,
      1059888, 626976, 0, 1239024, 313488, 74640, 1015104, 0, 328416,
      1283808, 14928, 925536, 358272, 403056, 89568, 89568, 253776,
      89568, 388128, 179136, 373200, 343344, 253776, 74640, 89568, 0
  ]

  shared_key = pow(g, a * b, p)
  semi_cipher = [c // (shared_key * 311) for c in cipher]

  reversed_plain = "".join(
      chr(val ^ ord(text_key[i % len(text_key)]))
      for i, val in enumerate(semi_cipher)
  )
  flag = reversed_plain[::-1]
  print(flag)
  ```
- Al ejecutar el script obtenemos la bandera:
```text
academy{custom_d2cr0pt6d_5826e4ee}
```

## Notas Adicionales
- El esquema combina una derivación de clave Diffie-Hellman (cuyos secretos $a$ y $b$ se imprimen directamente en la salida), un cifrado XOR polialfabético sobre la cadena invertida con clave fija (`"trudeau"`) y un escalamiento lineal (`* shared_key * 311`). Al ser todas las operaciones biyectivas y con parámetros conocidos, el proceso es completamente reversible.
- Herramientas utilizadas:
  - Python 3.

## Referencias
- [XOR cipher - Wikipedia](https://en.wikipedia.org/wiki/XOR_cipher)
- [Diffie–Hellman key exchange - Wikipedia](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange)
