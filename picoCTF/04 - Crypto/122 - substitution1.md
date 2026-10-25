## Descripción
A second message has come in the mail, and it seems almost identical to the first one. Maybe the same thing will work again. Download the message here.

## Solución
- El archivo `message.txt` contiene un texto cifrado mediante sustitución monoalfabética. A diferencia de `substitution0`, no incluye la clave de sustitución explícita en la cabecera, por lo que es necesario realizar criptoanálisis mediante análisis de frecuencia y reconocimiento de patrones contextuales.
- El texto cifrado comienza con:
  ```text
  TAHd (dqela hel tkfaclx aqx hzko) klx k abfx eh tevfcaxl dxtcliab tevfxaiaier...
  ```
- Por la estructura gramatical y el contexto de seguridad informática, identificamos fácilmente la frase introductoria:
  ```text
  CTFs (short for capture the flag) are a type of computer security competition...
  ```
- Este emparejamiento inicial proporciona la equivalencia para 17 caracteres (`t->c`, `a->t`, `h->f`, `d->s`, `q->h`, `e->o`, `l->r`, `k->a`, `f->p`, `c->u`, `x->e`, `z->l`, `o->g`, `b->y`, `v->m`, `i->i`, `r->n`).
- Al aplicar estas sustituciones sobre el resto del texto, deducimos las letras restantes a partir de palabras evidentes del idioma inglés:
  - `presenteu yith` $\rightarrow$ `presented with` (`u->d`, `y->w`)
  - `creatijity` $\rightarrow$ `creativity` (`j->v`)
  - `snills` $\rightarrow$ `skills` (`n->k`)
  - `proslem-soljing asility` $\rightarrow$ `problem-solving ability` (`s->b`)
  - `numser` $\rightarrow$ `number` (`s->b`)
  - `FR3MU3NCY` $\rightarrow$ `FR3QU3NCY` (`m->q`)
- Implementamos el script en Python con la tabla de sustitución completa deducida:
  ```python
  mapping = {
      't': 'c', 'a': 't', 'h': 'f', 'd': 's', 'q': 'h', 'e': 'o',
      'l': 'r', 'k': 'a', 'f': 'p', 'c': 'u', 'x': 'e', 'z': 'l',
      'o': 'g', 'b': 'y', 'v': 'm', 'i': 'i', 'r': 'n', 'u': 'd',
      'y': 'w', 'j': 'v', 'n': 'k', 's': 'b', 'm': 'q'
  }

  cipher_flag = 'ktkuxvb{HL3MC3RTB_4774TN5_4L3_T001_7S0043K0}'

  res = []
  for ch in cipher_flag:
      if ch.lower() in mapping:
          p = mapping[ch.lower()]
          res.append(p.upper() if ch.isupper() else p)
      else:
          res.append(ch)

  print(''.join(res))
  ```
- Al descifrar la última oración:
  ```text
  For this problem, the flag is: academy{FR3QU3NCY_4774CK5_4R3_C001_7B0043A0}
  ```
- Bandera obtenida:
```text
academy{FR3QU3NCY_4774CK5_4R3_C001_7B0043A0}
```

## Notas Adicionales
- El análisis de frecuencias es la técnica clásica para quebrar cifrados de sustitución monoalfabética aprovechando que la frecuencia de distribución de las letras en el texto cifrado coincide con la frecuencia estadística del idioma del texto en claro (en inglés: $E, T, A, O, I, N, S, H, R\dots$).
- Herramientas y técnicas utilizadas:
  - Análisis manual de n-gramas (bigramas y trigramas comunes como `the`, `and`, `ing`, `for`).
  - Python 3: Sustitución de caracteres monoalfabéticos mediante diccionario de equivalencias.

## Referencias
- [Frequency Analysis - Wikipedia](https://en.wikipedia.org/wiki/Frequency_analysis)
- [Practical Cryptography - Monoalphabetic Substitution Cipher](http://practicalcryptography.com/ciphers/classical-era/simple-substitution/)
