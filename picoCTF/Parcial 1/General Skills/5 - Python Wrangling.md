## Descripción
Python scripts are invoked kind of like programs in the Terminal... Can you run this Python script using this password to get the flag?
Recursos: `ende.py`, `password.txt`, `flag.txt.en`

## Solución
- Nos situamos en la carpeta donde se ubican los archivos del reto:
  - `ende.py`: Script en Python para cifrado (`-e`) y descifrado (`-d`) mediante Fernet
  - `password.txt`: Contiene la contraseña de descifrado
  - `flag.txt.en`: Archivo con la bandera cifrada
- Leemos la contraseña almacenada en `password.txt`:
  ```bash
  cat password.txt
  # Salida: 563e47ddeaf84eca8b2a31201381a898
  ```
- Ejecutamos el script pasando la bandera `-d` para descifrar el archivo `flag.txt.en`:
  ```bash
  python3 ende.py -d flag.txt.en
  ```
- Al solicitar el ingreso de la contraseña (`Please enter the password:`), introducimos `563e47ddeaf84eca8b2a31201381a898` (o bien pasándola como tercer argumento: `python3 ende.py -d flag.txt.en 563e47ddeaf84eca8b2a31201381a898`)
- El programa realiza el descifrado simétrico y muestra la bandera

```text
academy{4p0110_1n_7h3_h0us3_d6af8f37}
```

## Notas Adicionales
- **Paso de argumentos en Python (`sys.argv`)**: Permite que un script reciba parámetros desde la terminal al momento de su invocación (`python script.py arg1 arg2`).
- **Cifrado Fernet**: Mecanismo de cifrado simétrico autenticado que utiliza AES en modo CBC con padding PKCS7 y HMAC para autenticación, asegurando que los datos no puedan ser leídos ni modificados sin la clave correspondiente.

## Referencias
- [Documentación de Python - sys.argv](https://docs.python.org/es/3/library/sys.html#sys.argv)
- [Librería Cryptography - Fernet](https://cryptography.io/en/latest/fernet/)