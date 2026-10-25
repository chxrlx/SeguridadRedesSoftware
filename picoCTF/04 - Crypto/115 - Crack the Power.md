## Descripción
We received an encrypted message. The modulus is built from primes large enough that factoring them isn’t an option, at least not today. See if you can make sense of the numbers and reveal the flag. Download the message

## Solución
- El archivo `message.txt` contiene los parámetros de un cifrado RSA:
  - Un módulo $n$ de 4095 bits (demasiado grande para factorizar).
  - Un exponente público $e = 20$.
  - Un texto cifrado $c$ de 3813 bits.
- Al comparar la longitud de bits de $c$ y $n$, observamos que $c < n$. Esto indica que el mensaje en texto plano $m$, al ser elevado a la potencia $e = 20$, no superó el valor del módulo $n$ ($m^{20} < n$).
- Como se indica en la guía `rsa.md` para ataques con exponente pequeño (Small Exponent Attack), al cumplirse $m^e < n$, la operación de reducción modular nunca tuvo efecto en el cifrado:
  $$c \equiv m^e \pmod n \implies c = m^e = m^{20}$$
- Por lo tanto, el texto plano se puede recuperar directamente calculando la raíz 20-ésima entera exacta del criptograma $c$:
  $$m = \sqrt[20]{c}$$
- Implementamos el siguiente script en Python utilizando búsqueda binaria sobre enteros de precisión arbitraria para calcular la raíz exacta y decodificar el mensaje:
  ```python
  with open('message.txt') as f:
      lines = f.read().splitlines()

  n = int(lines[0].split('=')[1].strip())
  e = int(lines[1].split('=')[1].strip())
  c = int(lines[2].split('=')[1].strip())

  # Función de raíz e-ésima entera exacta mediante búsqueda binaria
  def integer_root(val, k):
      u = 1 << ((val.bit_length() + k - 1) // k)
      l = u >> 1
      while l <= u:
          mid = (l + u) // 2
          p = mid ** k
          if p == val:
              return mid, True
          elif p < val:
              l = mid + 1
          else:
              u = mid - 1
      return u, u ** k == val

  m, exact = integer_root(c, e)
  if exact:
      h = hex(m)[2:]
      if len(h) % 2:
          h = '0' + h
      flag = bytes.fromhex(h).decode()
      print(flag)
  ```
- Al ejecutar el script obtenemos la bandera:
```text
academy{t1ny_e_d6b18b0b}
```

## Notas Adicionales
- La vulnerabilidad de exponente público pequeño (Small Public Exponent Attack) se produce cuando se utiliza un exponente bajo (en este caso $e = 20$) en combinación con un módulo grande y sin esquemas de relleno probabilístico (padding) como OAEP. Al no envolver el módulo modularmente, la seguridad de RSA se rompe y el problema de resolver el logaritmo/raíz modular se convierte en un simple cálculo de raíz aritmética en los enteros $\mathbb{Z}$.
- Herramientas y conceptos utilizados:
  - Python 3: Aritmética de enteros de tamaño arbitrario y algoritmo de búsqueda binaria para extracción de raíces exactas.
  - Criptoanálisis de RSA: Ataque de bajo exponente sin módulo efectivo ($m^e < n$).

## Referencias
- [Twenty Years of Attacks on the RSA Cryptosystem (Dan Boneh)](https://crypto.stanford.edu/~dabo/papers/RSA-survey.pdf)
- [RSA Low Public Exponent Attack (CTF Wiki)](https://ctf-wiki.org/crypto/asymmetric/rsa/rsa_theory/)
- [Python int arbitrary precision integer math](https://docs.python.org/3/library/stdtypes.html#numeric-types-int-float-complex)
