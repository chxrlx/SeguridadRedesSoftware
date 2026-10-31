## Descripción
A message has been encrypted using RSA. The public key is gone… but someone might have been careless with the private key. Can you recover it and decrypt the message? Download the flag and image.

## Solución
- Inspeccionamos los metadatos de la imagen `image.jpg` mediante `exiftool`:
  ```bash
  exiftool image.jpg
  ```
- En el campo `Comment` del encabezado JFIF encontramos una extensa cadena codificada en hexadecimal:
  ```text
  Comment : 2d2d2d2d2d424547494e2050524956415445204b45592d2d2d2d2d0a4d494945764149424144414e...
  ```
- Al convertir la secuencia hexadecimal a ASCII (`2d2d2d2d2d424547494e...` $\rightarrow$ `-----BEGIN PRIVATE KEY-----`), confirmamos que se trata de una clave privada RSA en formato PEM (PKCS#8).
- Extraemos el comentario, lo decodificamos de hexadecimal y lo guardamos en `private.pem`:
  ```bash
  exiftool -s3 -Comment image.jpg | xxd -r -p > private.pem
  ```
- Utilizamos `openssl` junto con la clave privada recuperada para descifrar el archivo `flag.enc`:
  ```bash
  openssl pkeyutl -decrypt -inkey private.pem -in flag.enc
  ```
- Al ejecutar el comando obtenemos la bandera en texto claro:
```text
academy{rs4_k3y_1n_1mg_9db27b2c}
```

## Notas Adicionales
- La seguridad de RSA depende por completo de mantener en secreto la clave privada (que contiene el exponente privado $d$ y los factores primos $p, q$). Ocultar una clave privada mediante esteganografía o en los metadatos de una imagen constituye seguridad por oscuridad (*security through obscurity*) y permite a cualquier atacante que inspeccione el archivo recuperar la clave y descifrar los mensajes.
- Herramientas utilizadas:
  - `exiftool`: Extracción del campo `Comment` de la imagen JPEG.
  - `xxd`: Decodificación hexadecimal a formato PEM.
  - `openssl pkeyutl`: Descifrado RSA con clave privada PKCS#8.

## Referencias
- [OpenSSL pkeyutl Manual](https://docs.openssl.org/master/man1/openssl-pkeyutl/)
- [ExifTool by Phil Harvey](https://exiftool.org/)
