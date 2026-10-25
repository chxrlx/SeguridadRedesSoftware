## Descripción
This service provides you an encrypted flag. Can you decrypt it with just N & e?

## Solución
- Nos conectamos a la instancia local del reto mediante `nc` para obtener los valores emitidos por el servicio:
  ```bash
  nc chatelaine.cylabacademy.net 47845
  ```
  Salida recibida:
  ```text
  N: 19073839585117321477763107606529018602524862761711184824088780507487072836246880691935603459525100920151354788733725449078111006752176304707802539058562598
  e: 65537
  cyphertext: 7892990860880331984067103785602565006975126345608965176393329110902803475163678202224361646425683805882295246595129590964678584716609152542619913587974095
  ```
- Como sugiere el título del reto (*"EVEN RSA"*), observamos el último dígito del módulo $N$ y confirmamos que es un número **par** ($N \pmod 2 = 0$).
- En RSA, $N = p \cdot q$ debe componerse del producto de dos números primos impares muy grandes. Si $N$ es par, necesariamente uno de sus factores primos es $p = 2$.
- Por lo tanto, el otro factor primo $q$ se obtiene directamente por división entera:
  $$q = \frac{N}{2}$$
- La función de Euler $\phi(N)$ se simplifica a:
  $$\phi(N) = (p - 1)(q - 1) = (2 - 1)(q - 1) = q - 1$$
- Calculamos el exponente privado $d \equiv e^{-1} \pmod{\phi(N)}$ y desciframos el mensaje $m \equiv c^d \pmod N$:
  ```python
  N = 19073839585117321477763107606529018602524862761711184824088780507487072836246880691935603459525100920151354788733725449078111006752176304707802539058562598
  e = 65537
  c = 7892990860880331984067103785602565006975126345608965176393329110902803475163678202224361646425683805882295246595129590964678584716609152542619913587974095

  # Factorización trivial de N par
  p = 2
  q = N // 2
  phi = (p - 1) * (q - 1)  # phi = q - 1

  # Exponente privado y descifrado
  d = pow(e, -1, phi)
  m = pow(c, d, N)

  flag = m.to_bytes((m.bit_length() + 7) // 8, 'big').decode()
  print(flag)
  ```
- Salida generada:
```text
academy{tw0_1$_pr!m3207f407f}
```

## Notas Adicionales
- El número 2 es el único número primo par. El uso de $p = 2$ rompe por completo la seguridad de RSA, ya que la paridad de un número se comprueba de forma inmediata ($O(1)$) mediante el bit menos significativo, permitiendo hallar $q = N / 2$ sin requerir algoritmos de factorización.
- Herramientas utilizadas:
  - `nc` (netcat): Conexión al servicio de la instancia.
  - Python 3: Comprobación de paridad, cálculo del inverso modular con `pow(e, -1, phi)` y descifrado de RSA.

## Referencias
- [RSA (cryptosystem) - Key Generation](https://en.wikipedia.org/wiki/RSA_(cryptosystem)#Key_generation)
- [Euler's totient function](https://en.wikipedia.org/wiki/Euler%27s_totient_function)
