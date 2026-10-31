## Descripción
Everything seems secure; strong numbers, familiar parameters but something small might ruin it all. Can you recover the message? Download the message. And source code

## Solución
- Al analizar el código fuente en `encryption.py`, observamos cómo se generan los parámetros RSA:
  ```python
  # Generate two large primes (1048 bits each)
  p = getPrime(1048)
  q = getPrime(1048)
  n = p * q
  phi = (p - 1) * (q - 1)

  # compute d
  d = getPrime(256)

  # Compute the public exponent
  e = inverse(d, phi)
  ```
- Mientras que el módulo $n = p \cdot q$ tiene un tamaño de $\approx 2096$ bits, el exponente privado $d$ fue elegido de tan solo $256$ bits ($d < \frac{1}{3} n^{1/4}$, dado que $n^{1/4} \approx 2^{524}$).
- Esto hace que el sistema sea vulnerable al **Ataque de Wiener (Wiener's Attack)** mediante expansión en fracciones continuas de $\frac{e}{n}$.
  - Dado que $e \cdot d - k \cdot \varphi(n) = 1$, al dividir por $d \cdot \varphi(n)$ y aproximar $\varphi(n) \approx n$:
    $$\left| \frac{e}{n} - \frac{k}{d} \right| < \frac{1}{2d^2}$$
  - Por el teorema de Legendre sobre aproximaciones diofánticas, la fracción $\frac{k}{d}$ aparece necesariamente entre los convergentes de la fracción continua de $\frac{e}{n}$.
- Implementamos el ataque de Wiener en Python:
  ```python
  with open("message.txt") as f:
      vals = dict(line.strip().split(" = ") for line in f)

  n = int(vals["n"])
  e = int(vals["e"])
  c = int(vals["c"])

  def cf_expansion(num, den):
      res = []
      while den:
          q = num // den
          res.append(q)
          num, den = den, num - q * den
      return res

  def convergents(cf):
      convs = []
      h0, h1 = 0, 1
      k0, k1 = 1, 0
      for q in cf:
          h = q * h1 + h0
          k = q * k1 + k0
          convs.append((h, k))
          h0, h1 = h1, h
          k0, k1 = k1, k
      return convs

  for k, d in convergents(cf_expansion(e, n)):
      if k == 0 or (e * d - 1) % k != 0:
          continue
      m = pow(c, d, n)
      b = m.to_bytes((m.bit_length() + 7) // 8, "big")
      if b.startswith(b"academy{"):
          print("d =", d)
          print(b.decode("ascii"))
          break
  ```
- Al ejecutar el script recuperamos el exponente privado $d = 98884499117184943324257581219011102082886360413844675438543286862305004210131$ y desciframos la bandera:
```text
academy{sm4ll_d_073963cb}
```

## Notas Adicionales
- En RSA no se debe elegir un exponente privado $d$ pequeño para acelerar el descifrado o la generación de firmas. El teorema de Michael J. Wiener (1990) demuestra que si $p < q < 2p$ y $d < \frac{1}{3} n^{1/4}$, el exponente privado $d$ se recupera en tiempo polinomial mediante fracciones continuas (y el ataque de Boneh-Durfee extiende este límite hasta $d < n^{0.292}$).
- En su lugar, la práctica estándar consiste en fijar un exponente público corto como $e = 65537$ y emplear el Teorema Chino del Resto (CRT-RSA) para optimizar la velocidad de descifrado sin reducir el tamaño de $d$.
- Herramientas utilizadas:
  - Python 3 (expansión en fracciones continuas y convergentes).

## Referencias
- [Wiener's attack - Wikipedia](https://en.wikipedia.org/wiki/Wiener%27s_attack)
- [Cryptanalysis of Short RSA Secret Exponents - Michael J. Wiener (1990)](https://ieeexplore.ieee.org/document/54902)
