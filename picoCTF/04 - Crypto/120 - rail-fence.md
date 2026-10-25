## Descripción
A type of transposition cipher is the rail fence cipher, which is described here. Here is one such cipher encrypted using the rail fence with 4 rails. Can you decrypt it? Download the message here.

## Solución
- El archivo `message.txt` contiene un texto cifrado mediante el algoritmo de transposición **Rail Fence** (cifrado en zigzag) utilizando 4 rieles (*rails*):
  ```text
  Ta _7N6D4Chlg:W3D_H3C31N__3E4ef sHR053F38N43DBC i33___NB
  ```
- En un cifrado Rail Fence con 4 rieles, los caracteres se distribuyen en un patrón de zigzag hacia abajo y hacia arriba con un ciclo de longitud:
  $$\text{ciclo} = 2 \times (\text{rieles} - 1) = 2 \times (4 - 1) = 6$$
- Para revertir la transposición, calculamos a qué riel corresponde cada posición del texto original y luego reconstruimos el texto ordenado a partir de los fragmentos leídos fila por fila.
- Implementamos un script en Python para descifrar el mensaje:
  ```python
  ciphertext = 'Ta _7N6D4Chlg:W3D_H3C31N__3E4ef sHR053F38N43DBC i33___NB'
  num_rails = 4

  def decrypt_rail_fence(ciphertext, num_rails):
      cycle = 2 * (num_rails - 1)
      rail_indices = [[] for _ in range(num_rails)]
      
      for i in range(len(ciphertext)):
          pos = i % cycle
          if pos >= num_rails:
              pos = cycle - pos
          rail_indices[pos].append(i)
          
      plaintext = [''] * len(ciphertext)
      cur = 0
      for r in range(num_rails):
          for idx in rail_indices[r]:
              plaintext[idx] = ciphertext[cur]
              cur += 1
              
      return ''.join(plaintext)

  decrypted = decrypt_rail_fence(ciphertext, num_rails)
  print(decrypted)
  ```
- Salida obtenida:
  ```text
  The flag is: WH3R3_D035_7H3_F3NC3_8361N_4ND_3ND_43BBCEC4
  ```
- Envolviendo el valor en el formato de bandera:
```text
academy{WH3R3_D035_7H3_F3NC3_8361N_4ND_3ND_43BBCEC4}
```

## Notas Adicionales
- El cifrado Rail Fence es un cifrado clásico de transposición que reorganiza el orden de los caracteres sin alterar las letras individuales (no sustituye caracteres).
- Su seguridad es mínima debido al reducido espacio de claves posibles (el número de rieles suele ser pequeño y se puede romper por fuerza bruta instantáneamente si la clave no se conoce).
- Herramientas utilizadas:
  - Python 3: Reconstrucción algorítmica del patrón de zigzag y lectura de posiciones transpuesta.

## Referencias
- [Rail Fence Cipher - Wikipedia](https://en.wikipedia.org/wiki/Rail_fence_cipher)
- [Transposition Ciphers - Practical Cryptography](http://practicalcryptography.com/ciphers/classical-era/rail-fence/)
