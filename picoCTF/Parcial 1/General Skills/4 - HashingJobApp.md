## Descripción
If you want to hash with the best, beat this test!
Conexión: netcat

## Solución
- Nos conectamos al servidor remoto mediante Netcat
- El servicio nos desafía a responder en tiempo real calculando el hash MD5 de tres cadenas aleatorias consecutivas (excluyendo las comillas):
  - Reto 1: `Japan`
    - Calculamos: `echo -n 'Japan' | md5sum` $\rightarrow$ `53a577bb3bc587b0c28ab808390f1c9b`
  - Reto 2: `Atlantis`
    - Calculamos: `echo -n 'Atlantis' | md5sum` $\rightarrow$ `a425a3c39e1eaba4a378b0a08d80db9b`
  - Reto 3: `China`
    - Calculamos: `echo -n 'China' | md5sum` $\rightarrow$ `ae54a5c026f31ada088992587d92cb3a`
- Debido a que el servidor puede cerrar la conexión si tardamos demasiado en contestar, se puede automatizar la resolución mediante un script en Python:
  ```python
  import socket, re, hashlib

  s = socket.socket()
  s.connect(("localhost", 3224))
  buf = ""
  while True:
      chunk = s.recv(1024).decode(errors="ignore")
      if not chunk: break
      buf += chunk
      m = re.search(r"excluding the quotes:\s*'([^']+)'", buf)
      if m and "Answer:" in buf:
          h = hashlib.md5(m.group(1).encode()).hexdigest()
          s.sendall(f"{h}\n".encode())
          buf = ""
      if "academy{" in chunk:
          print(chunk)
  ```
- Al ingresar correctamente los tres hashes MD5, el servidor valida las respuestas y nos devuelve la bandera

```text
academy{4ppl1c4710n_r3c31v3d_467e06bf}
```

## Notas Adicionales
- **MD5 (Message Digest Algorithm 5)**: Es una función resumen criptográfica que genera una huella de 128 bits expresada habitualmente en una cadena de 32 dígitos hexadecimales.
- **Uso de `echo -n`**: Al generar hashes en la terminal con `md5sum`, es fundamental utilizar la opción `-n` en `echo` para evitar incluir el salto de línea (`\n`) implícito, ya que cualquier variación en los bytes de entrada produce un hash completamente diferente.

## Referencias
- [MD5 Hash Generator](https://www.md5hashgenerator.com/)
- [Man Page - md5sum](https://linux.die.net/man/1/md5sum)
- [Documentación de Python hashlib](https://docs.python.org/3/library/hashlib.html)