## Descripción
We found a leak of a blackmarket website's login credentials. Can you find the password of the user cultiris and successfully decrypt it? Download the leak here.

The first user in usernames.txt corresponds to the first password in passwords.txt. The second user corresponds to the second password, and so on.

## Solución
- Descomprimimos el archivo `leak.tar` que contiene dos listas de texto: `usernames.txt` y `passwords.txt`:
  ```bash
  tar -xf leak.tar
  ```
- Buscamos el número de línea correspondiente al usuario `cultiris` en `usernames.txt`:
  ```bash
  grep -n "cultiris" leak/usernames.txt
  ```
  Salida:
  ```text
  378:cultiris
  ```
- Extraemos la contraseña ubicada en la misma posición (línea 378) de `passwords.txt`:
  ```bash
  sed -n '378p' leak/passwords.txt
  ```
  Salida obtenida:
  ```text
  npnqrzl{P7e1S_54I35_71Z3}
  ```
- Observamos que el texto cifrado tiene el formato `npnqrzl{...}`, correspondiente a un cifrado César / ROT13 del prefijo `academy{...}`.
- Aplicamos la sustitución ROT13 sobre la cadena mediante `tr` o Python:
  ```bash
  sed -n '378p' leak/passwords.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
  ```
- Salida generada:
```text
academy{C7r1F_54V35_71M3}
```

## Notas Adicionales
- La fuga de credenciales (credential stuffing / leak analysis) es un escenario común donde atacantes reutilizan combinaciones de usuario y contraseña filtradas.
- ROT13 (Rotate by 13 places) es un caso especial del cifrado César con desplazamiento 13 sobre el alfabeto de 26 letras inglesas, siendo su propia función inversa (aplicar ROT13 dos veces restaura el texto original).
- Herramientas utilizadas en Kali Linux:
  - `tar`: Extracción del archivo comprimido.
  - `grep`: Búsqueda y numeración de línea del usuario objetivo.
  - `sed`: Extracción de la línea específica por su número.
  - `tr`: Transformación y sustitución de caracteres (decodificación ROT13).

## Referencias
- [ROT13 Cipher - Wikipedia](https://en.wikipedia.org/wiki/ROT13)
- [Credential Stuffing - OWASP](https://owasp.org/www-community/attacks/Credential_stuffing)
- [tr(1) - Linux man page](https://manpages.debian.org/testing/coreutils/tr.1.en.html)
