## Descripción
Oops! I have a typo in my first message so i sent it again! I used RSA twice so this is secure right? chall.py output.txt

## Solución
- Analizamos el archivo `chall.py`:
  ```python
  e = 0x11
  p = getPrime(1024)
  q = getPrime(1024)
  phi = (p-1) * (q-1)
  d = inverse(e, phi)
  N = p*q

  ciphertext = pow(Message, e, N)
  ciphertext2 = pow(Message_fixed, e, N)

  print(ciphertext, ciphertext2)
  print(Message - Message_fixed)
  print(N)
  ```
- Se nos proporcionan dos textos cifrados $c_1 \equiv M^e \pmod N$ y $c_2 \equiv M_{\text{fixed}}^e \pmod N$ bajo el mismo módulo $N$ y exponente público pequeño $e = 17$ (`0x11`), junto con su diferencia lineal conocida $\Delta = M - M_{\text{fixed}} = -3$.
- Este escenario es vulnerable al **Ataque de Mensajes Relacionados de Franklin-Reiter (Franklin-Reiter Related Message Attack)**:
  - Definimos los dos polinomios en el anillo $\mathbb{Z}_N[x]$:
    $$f_1(x) = (x + \Delta)^{17} - c_1 \pmod N$$
    $$f_2(x) = x^{17} - c_2 \pmod N$$
  - Dado que $x = M_{\text{fixed}}$ es raíz común de ambos polinomios módulo $N$, el máximo común divisor polinomial mónico $\gcd(f_1(x), f_2(x))$ en $\mathbb{Z}_N[x]$ es el polinomio lineal:
    $$g(x) = x - M_{\text{fixed}} \pmod N$$
- Implementamos el algoritmo de Euclides para polinomios sobre $\mathbb{Z}_N[x]$ en Python:
  ```python
  from math import comb

  with open("output.txt") as f:
      lines = [line.strip() for line in f if line.strip()]

  c1, c2, diff, N = map(int, lines)
  e = 17

  def trim(p, n):
      p = [x % n for x in p]
      while p and p[-1] == 0:
          p.pop()
      return p

  def poly_divmod(A, B, n):
      A, B = trim(A[:], n), trim(B[:], n)
      inv_lead = pow(B[-1], -1, n)
      deg_B = len(B) - 1
      while len(A) >= len(B) and A:
          deg_A = len(A) - 1
          factor = (A[-1] * inv_lead) % n
          shift = deg_A - deg_B
          for i in range(len(B)):
              A[i + shift] = (A[i + shift] - factor * B[i]) % n
          A = trim(A, n)
      return A

  def poly_gcd(A, B, n):
      while B:
          A, B = B, poly_divmod(A, B, n)
      inv_lead = pow(A[-1], -1, n)
      return [(x * inv_lead) % n for x in A]

  f1 = [(comb(e, k) * pow(diff, e - k, N)) % N for k in range(e + 1)]
  f1[0] = (f1[0] - c1) % N

  f2 = [0] * (e + 1)
  f2[e] = 1
  f2[0] = (-c2) % N

  g = poly_gcd(f1, f2, N)
  m_fixed = (-g[0]) % N
  m_orig = (m_fixed + diff) % N

  print("Original:", m_orig.to_bytes((m_orig.bit_length() + 7) // 8, "big").decode())
  print("Fixed:   ", m_fixed.to_bytes((m_fixed.bit_length() + 7) // 8, "big").decode())
  ```
- Al ejecutar el script obtenemos ambos mensajes (`academy{m3ssage_w1th_typ0z` y `academy{m3ssage_w1th_typ0}`), revelando la bandera corregida:
```text
academy{m3ssage_w1th_typ0}
```

## Notas Adicionales
- El ataque de Franklin-Reiter demuestra por qué nunca se debe usar RSA determinista ("textbook RSA") sin un esquema de relleno aleatorio como **RSA-OAEP**. Cuando dos mensajes guardan una relación polinomial conocida $M_1 = P(M_2)$ y el exponente público $e$ es moderadamente pequeño, el algoritmo de Euclides sobre $\mathbb{Z}_N[x]$ recupera el texto en claro en $O(e^2 \log^2 N)$ operaciones sin necesidad de factorizar $N$.
- Herramientas utilizadas:
  - Python 3 (aritmética polinomial modular sobre $\mathbb{Z}_N[x]$).

## Referencias
- [Coppersmith's attack / Franklin-Reiter related-message attack - Wikipedia](https://en.wikipedia.org/wiki/Coppersmith%27s_attack#Franklin%E2%80%93Reiter_related-message_attack)
- [Linear-Related Messages Attack on RSA - Franklin & Reiter (1996)](https://link.springer.com/chapter/10.1007/3-540-68339-9_1)
