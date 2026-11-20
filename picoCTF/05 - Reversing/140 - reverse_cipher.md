## Descripción
We have recovered a binary and a text file. Can you reverse the flag.

## Solución
- El archivo `rev_this` contiene la cadena cifrada de 24 caracteres:
  ```text
  academy{w1{1wq8684;3g5k}
  ```
- Desensamblamos el binario ELF `rev` con `objdump -d -M intel rev` para analizar la lógica de cifrado dentro de la función `main`:
  1. Lee 24 bytes (`0x18`) desde el archivo de entrada (`flag.txt`) hacia el búfer `[rbp-0x50]`.
  2. Primer bucle (`0 <= i <= 7`): Copia los primeros 8 bytes sin modificar al archivo de salida (`academy{`).
  3. Segundo bucle (`8 <= i <= 22`, es decir `0x8 <= i <= 0x16`):
     ```assembly
     1259: 8b 45 f4             mov    eax,DWORD PTR [rbp-0xc]
     125c: 83 e0 01             and    eax,0x1
     125f: 85 c0                test   eax,eax
     1261: 75 0c                jne    126f <main+0xea>
     1263: 0f b6 45 ff          movzx  eax,BYTE PTR [rbp-0x1]
     1267: 83 c0 05             add    eax,0x5       ; si i es par: +5
     126a: 88 45 ff             mov    BYTE PTR [rbp-0x1],al
     126d: eb 0a                jmp    1279 <main+0xf4>
     126f: 0f b6 45 ff          movzx  eax,BYTE PTR [rbp-0x1]
     1273: 83 e8 02             sub    eax,0x2       ; si i es impar: -2
     1276: 88 45 ff             mov    BYTE PTR [rbp-0x1],al
     ```
     - Si el índice `i` es **par**, suma `5` al valor ASCII del carácter (`+ 0x5`).
     - Si el índice `i` es **impar**, resta `2` al valor ASCII del carácter (`- 0x2`).
  4. Último byte (`i = 23`, ubicado en `[rbp-0x39]`): Se escribe sin modificaciones (`}`).
- Para revertir el algoritmo, aplicamos las operaciones inversas sobre los índices `8` a `22` de `rev_this` mediante Python:
  ```python
  with open("rev_this", "r") as f:
      enc = f.read().strip()

  res = list(enc)
  for i in range(8, 23):
      if i % 2 == 0:
          res[i] = chr(ord(enc[i]) - 5)
      else:
          res[i] = chr(ord(enc[i]) + 2)

  flag = "".join(res)
  print(flag)
  ```
- Al ejecutar el script (y verificar ejecutando `./rev` sobre la bandera recuperada, obteniendo un archivo `rev_this` idéntico byte a byte), obtenemos la bandera:
```text
academy{r3v3rs383665b7f}
```

## Notas Adicionales
- Se trata de un cifrado de sustitución posicional simple en memoria que alterna dos desplazamientos constantes según la paridad del índice (`+5` en posiciones pares, `-2` en impares) manteniendo intactos el prefijo `academy{` y el sufijo `}`.
- Herramientas utilizadas:
  - `objdump`: Desensamblado de la función `main` del binario `rev`.
  - Python 3: Implementación de la función inversa para descifrar `rev_this`.

## Referencias
- [objdump(1) - Linux manual page](https://man7.org/linux/man-pages/man1/objdump.1.html)
