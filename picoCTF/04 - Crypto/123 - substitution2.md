## Descripción
It seems that another encrypted message has been intercepted. The encryptor seems to have learned their lesson though and now there isn't any punctuation! Can you still crack the cipher? Download the message here.

## Solución
- El archivo `message.txt` contiene un texto continuo en minúsculas sin signos de puntuación ni espacios, cifrado mediante sustitución monoalfabética.
- Hacia el final del mensaje, reconocemos la estructura típica que precede a la bandera:
  ```text
  ...uxclimrtgureroljb{A6S4J_4A41B515_15_73O10V5_IO56E357}
  ```
- El fragmento `uxclimrtgureroljb` corresponde al texto conocido `theflagisacademy`:
  - `u` $\rightarrow$ `s`
  - `x` $\rightarrow$ `t`
  - `c` $\rightarrow$ `h`
  - `l` $\rightarrow$ `e`
  - `i` $\rightarrow$ `f`
  - `m` $\rightarrow$ `l`
  - `r` $\rightarrow$ `a`
  - `t` $\rightarrow$ `g`
  - `g` $\rightarrow$ `i`
  - `e` $\rightarrow$ `c`
  - `o` $\rightarrow$ `d`
  - `j` $\rightarrow$ `m`
  - `b` $\rightarrow$ `y`
- Con esta correspondencia inicial de 13 letras, sustituimos parcialmente el texto y completamos el resto del alfabeto analizando palabras en inglés (`school`, `computer`, `competitions`, `security`, `established`, `offensive`, etc.):
  - `p` $\rightarrow$ `o`
  - `v` $\rightarrow$ `u`
  - `y` $\rightarrow$ `p`
  - `a` $\rightarrow$ `n`
  - `w` $\rightarrow$ `b`
  - `k` $\rightarrow$ `w`
  - `s` $\rightarrow$ `r`
  - `z` $\rightarrow$ `k`
  - `n` $\rightarrow$ `v`
  - `h` $\rightarrow$ `x`
- Se implementó el siguiente script en Python para aplicar la sustitución y recuperar la bandera:
  ```python
  mapping = {
      'x': 't', 'c': 'h', 'l': 'e', 'i': 'f', 'm': 'l', 'r': 'a',
      't': 'g', 'g': 'i', 'u': 's', 'e': 'c', 'o': 'd', 'j': 'm', 'b': 'y',
      'p': 'o', 'v': 'u', 'y': 'p', 'a': 'n', 'w': 'b', 'k': 'w', 's': 'r',
      'z': 'k', 'n': 'v', 'h': 'x'
  }

  cipher_flag = 'A6S4J_4A41B515_15_73O10V5_IO56E357'
  flag_inner = []
  for ch in cipher_flag:
      if ch.lower() in mapping:
          p = mapping[ch.lower()]
          flag_inner.append(p.upper() if ch.isupper() else p)
      else:
          flag_inner.append(ch)

  print(f"academy{{{''.join(flag_inner)}}}")
  ```
- Salida obtenida:
```text
academy{N6R4M_4N41Y515_15_73D10U5_FD56C357}
```

## Notas Adicionales
- Aunque se eliminen los espacios y la puntuación para dificultar la segmentación de palabras, la sustitución monoalfabética sigue siendo vulnerable debido a que:
  1. La identificación de palabras clave fijas (como `theflagisacademy`) revela directamente la mitad del alfabeto.
  2. Las frecuencias relativas de las letras y n-gramas siguen revelando las demás correspondencias.
- Herramientas y técnicas utilizadas:
  - Criptoanálisis de texto en claro conocido (Known-Plaintext Attack parcial en `theflagisacademy`).
  - Python 3: Sustitución de caracteres monoalfabéticos mediante diccionario de correspondencias.

## Referencias
- [Known-Plaintext Attack - Wikipedia](https://en.wikipedia.org/wiki/Known-plaintext_attack)
- [Frequency Analysis and N-gram Statistics](http://practicalcryptography.com/cryptanalysis/letter-frequencies-various-languages/english-letter-frequencies/)
