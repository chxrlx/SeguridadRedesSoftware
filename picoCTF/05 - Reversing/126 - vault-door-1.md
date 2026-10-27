## Descripción
This vault uses some complicated arrays! I hope you can make sense of it, special agent. The source code for this vault is here: VaultDoor1.java

## Solución
- Al examinar el archivo `VaultDoor1.java`, observamos que la contraseña se valida carácter por carácter comprobando cada posición mediante `password.charAt(indice) == 'caracter'`:
  ```java
  public boolean checkPassword(String password) {
      return password.length() == 32 &&
             password.charAt(0)  == 'd' &&
             password.charAt(29) == 'b' &&
             password.charAt(4)  == 'r' &&
             password.charAt(2)  == '5' &&
             password.charAt(23) == 'r' &&
             password.charAt(3)  == 'c' &&
             password.charAt(17) == '4' &&
             password.charAt(1)  == '3' &&
             password.charAt(7)  == 'b' &&
             password.charAt(10) == '_' &&
             password.charAt(5)  == '4' &&
             password.charAt(9)  == '3' &&
             password.charAt(11) == 't' &&
             password.charAt(15) == 'c' &&
             password.charAt(8)  == 'l' &&
             password.charAt(12) == 'H' &&
             password.charAt(20) == 'c' &&
             password.charAt(14) == '_' &&
             password.charAt(6)  == 'm' &&
             password.charAt(24) == '5' &&
             password.charAt(18) == 'r' &&
             password.charAt(13) == '3' &&
             password.charAt(19) == '4' &&
             password.charAt(21) == 'T' &&
             password.charAt(16) == 'H' &&
             password.charAt(27) == '5' &&
             password.charAt(30) == 'c' &&
             password.charAt(25) == '_' &&
             password.charAt(22) == '3' &&
             password.charAt(28) == 'e' &&
             password.charAt(26) == '4' &&
             password.charAt(31) == '3';
  }
  ```
- Para reconstruir la contraseña ordenada, podemos parsear las comparaciones usando una expresión regular en Python y ordenar los caracteres según su índice (0 a 31):
  ```python
  import re

  with open('VaultDoor1.java') as f:
      content = f.read()

  matches = re.findall(r"password\.charAt\((\d+)\)\s*==\s*'(.)'", content)
  chars = [None] * 32
  for idx, char in matches:
      chars[int(idx)] = char

  flag = 'academy{' + ''.join(chars) + '}'
  print(flag)
  ```
- Ejecutando el script obtenemos la contraseña: `d35cr4mbl3_tH3_cH4r4cT3r5_45ebc3`.
- Envolvieldo la clave en el formato de bandera `academy{...}`:
```text
academy{d35cr4mbl3_tH3_cH4r4cT3r5_45ebc3}
```

## Notas Adicionales
- La ofuscación de contraseñas mediante comprobaciones desordenadas de índices en cadenas de texto es trivialmente reversible reordenando los índices mediante scripting.
- Herramientas utilizadas:
  - Expresiones regulares (`re`) en Python para extraer pares `(índice, carácter)`.
  - Compilador de Java (`javac` y `java`) para validar el acceso.

## Referencias
- [Java String charAt() Method](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html#charAt(int))
