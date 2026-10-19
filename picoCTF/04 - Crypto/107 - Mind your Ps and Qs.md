## Descripción
In RSA, a small e value can be problematic, but what about N? Can you decrypt this?

## Solución
- El archivo `values` proporciona los valores de un cifrado RSA:
  - $c$: Mensaje cifrado.
  - $n$: Módulo RSA (clave pública compartida).
  - $e = 65537$: Exponente público estándar.
- Observamos que el módulo $n$ es relativamente pequeño (~270 bits / 82 dígitos decimales), lo que lo hace vulnerable a ataques de factorización de enteros.
- Siguiendo la guía de `rsa.md`, consultamos la base de datos de factorización [FactorDB](https://factordb.com/) para descomponer $n$ en sus factores primos $p$ y $q$:
  $$n = p \cdot q$$
  Donde:
  - $p = 2074606527507983503944226222695401999839$
  - $q = 659880377875535146491994798968813949893149$
- Con los factores primos, calculamos la función indicatriz de Euler $\phi(n) = (p - 1)(q - 1)$ y obtenemos el exponente privado $d$:
  $$d \equiv e^{-1} \pmod{\phi(n)}$$
- Desencriptamos el mensaje calculando $m = c^d \pmod n$:
  ```python
  import urllib.request, json

  # Valores dados en el archivo values
  c = 717608817902276639011922897702552744324264244069272484013719744328086856277690292
  n = 1368992139315019955067447961537607041364451719382349005056752379702694554965203011
  e = 65537

  # Consulta a FactorDB para obtener los factores primos
  url = f'https://factordb.com/api?query={n}'
  req = urllib.request.Request(url, headers={'User-Agent': 'Mozilla/5.0'})
  resp = urllib.request.urlopen(req)
  data = json.loads(resp.read().decode())
  factors = [int(f[0]) for f in data['factors']]

  p, q = factors[0], factors[1]
  phi = (p - 1) * (q - 1)
  d = pow(e, -1, phi)
  m = pow(c, d, n)

  # Conversión del entero a bytes (orden little-endian)
  flag = m.to_bytes((m.bit_length() + 7) // 8, 'little').decode().strip()
  print(flag)
  ```
- Salida generada:
```text
academy{sma11_N_n0_g0od_a3ac7152}
```

## Notas Adicionales
- La seguridad del algoritmo RSA se fundamenta en la dificultad computacional de factorizar el producto de dos números primos grandes $n = p \cdot q$.
- Si el módulo $n$ es de longitud insuficiente (en este caso menos de 512 bits), puede ser factorizado rápidamente utilizando algoritmos como el Tamiz Cuadrático (QS) o el Tamiz General del Cuerpo de Números (GNFS), o consultando bases de datos de factores precalculados como FactorDB.
- Herramientas utilizadas:
  - Python 3: Consulta al API de FactorDB, cálculo del inverso modular con `pow(e, -1, phi)` y descifrado modular.
  - [FactorDB](https://factordb.com/): Factorización del módulo compuesto $n$.

## Referencias
- [FactorDB](https://factordb.com/)
- [RSA Cryptosystem Theory and Factorization Attacks (CTF Wiki)](https://ctf-wiki.org/crypto/asymmetric/rsa/rsa_theory/)
- [NIST Special Publication 800-57: Key Management Recommendations](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final)
