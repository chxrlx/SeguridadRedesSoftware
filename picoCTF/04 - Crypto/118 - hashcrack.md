## Descripción
A company stored a secret message on a server which got breached due to the admin using weakly hashed passwords. Can you gain access to the secret stored within the server?

## Solución
- Nos conectamos al servicio mediante `nc` o un script interactivo en Python. El servidor solicita romper consecutivamente tres funciones hash débiles utilizadas para almacenar contraseñas comunes:
  1. **MD5** (32 caracteres hexadecimales):
     - Hash: `482c811da5d5b4bc6d497ffa98491e38`
     - Contraseña descifrada: `password123`
  2. **SHA-1** (40 caracteres hexadecimales):
     - Hash: `b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3`
     - Contraseña descifrada: `letmein`
  3. **SHA-256** (64 caracteres hexadecimales):
     - Hash: `916e8c4f79b25028c9e467f1eb8eee6d6bbdff965f9928310ad30a8d88697745`
     - Contraseña descifrada: `qwerty098`
- Se implementó un script interactivo en Python que se conecta al socket TCP, extrae dinámicamente cada hash mediante expresiones regulares y lo busca en la lista de contraseñas de `rockyou.txt` calculando el resumen correspondiente:
  ```python
  import socket, hashlib, re

  # Cargar contraseñas comunes de rockyou.txt
  rainbow = {}
  with open('/usr/share/wordlists/rockyou.txt', 'r', encoding='latin-1') as f:
      for i in range(200000):
          line = f.readline()
          if not line: break
          pwd = line.strip('\r\n')
          rainbow[hashlib.md5(pwd.encode()).hexdigest()] = pwd
          rainbow[hashlib.sha1(pwd.encode()).hexdigest()] = pwd
          rainbow[hashlib.sha256(pwd.encode()).hexdigest()] = pwd

  s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  s.connect(('xebec.cylabacademy.net', 10176))
  s.settimeout(5)

  while True:
      try:
          data = s.recv(4096).decode()
          print(data)
          if 'academy{' in data:
              break
          matches = re.findall(r'[a-fA-F0-9]{32,64}', data)
          if matches:
              target_hash = matches[0].lower()
              if target_hash in rainbow:
                  pwd = rainbow[target_hash]
                  s.sendall((pwd + '\n').encode())
              else:
                  # Búsqueda completa en rockyou.txt
                  with open('/usr/share/wordlists/rockyou.txt', 'r', encoding='latin-1') as f:
                      for line in f:
                          pwd = line.strip('\r\n')
                          h = None
                          if len(target_hash) == 32: h = hashlib.md5(pwd.encode()).hexdigest()
                          elif len(target_hash) == 40: h = hashlib.sha1(pwd.encode()).hexdigest()
                          elif len(target_hash) == 64: h = hashlib.sha256(pwd.encode()).hexdigest()
                          if h == target_hash:
                              s.sendall((pwd + '\n').encode())
                              break
      except socket.timeout:
          break
  ```
- Tras resolver los tres retos de hash, el servidor revela la bandera:
```text
academy{UseStr0nG_h@shEs_&PaSswDs!_a8894b1b}
```

## Notas Adicionales
- Las funciones de hash criptográfico clásicas y rápidas como MD5, SHA-1 e incluso SHA-256 no son recomendables para almacenar contraseñas de forma aislada, ya que están diseñadas para procesar datos a alta velocidad y son vulnerables a ataques de fuerza bruta y diccionario asistidos por GPU o tablas rainbow.
- Para el almacenamiento seguro de contraseñas se deben utilizar funciones de derivación de claves con factor de costo ajustable (memory-hard / compute-hard) y salting (salting único por usuario), como Argon2id, bcrypt, scrypt o PBKDF2.
- Herramientas utilizadas:
  - `rockyou.txt`: Diccionario estándar de contraseñas en Kali Linux (`/usr/share/wordlists/rockyou.txt`).
  - Python 3 (`hashlib`, `socket`): Automatización de conexión eインversión de hashes.

## Referencias
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
- [Hashcat - Advanced Password Recovery](https://hashcat.net/hashcat/)
- [Python hashlib documentation](https://docs.python.org/3/library/hashlib.html)
