## Descripción
Can you get the flag? Download this binary.

Here's the test drive instructions:
```text
$ chmod +x gdbme
$ gdb gdbme
(gdb) layout asm
(gdb) break *(main+99)
(gdb) run
(gdb) jump *(main+104)
```

## Solución
- Al desensamblar la función `main` del binario `gdbme` mediante `objdump -d -M intel gdbme`, observamos por qué las instrucciones indican saltar de `main+99` a `main+104`:
  ```assembly
  00000000000012c7 <main>:
      ...
      1325: bf a0 86 01 00       mov    edi,0x186a0
      132a: e8 e1 fd ff ff       call   1110 <sleep@plt>   ; main+99 (0x12c7 + 99 = 0x132a)
      132f: 48 8d 45 d0          lea    rax,[rbp-0x30]    ; main+104 (0x12c7 + 104 = 0x132f)
      1333: 48 89 c6             mov    rsi,rax
      1336: bf 00 00 00 00       mov    edi,0x0
      133b: e8 c9 fe ff ff       call   1209 <rotate_encrypt>
      ...
  ```
- Antes de descifrar e imprimir la bandera mediante `rotate_encrypt`, el programa llama a `sleep(0x186a0)` (`100000` segundos $\approx 27.7$ horas).
- Existen dos formas principales de omitir esta espera:
  1. **Con GDB (siguiendo las instrucciones del reto):**
     ```bash
     chmod +x gdbme
     gdb gdbme
     (gdb) break *(main+99)
     (gdb) run
     (gdb) jump *(main+104)
     ```
  2. **Parcheo binario temporal (NOP-ing `call sleep@plt`):**
     Reemplazando los 5 bytes de la instrucción `call 1110 <sleep@plt>` en el desplazamiento `0x132a` por instrucciones `NOP` (`0x90`):
     ```python
     with open("gdbme", "rb") as f:
         data = bytearray(f.read())

     assert data[0x132a:0x132f] == b"\xe8\xe1\xfd\xff\xff"
     data[0x132a:0x132f] = b"\x90" * 5

     with open("/tmp/gdbme_patched", "wb") as f:
         f.write(data)
     ```
     Al ejecutar `/tmp/gdbme_patched`, el programa evita el `sleep` e imprime directamente la bandera:
```text
academy{d3bugg3r_dr1v3_ff5329f1}
```

## Notas Adicionales
- El comando `jump *address` de GDB permite modificar el registro del puntero de instrucción (`RIP` en x86-64) para continuar la ejecución desde una dirección arbitraria, omitiendo llamadas bloqueantes o comprobaciones condicionales en tiempo de ejecución.
- Herramientas utilizadas:
  - `objdump`: Desensamblado estático del binario ELF x86-64.
  - Python 3: Parcheo temporal de los 5 bytes de `call sleep@plt` en una copia en `/tmp` para ejecutar el binario sin esperas.

## Referencias
- [GDB Documentation - Continuing at a Different Address](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Jumping.html)
- [objdump(1) - Linux manual page](https://man7.org/linux/man-pages/man1/objdump.1.html)
