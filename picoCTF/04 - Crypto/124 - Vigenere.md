## Descripción
Can you decrypt this message? Decrypt this message using this key "CYLAB".

## Solución
- El archivo `cipher.txt` contiene una cadena cifrada mediante el cifrado polialfabético de Vigenère:
  ```text
  caldfow{O0NU_WQ3_G1G3O3T3_A1AH3S_42eaff9d}
  ```
- La clave indicada en la descripción es:
  ```text
  CYLAB
  ```
- En el cifrado de Vigenère, cada letra del texto en claro $p_i$ se recupera restando modularmente la letra de la clave correspondiente $k_i$ (desplazando únicamente en caracteres alfabéticos y preservando mayúsculas, minúsculas, números y caracteres especiales):
  $$p_i \equiv (c_i - k_i) \pmod{26}$$
- Implementamos el siguiente script en Python para descifrar el criptograma:
  ```python
  with open('cipher.txt') as f:
      ciphertext = f.read().strip()

  key = 'CYLAB'

  def vigenere_decrypt(cipher, key):
      res = []
      k_idx = 0
      key = key.upper()
      for ch in cipher:
          if ch.isalpha():
              is_upper = ch.isupper()
              c_val = ord(ch.upper()) - ord('A')
              k_val = ord(key[k_idx % len(key)]) - ord('A')
              p_val = (c_val - k_val) % 26
              p_char = chr(ord('A') + p_val)
              res.append(p_char if is_upper else p_char.lower())
              k_idx += 1
          else:
              res.append(ch)
      return ''.join(res)

  decrypted = vigenere_decrypt(ciphertext, key)
  print(decrypted)
  ```
- Al ejecutar el script obtenemos la bandera:
```text
academy{D0NT_US3_V1G3N3R3_C1PH3R_42ccuf9c}
```

## Notas Adicionales
- El cifrado de Vigenère es un método clásico de cifrado polialfabético que utiliza una palabra clave repetida para aplicar múltiples desplazamientos César diferentes.
- A diferencia de los cifrados monoalfabéticos simples, enmascara las frecuencias de las letras individuales. No obstante, si se conoce la clave (como en este reto) el descifrado es directo; y si no se conoce, puede romperse determinando la longitud de la clave mediante el examen de Kasiski o el índice de coincidencia de Friedman.
- Herramientas utilizadas:
  - Python 3: Implementación estándar del algoritmo inverso de Vigenère módulo 26.

## Referencias
- [Vigenère cipher - Wikipedia](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher)
- [Kasiski examination - Wikipedia](https://en.wikipedia.org/wiki/Kasiski_examination)
