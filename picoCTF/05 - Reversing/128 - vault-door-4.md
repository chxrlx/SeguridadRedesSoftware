## Descripción
This vault uses ASCII encoding for the password. The source code for this vault is here: VaultDoor4.java

## Solución
- Al analizar el método `checkPassword` en `VaultDoor4.java`, se observa una matriz de 32 bytes expresados en cuatro bases y formatos numéricos distintos:
  ```java
  public boolean checkPassword(String password) {
      byte[] passBytes = password.getBytes();
      byte[] myBytes = {
          106 , 85  , 53  , 116 , 95  , 52  , 95  , 98  ,
          0x55, 0x6e, 0x43, 0x68, 0x5f, 0x30, 0x66, 0x5f,
          0142, 0131, 0164, 063 , 0163, 0137, 070 , 066 ,
          'c' , '8' , '4' , '3' , 'f' , 'd' , '1' , '0' ,
      };
      for (int i=0; i<32; i++) {
          if (passBytes[i] != myBytes[i]) {
              return false;
          }
      }
      return true;
  }
  ```
- Cada fila representa 8 caracteres en un sistema numérico específico:
  1. **Decimal (base 10):**
     - `106, 85, 53, 116, 95, 52, 95, 98` $\rightarrow$ `'j'`, `'U'`, `'5'`, `'t'`, `'_'`, `'4'`, `'_'`, `'b'`
  2. **Hexadecimal (base 16, prefijo `0x`):**
     - `0x55, 0x6e, 0x43, 0x68, 0x5f, 0x30, 0x66, 0x5f` $\rightarrow$ `'U'`, `'n'`, `'C'`, `'h'`, `'_'`, `'0'`, `'f'`, `'_'`
  3. **Octal (base 8, prefijo `0` en Java):**
     - `0142, 0131, 0164, 063, 0163, 0137, 070, 066` $\rightarrow$ `'b'`, `'Y'`, `'t'`, `'3'`, `'s'`, `'_'`, `'8'`, `'6'`
  4. **Literales de caracteres ASCII:**
     - `'c', '8', '4', '3', 'f', 'd', '1', '0'` $\rightarrow$ `'c'`, `'8'`, `'4'`, `'3'`, `'f'`, `'d'`, `'1'`, `'0'`
- Concatenando los bytes convertidos a caracteres ASCII obtenemos:
  ```python
  b1 = [106 , 85  , 53  , 116 , 95  , 52  , 95  , 98]
  b2 = [0x55, 0x6e, 0x43, 0x68, 0x5f, 0x30, 0x66, 0x5f]
  b3 = [0o142, 0o131, 0o164, 0o63 , 0o163, 0o137, 0o70 , 0o66]
  b4 = [ord(c) for c in ['c', '8', '4', '3', 'f', 'd', '1', '0']]

  password = bytes(b1 + b2 + b3 + b4).decode('ascii')
  flag = f"academy{{{password}}}"
  print(flag)
  ```
- El resultado es la contraseña `jU5t_4_bUnCh_0f_bYt3s_86c843fd10`, lo que genera la bandera:
```text
academy{jU5t_4_bUnCh_0f_bYt3s_86c843fd10}
```

## Notas Adicionales
- En Java y en la sintaxis heredada de C, un número entero que comienza con `0` (sin la letra `x` o `b`) representa un literal en base 8 (octal).
- Herramientas utilizadas:
  - Intérprete interactivo de Python 3 para decodificar los arreglos en diferentes bases numéricas.
  - Compilador de Java (`javac` y `java`) para verificar el acceso concedido.

## Referencias
- [Java Integer Literals (JLS 3.10.1)](https://docs.oracle.com/javase/specs/jls/se17/html/jls-3.html#jls-3.10.1)
- [ASCII Table](https://www.asciitable.com/)
