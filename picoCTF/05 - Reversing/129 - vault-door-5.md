## Descripción
In the last challenge, you mastered octal (base 8), decimal (base 10), and hexadecimal (base 16) numbers, but this vault door uses a different change of base as well as URL encoding! The source code for this vault is here: VaultDoor5.java

## Solución
- Analizando el método `checkPassword` en `VaultDoor5.java`:
  ```java
  public boolean checkPassword(String password) {
      String urlEncoded = urlEncode(password.getBytes());
      String base64Encoded = base64Encode(urlEncoded.getBytes());
      String expected = "JTYzJTMwJTZlJTc2JTMzJTcyJTc0JTMxJTZlJTY3JTVm"
                      + "JTY2JTcyJTMwJTZkJTVmJTYyJTYxJTM1JTY1JTVmJTM2"
                      + "JTM0JTVmJTM5JTMyJTYyJTY1JTMyJTM1JTY2JTMx";
      return base64Encoded.equals(expected);
  }
  ```
- El proceso de validación aplica dos transformaciones consecutivas sobre los bytes de la contraseña:
  1. **URL encoding** (codificación porcentual de cada byte en hexadecimal).
  2. **Base64 encoding** de la cadena codificada por URL.
- Para revertir el proceso e inferir la contraseña original:
  1. Se decodifica la cadena `expected` en Base64, obteniendo una secuencia URL-encoded:
     ```text
     %63%30%6e%76%33%72%74%31%6e%67%5f%66%72%30%6d%5f%62%61%35%65%5f%36%34%5f%39%32%62%65%32%35%66%31
     ```
  2. Se decodifican los valores porcentuales a caracteres ASCII (`urllib.parse.unquote`).
- Script de decodificación en Python:
  ```python
  import base64
  import urllib.parse

  expected = (
      "JTYzJTMwJTZlJTc2JTMzJTcyJTc0JTMxJTZlJTY3JTVm"
      "JTY2JTcyJTMwJTZkJTVmJTYyJTYxJTM1JTY1JTVmJTM2"
      "JTM0JTVmJTM5JTMyJTYyJTY1JTMyJTM1JTY2JTMx"
  )

  # 1. Base64 Decode
  url_encoded = base64.b64decode(expected).decode("ascii")

  # 2. URL Decode
  password = urllib.parse.unquote(url_encoded)
  flag = f"academy{{{password}}}"
  print(flag)
  ```
- Al ejecutar el script obtenemos `c0nv3rt1ng_fr0m_ba5e_64_92be25f1`, resultando en la bandera:
```text
academy{c0nv3rt1ng_fr0m_ba5e_64_92be25f1}
```

## Notas Adicionales
- La codificación (Encoding) como Base64 y URL Percent-Encoding no constituye cifrado (Encryption); es simplemente una representación alternativa de datos diseñada para transporte seguro de caracteres a través de canales de texto o HTTP.
- Herramientas utilizadas:
  - Módulos `base64` y `urllib.parse` de Python 3.
  - Compilador de Java (`javac` y `java`) para verificar la solución contra la aplicación original.

## Referencias
- [RFC 4648 - The Base16, Base32, and Base64 Data Encodings](https://datatracker.ietf.org/doc/html/rfc4648)
- [RFC 3986 - Uniform Resource Identifier (URI): Generic Syntax (Percent-Encoding)](https://datatracker.ietf.org/doc/html/rfc3986#section-2.1)
