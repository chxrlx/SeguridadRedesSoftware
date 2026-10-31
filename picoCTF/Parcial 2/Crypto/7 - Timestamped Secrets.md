## Descripción
Someone encrypted a message using AES in ECB mode but they weren’t very careful with their key. Turns out it’s derived from something as simple as the current time! Can you uncover the key and decrypt the flag? Download the encrypted message: message

You may also find the encryption script helpful: code

## Solución
- Analizamos el script `encryption.py`:
  ```python
  def encrypt(plaintext: str, timestamp: int) -> str:
      timestamp = int(time.time())
      key = sha256(str(timestamp).encode()).digest()[:16]
      cipher = AES.new(key, AES.MODE_ECB)
      padded = pad(plaintext.encode(), AES.block_size)
      ciphertext = cipher.encrypt(padded)
      return ciphertext.hex()
  ```
- La clave AES-128 (16 bytes) se genera tomando los primeros 16 bytes del hash SHA-256 de la marca de tiempo Unix (`int(time.time())`) convertida a cadena ASCII.
- En el archivo `message.txt` se nos proporciona la pista del timestamp junto con el texto cifrado en hexadecimal:
  ```text
  Hint: The encryption was done around 1790147510 UTC
  Ciphertext (hex): f4de02cdd646ec8434da64338ea3088b19782037a143d83710869e38ec9f690e
  ```
- Desarrollamos un script en Python para probar marcas de tiempo alrededor de `1790147510`, derivar la clave AES-ECB y verificar cuál produce el prefijo `academy{`:
  ```python
  from hashlib import sha256
  from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes

  ct = bytes.fromhex("f4de02cdd646ec8434da64338ea3088b19782037a143d83710869e38ec9f690e")
  base_ts = 1790147510

  for delta in range(-1000, 1001):
      ts = base_ts + delta
      key = sha256(str(ts).encode()).digest()[:16]
      cipher = Cipher(algorithms.AES(key), modes.ECB())
      decryptor = cipher.decryptor()
      pt = decryptor.update(ct) + decryptor.finalize()
      if pt.startswith(b"academy{"):
          pad_len = pt[-1]
          print(f"Timestamp: {ts}")
          print(pt[:-pad_len].decode("ascii"))
          break
  ```
- El timestamp exacto resulta ser `1790147510` y, tras eliminar el relleno PKCS#7 (`\x04\x04\x04\x04`), obtenemos la bandera:
```text
academy{sa3S_sEc9t_57326579}
```

## Notas Adicionales
- Aunque AES-128 y SHA-256 son algoritmos criptográficamente seguros, derivar una clave a partir de una fuente de baja entropía y predecible como la hora del sistema (`time.time()` en segundos) reduce drásticamente el espacio de búsqueda, permitiendo ataques de fuerza bruta en cuestión de milisegundos.
- Para la generación de claves criptográficas siempre debe utilizarse un generador de números pseudoaleatorios criptográficamente seguro (CSPRNG), como `os.urandom(16)` o el módulo `secrets` de Python.
- Herramientas utilizadas:
  - Python 3 (`hashlib.sha256` y `cryptography.hazmat.primitives.ciphers`).

## Referencias
- [CWE-330: Use of Insufficiently Random Values](https://cwe.mitre.org/data/definitions/330.html)
- [Python secrets module documentation](https://docs.python.org/3/library/secrets.html)
