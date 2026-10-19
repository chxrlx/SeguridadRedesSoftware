## Descripción
What happens if you have a small exponent? There is a twist though, we padded the plaintext so that M^e is just barely larger than N. Let's decrypt this: ciphertext.

## Solución
- El archivo `values` proporciona los parámetros RSA clásicos: el módulo $N$, el exponente público $e = 3$ y el mensaje cifrado $c$.
- Debido a que el exponente público $e$ es extremadamente pequeño ($e = 3$), si el mensaje $m$ tras elevarse a la potencia $e$ no supera el módulo $N$ (es decir, $m^3 < N$), no ocurre la reducción modular ($c = m^3 \pmod N \implies c = m^3$). En este caso, la recuperación del texto plano se reduce a calcular la raíz cúbica entera directa del criptograma:
  $$m = \sqrt[3]{c}$$
- Si el mensaje hubiese superado $N$ ligeramente por un múltiplo $k$, se cumpliría $m^3 = c + k \cdot N$, requiriendo iterar sobre valores pequeños de $k$.
- Implementamos un script en Python con búsqueda binaria para calcular la raíz cúbica exacta de números enteros de gran tamaño sin pérdida de precisión:
  ```python
  with open('values') as f:
      lines = f.read().splitlines()

  N = int(lines[0].split(':')[1].strip())
  e = int(lines[1].split(':')[1].strip())
  c = int(lines[3].split(':')[1].strip())

  def integer_cuberoot(n, k=3):
      u = 1 << ((n.bit_length() + k - 1) // k)
      l = u >> 1
      while l <= u:
          m = (l + u) // 2
          p = m ** k
          if p == n:
              return m, True
          elif p < n:
              l = m + 1
          else:
              u = m - 1
      return u, u ** k == n

  # Verificar raíz cúbica directa (k=0)
  root, exact = integer_cuberoot(c, 3)
  if exact:
      h = hex(root)[2:]
      if len(h) % 2:
          h = '0' + h
      print(bytes.fromhex(h).decode())
  else:
      for k in range(1, 100000):
          val = c + k * N
          root, exact = integer_cuberoot(val, 3)
          if exact:
              h = hex(root)[2:]
              if len(h) % 2:
                  h = '0' + h
              print(bytes.fromhex(h).decode())
              break
  ```
- Al calcular la raíz cúbica directa para $k = 0$, se obtiene la bandera con formato limpio:
```text
academy{e_sh0u1d_b3_lArg3r_35f26e1a}
```

## Notas Adicionales
- La vulnerabilidad de exponente público pequeño (Small Public Exponent Attack) ocurre cuando se elige un valor bajo como $e = 3$ sin un esquema de relleno probabilístico adecuado (como OAEP). Si el mensaje $m$ es corto o el módulo $N$ es grande, $m^e < N$ o $m^e$ supera $N$ por un múltiplo $k$ muy pequeño, permitiendo recuperar el texto en claro en tiempo polinomial.
- Herramientas y conceptos utilizados:
  - Python 3: Algoritmo de búsqueda binaria para cálculo de raíz cúbica entera en enteros de precisión arbitraria.
  - Criptografía asimétrica RSA y análisis de debilidad en claves públicas con bajo exponente.

## Referencias
- [Twenty Years of Attacks on the RSA Cryptosystem (Dan Boneh)](https://crypto.stanford.edu/~dabo/papers/RSA-survey.pdf)
- [RSA Low Public Exponent Attack (CryptoHack / CTF Wiki)](https://ctf-wiki.org/crypto/asymmetric/rsa/rsa_theory/)
- [Python Arbitrary-Precision Integers Documentation](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex)
