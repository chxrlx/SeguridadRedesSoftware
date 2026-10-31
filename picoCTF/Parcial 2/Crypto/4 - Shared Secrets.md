## Descripción
A message was encrypted using a shared secret... but it looks like one side of the exchange leaked something. Can you piece together the secret and get the flag? Download the message. And source code
## Solución
- Al analizar el código fuente en `encryption.py`, vemos que se implementa un intercambio de claves Diffie-Hellman seguido de un cifrado XOR de un solo byte:
  ```python
  # Server's secret
  a = randint(2, p-2)
  A = pow(g, a, p)

  # Client secret
  b = randint(2, p-2)
  B = pow(g, b, p)

  # Shared key
  shared = pow(A, b, p)

  # Encrypt flag
  flag = b"academy{...}"
  enc = bytes([x ^ (shared % 256) for x in flag])
  ```
- Al revisar `message.txt`, observamos que en lugar de publicar la clave pública del cliente $B = g^b \bmod p$, el archivo filtró directamente el exponente privado del cliente $b$, junto con la clave pública del servidor $A$, el primo $p$ y el texto cifrado `enc`:
  - Dado que poseemos $A$, $b$ y $p$, podemos calcular directamente el secreto compartido:
    $$\text{shared} = A^b \bmod p$$
  - Además, dado que el cifrado consiste en un XOR con un único byte (`shared % 256`), también sería trivial recuperar la clave mediante un ataque de texto claro conocido con el primer carácter de `academy{` (`0x43 ^ ord('a') = 34`).
- Implementamos el siguiente script en Python para recuperar el secreto compartido y descifrar `enc`:
  ```python
  with open("message.txt") as f:
      params = {}
      for line in f:
          k, v = line.strip().split(" = ")
          params[k] = v

  p = int(params["p"])
  A = int(params["A"])
  b = int(params["b"])
  enc = bytes.fromhex(params["enc"])

  shared = pow(A, b, p)
  key = shared % 256
  flag = bytes([x ^ key for x in enc]).decode("ascii")
  print(flag)
  ```
- Al ejecutar el script obtenemos `key = 34` (`0x22`) y la bandera:
```text
academy{dh_s3cr3t_441eb9ee}
```

## Notas Adicionales
- En el protocolo Diffie-Hellman, las partes intercambian públicamente $A = g^a \bmod p$ y $B = g^b \bmod p$, manteniendo en estricto secreto los exponentes privados $a$ y $b$. Si cualquiera de los exponentes privados se filtra (como $b$ en este reto), un observador pasivo puede calcular el secreto compartido $s = A^b \bmod p = B^a \bmod p$.
- Adicionalmente, derivar una clave de cifrado tomando únicamente `shared % 256` reduce el espacio de claves efectivas a $2^8 = 256$ valores posibles, anulando la seguridad criptográfica del intercambio Diffie-Hellman de 1048 bits.
- Herramientas utilizadas:
  - Python 3 (exponenciación modular `pow(A, b, p)` y decodificación XOR).

## Referencias
- [Diffie–Hellman key exchange - Wikipedia](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange)
