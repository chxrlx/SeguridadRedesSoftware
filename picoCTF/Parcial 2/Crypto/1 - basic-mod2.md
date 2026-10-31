## Descripción
A new modular challenge! Download the message here.

Take each number mod 41 and find the modular inverse for the result. Then map to the following character set: 1-26 are the alphabet, 27-36 are the decimal digits, and 37 is an underscore.

## Solución
- Leemos el contenido del archivo `message.txt`:
  ```text
  432 126 192 354 98 296 272 311 146 297 364 373 262 277 215 377 255 407 72 46 213 448 212
  ```
- Para cada número $n$ de la lista:
  1. Calculamos su residuo módulo 41: $x = n \bmod 41$.
  2. Obtenemos su inverso multiplicativo modular $x^{-1} \pmod{41}$, es decir, el número $y \in \{1, \dots, 40\}$ tal que $(x \cdot y) \equiv 1 \pmod{41}$.
  3. Mapeamos cada valor resultante $y$ al conjunto de caracteres indicado:
     - `1–26`: Alfabeto (`A-Z` / `a-z`)
     - `27–36`: Dígitos decimales (`0-9`)
     - `37`: Guion bajo (`_`)
- Implementamos el siguiente script en Python 3 empleando `pow(n, -1, 41)` para calcular el inverso modular directamente:
  ```python
  import string

  with open("message.txt") as f:
      nums = [int(x) for x in f.read().split()]

  charset_upper = " " + string.ascii_uppercase + string.digits + "_"
  charset_lower = " " + string.ascii_lowercase + string.digits + "_"

  inverses = [pow(n, -1, 41) for n in nums]

  flag_upper = f"academy{{{''.join(charset_upper[i] for i in inverses)}}}"
  flag_lower = f"academy{{{''.join(charset_lower[i] for i in inverses)}}}"

  print(" Secuencia de inversos:", inverses)
  print("Mayúsculas:", flag_upper)
  print("Minúsculas:", flag_lower)
  ```
- Los inversos modulares obtenidos son:
  ```text
  [28, 14, 22, 30, 18, 32, 30, 12, 25, 37, 8, 31, 18, 4, 37, 36, 32, 27, 4, 33, 36, 27, 6]
  ```
- Mapeando los valores al alfabeto y envolviendo el resultado en el formato `academy{}`:
```text
academy{1NV3R53LY_H4RD_950D690F}
```

## Notas Adicionales
- Dado que $41$ es un número primo, todo entero $x$ no divisible por $41$ ($1 \le x < 41$) es coprimo con $41$, garantizando la existencia y unicidad de su inverso multiplicativo módulo $41$ (calculable mediante el Algoritmo Extendido de Euclides o el Pequeño Teorema de Fermat: $x^{-1} \equiv x^{39} \pmod{41}$).
- Herramientas utilizadas:
  - Python 3 (`pow(base, -1, mod)` disponible de forma nativa desde Python 3.8).

## Referencias
- [Modular multiplicative inverse - Wikipedia](https://en.wikipedia.org/wiki/Modular_multiplicative_inverse)
- [Python Built-in Functions: pow()](https://docs.python.org/3/library/functions.html#pow)
