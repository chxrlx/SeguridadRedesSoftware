## Descripción
This vault uses for-loops and byte arrays. The source code for this vault is here: VaultDoor3.java

## Solución
- Analizando el método `checkPassword` en `VaultDoor3.java`:
  ```java
  public boolean checkPassword(String password) {
      if (password.length() != 32) {
          return false;
      }
      char[] buffer = new char[32];
      int i;
      for (i=0; i<8; i++) {
          buffer[i] = password.charAt(i);
      }
      for (; i<16; i++) {
          buffer[i] = password.charAt(23-i);
      }
      for (; i<32; i+=2) {
          buffer[i] = password.charAt(46-i);
      }
      for (i=31; i>=17; i-=2) {
          buffer[i] = password.charAt(i);
      }
      String s = new String(buffer);
      return s.equals("jU5t_a_sna_3lpm15ge42_u_4_mbr248");
  }
  ```
- El algoritmo genera una permutación del arreglo `password` en `buffer` de longitud 32 a través de cuatro bucles:
  1. $0 \le i < 8$: `buffer[i] = password[i]`
  2. $8 \le i < 16$: `buffer[i] = password[23 - i]` $\implies$ `password[23 - i] = buffer[i]`
  3. $i \in \{16, 18, \dots, 30\}$: `buffer[i] = password[46 - i]` $\implies$ `password[46 - i] = buffer[i]`
  4. $i \in \{31, 29, \dots, 17\}$: `buffer[i] = password[i]` $\implies$ `password[i] = buffer[i]`
- Para revertir la transformación y recuperar `password`, mapeamos los caracteres de `s` a sus posiciones originales en un script de Python:
  ```python
  s = "jU5t_a_sna_3lpm15ge42_u_4_mbr248"
  password = [None] * 32

  # 1. 0 a 7
  for i in range(8):
      password[i] = s[i]

  # 2. 8 a 15
  for i in range(8, 16):
      password[23 - i] = s[i]

  # 3. 16 a 31 (pares)
  for i in range(16, 32, 2):
      password[46 - i] = s[i]

  # 4. 31 a 17 (impares)
  for i in range(31, 16, -2):
      password[i] = s[i]

  flag = "academy{" + "".join(password) + "}"
  print(flag)
  ```
- Al ejecutar el script obtenemos el texto claro `jU5t_a_s1mpl3_an4gr4m_4_u_2be258` y la bandera correspondiente:
```text
academy{jU5t_a_s1mpl3_an4gr4m_4_u_2be258}
```

## Notas Adicionales
- El algoritmo aplica una transposición o permutación determinista sin pérdida de caracteres (un anagrama). Dado que se conocen los índices de origen y destino de cada posición, la función es biyectiva y se invierte asignando directamente cada posición de salida a su índice original de entrada.
- Herramientas utilizadas:
  - Python 3 para el script de inversión de la permutación.
  - Compilador de Java (`javac` y `java`) para validar el resultado con la aplicación.

## Referencias
- [Java char Array to String](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html#%3Cinit%3E(char%5B%5D))
