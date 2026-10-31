## Descripción
Can you abuse the oracle?

An attacker was able to intercept communications between a bank and a fintech company. They managed to get the message (ciphertext) and the password that was used to encrypt the message. After some intensive reconassainance they found out that the bank has an oracle that was used to encrypt the password and can be found here nc xebec.cylabacademy.net 28459. Decrypt the password and use it to decrypt the message. The oracle can decrypt anything except the password.

## Solución
- Inspeccionamos los dos archivos proporcionados con `file`:
  - `secret.enc`: Archivo cifrado simétricamente con `openssl enc` (`Salted__...`).
  - `password.enc`: Contiene el texto cifrado RSA $c$ correspondiente a la contraseña:
    ```text
    3866059118128440816066897401856426000966295118699899417550986956938429514754327731737414991842543326348611940913710191848472878865665656456524988060185143
    ```
- Aunque el oráculo rechaza descifrar $c$ directamente, RSA sin relleno posee una propiedad **homomórfica multiplicativa (maleabilidad)** que permite realizar un **Ataque de Texto Cifrado Elegido mediante Cegado (RSA Blinding / Chosen-Ciphertext Attack)**:
  1. Solicitamos al oráculo cifrar el valor $m_2 = 2$ (enviando el byte `\x02` en la opción `E`), obteniendo:
     $$c_2 \equiv 2^e \pmod n$$
  2. Multiplicamos el texto cifrado de la contraseña $c$ por $c_2$:
     $$c' = c \cdot c_2 \equiv (m \cdot 2)^e \pmod n$$
  3. Enviamos $c'$ al oráculo en la opción `D` (descifrar). Como $c' \neq c$, el oráculo lo acepta y nos devuelve:
     $$m' \equiv (c')^d \equiv 2m \pmod n$$
  4. Dividimos $m'$ entre $2$ para recuperar la contraseña original $m$.
- Automatizamos la explotación del oráculo y la recuperación de la contraseña con el siguiente script en Python:
  ```python
  import socket
  import time
  import subprocess

  with open("password.enc") as f:
      c_pass = int(f.read().strip())

  s = socket.create_connection(("xebec.cylabacademy.net", 28459), timeout=5)
  s.recv(4096)

  # 1. Cifrar m = 2
  s.sendall(b"E\n")
  time.sleep(0.2)
  s.recv(4096)
  s.sendall(b"\x02\n")
  time.sleep(0.2)
  resp = s.recv(4096).decode(errors="ignore")
  c_2 = int(resp.split("ciphertext (m ^ e mod n) ")[1].split()[0])

  # 2. Cegar el criptograma: c' = c_pass * c_2
  c_blind = c_pass * c_2

  # 3. Descifrar c' en el oráculo
  s.sendall(b"D\n")
  time.sleep(0.2)
  s.recv(4096)
  s.sendall(f"{c_blind}\n".encode())
  time.sleep(0.2)
  resp_dec = s.recv(4096).decode(errors="ignore")
  s.close()

  m_blind_hex = resp_dec.split("decrypted ciphertext as hex (c ^ d mod n): ")[1].split()[0]
  m_pass = int(m_blind_hex, 16) // 2
  password = m_pass.to_bytes((m_pass.bit_length() + 7) // 8, "big").decode("ascii")
  print(f"Password recuperada: {password}")
  ```
- El oráculo devuelve `6cca66cccc` en hexadecimal; al dividirlo entre 2 obtenemos `0x3665336666`, que en ASCII corresponde a la contraseña:
  ```text
  6e3ff
  ```
- Finalmente, desciframos `secret.enc` usando `openssl` con la contraseña recuperada:
  ```bash
  openssl enc -aes-256-cbc -d -in secret.enc -k "6e3ff"
  ```
- Obteniendo la bandera:
```text
academy{su((3ss_(r@ck1ng_r3@_6e3fffd9}
```

## Notas Adicionales
- El cifrado RSA de libro de texto (*Textbook RSA*) es maleable porque la operación de cifrado es un homomorfismo de grupos multiplicativos: $E(m_1) \cdot E(m_2) \equiv (m_1 \cdot m_2)^e \pmod n = E(m_1 \cdot m_2)$.
- Esto demuestra que filtrar un único criptograma prohibido en un oráculo de descifrado es inútil si no se emplea un esquema de relleno con verificación de integridad como **RSA-OAEP (Optimal Asymmetric Encryption Padding)**, el cual destruye la estructura algebraica multiplicativa e impide que un atacante construya criptogramas válidos a partir de otros.
- Herramientas utilizadas:
  - Python 3 (`socket` y aritmética de precisión arbitraria).
  - `openssl enc -aes-256-cbc`: Descifrado del archivo `secret.enc`.

## Referencias
- [Chosen-ciphertext attack on RSA - Wikipedia](https://en.wikipedia.org/wiki/Chosen-ciphertext_attack)
- [Homomorphic properties / Malleability of RSA](https://en.wikipedia.org/wiki/Malleability_(cryptography)#Example:_RSA_cryptosystem)
