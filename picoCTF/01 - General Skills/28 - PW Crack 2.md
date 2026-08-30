## Descripción
Can you crack the password to get the flag?
## Solución
- Abrimos el archivo descargado con el comando: `nano level2.py`
- Vemos lo siguiente en el código: `if( user_pw == chr(0x34) + chr(0x65) + chr(0x63) + chr(0x39) ):`
- Convertimos los valores hexadecimales a decimal primero usando una herramienta web y luego ese valor decimal lo convertimos a su valor ASCII
- Ejecutamos el programa: `python level2.py` e ingresamos la contraseña que decodificamos
```text
picoCTF{tr45h_51ng1ng_9701e681}
```
## Notas Adicionales
## Referencias
[Convertidor de Hexadecimal a Decimal en linea | HEX a DEC](https://masterplc.com/calculadora/hexadecimal-a-decimal/)
[Convertidor de decimal a ASCII (dec a ascii)](https://www.prepostseo.com/tool/decimal-to-ascii)