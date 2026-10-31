## Descripción
Our data got corrupted on the way here. Luckily, nothing got replaced, but every block of 3 got scrambled around! The first word seems to be three letters long, maybe you can use that to recover the rest of the message. Download the corrupted message here.

## Solución
- Leemos el contenido del archivo `message.txt`:
  ```text
  heTfl g as icaaemd{7y4NRP051N5_16_35P3X51N3_V575D318}F
  ```
- Analizando los primeros bloques de 3 caracteres (`heT`, `fl `, `g a`, `s i`, `caa`, `emd`), observamos que `heT` corresponde a la palabra `"The"`.
- Por lo tanto, en cada bloque de 3 caracteres $(c_0, c_1, c_2)$, el primer carácter original fue desplazado al final del bloque, de modo que para restaurar el orden original debemos aplicar la permutación $(c_2, c_0, c_1)$.
- Implementamos el siguiente script en Python para reordenar todos los bloques de 3 caracteres:
  ```python
  with open("message.txt") as f:
      data = f.read().strip()

  decrypted = "".join(
      data[i + 2] + data[i] + data[i + 1]
      for i in range(0, len(data), 3)
  )
  print(decrypted)
  ```
- Al ejecutar el script obtenemos el mensaje completo:
  ```text
  The flag is academy{7R4N5P051N6_15_3XP3N51V3_5571D3F8}
  ```
- Bandera extraída:
```text
academy{7R4N5P051N6_15_3XP3N51V3_5571D3F8}
```

## Notas Adicionales
- Los cifrados por transposición de bloques dividen el texto en claro en grupos de tamaño fijo $k$ y aplican una permutación constante $\pi$ sobre las posiciones dentro de cada bloque. Cuando $k=3$, solo existen $3! = 6$ permutaciones posibles, por lo que basta un único bloque conocido (como `"The"`) para determinar la permutación inversa $\pi^{-1}$.
- Herramientas utilizadas:
  - Python 3.

## Referencias
- [Transposition cipher - Wikipedia](https://en.wikipedia.org/wiki/Transposition_cipher)
