## Descripción
The name of the game is speed. Are you quick enough to solve this problem and keep it above 50 mph? need-for-speed

## Solución
- Al inspeccionar los símbolos y desensamblar el binario ELF x86-64 `need-for-speed` mediante `objdump -d -M intel need-for-speed`, identificamos la secuencia de ejecución en `main`:
  1. `header()`: Imprime el encabezado del programa.
  2. `set_timer()`: Configura el manejador `alarm_handler` para `SIGALRM` y establece un temporizador `alarm(1)` de 1 segundo que termina el proceso si se excede el tiempo.
  3. `get_key()`: Invoca `calculate_key()` y almacena el resultado en la variable global `key` (`0x405c`).
  4. `print_flag()`: Pasa `key` a `decrypt_flag(key)` para descifrar los bytes almacenados en `flag` (`0x4020`) y los imprime.
- Analizando `calculate_key()`:
  ```assembly
  0000000000001261 <calculate_key>:
      1269: c7 45 f8 1e 85 f3 dd    mov    DWORD PTR [rbp-0x8],0xddf3851e
      1270: c7 45 fc 00 00 00 00    mov    DWORD PTR [rbp-0x4],0x0
      1277: eb 18                   jmp    1291 <calculate_key+0x30>
      1279: c7 45 f8 3c 0a e7 bb    mov    DWORD PTR [rbp-0x8],0xbbe70a3c
      1280: 83 6d f8 01             sub    DWORD PTR [rbp-0x8],0x1
      1284: 81 7d f8 1e 85 f3 dd    cmp    DWORD PTR [rbp-0x8],0xddf3851e
      128b: 75 f3                   jne    1280 <calculate_key+0x1f>
      128d: 83 45 fc 01             add    DWORD PTR [rbp-0x4],0x1
      1291: 83 7d fc 07             cmp    DWORD PTR [rbp-0x4],0x7
      1295: 7e e2                   jle    1279 <calculate_key+0x18>
      1297: 8b 45 f8                mov    eax,DWORD PTR [rbp-0x8]
      129b: c3                      ret
  ```
- La función `calculate_key()` realiza 8 ciclos completos decrementando un entero de 32 bits sin signo desde `0xbbe70a3c` hasta `0xddf3851e` (más de 30 mil millones de iteraciones), provocando que se dispare `alarm(1)` antes de finalizar. Sin embargo, el valor final que siempre retorna es constante: `0xddf3851e`.
- Podemos resolverlo de dos formas:
  1. **Parcheo binario temporal:** Modificando el salto en `0x1277` (`eb 18` $\rightarrow$ `eb 1e`) para saltar directamente a `0x1297` y retornar `0xddf3851e` sin ejecutar el bucle de espera:
     ```python
     with open("need-for-speed", "rb") as f:
         data = bytearray(f.read())

     assert data[0x1277:0x1279] == b"\xeb\x18"
     data[0x1277:0x1279] = b"\xeb\x1e"

     with open("/tmp/nfs_patched", "wb") as f:
         f.write(data)
     ```
  2. **Reimplementación de `decrypt_flag` en Python:** Extrayendo los 55 bytes de la sección `.data` en `0x4020` (`objdump -s -j .data need-for-speed`) y aplicando el XOR con `key_bytes[i % 2]` incrementando `key` en 1 cuando `i % 3 == 2`:
     ```python
     import struct

     data = bytes.fromhex(
         "7fe67fe17ae859fe67ea4ee102ef4de7"
         "03ee41e054ec4be206e753f607a61ab0"
         "19e34ce04ee30af65be049e145eb4aa5"
         "4fe941eb48a44d00"
     )
     flag = bytearray(data)
     key = 0xddf3851e

     for i in range(0x37):
         flag[i] ^= struct.pack("<I", key)[i % 2]
         if i % 3 == 2:
             key = (key + 1) & 0xffffffff

     print(flag.split(b"\x00")[0].decode("ascii"))
     ```
- Obtenemos la bandera:
```text
academy{Good job keeping bus #251feedf speeding along!}
```

## Notas Adicionales
- Cuando un binario utiliza temporizadores (`alarm`) o bucles de retardo excesivos como mecanismo anti-análisis dinámico, el análisis estático y el parcheo de flujo de control permiten obtener el resultado instantáneamente sin necesidad de esperar o modificar la señal `SIGALRM`.
- Herramientas utilizadas:
  - `objdump`: Desensamblado de funciones y volcado de la sección `.data`.
  - Python 3: Parcheo temporal y reimplementación del algoritmo de descifrado.

## Referencias
- [objdump(1) - Linux manual page](https://man7.org/linux/man-pages/man1/objdump.1.html)
- [alarm(2) - Linux manual page](https://man7.org/linux/man-pages/man2/alarm.2.html)
