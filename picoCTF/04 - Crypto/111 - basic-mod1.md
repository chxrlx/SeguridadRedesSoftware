## Descripción

We found this weird message being passed around on the servers, we think we have a working decryption scheme. Download the message here.

Take each number mod 37 and map it to the following character set: 0-25 is the alphabet (uppercase), 26-35 are the decimal digits, and 36 is an underscore.

Wrap your decrypted message in the academy flag format (i.e. `academy{decrypted_message}`).

## Solución

El archivo contiene una secuencia de números:

```text
91 396 316 198 225 369 50 369 350 63 205 346 299 406 330 397 335 374 71 111 179 64
```

Reducimos cada número módulo 37 y usamos el conjunto de caracteres:

```python
characters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789_"
```

Los valores obtenidos son:

```text
17 26 20 13 3 36 13 36 17 26 20 13 3 36 34 27 2 4 34 0 31 27
```

El mapeo produce el mensaje:

```text
R0UND_N_R0UND_81CE8A51
```

Script reproducible:

```python
with open("message.txt") as file:
    numbers = [int(value) for value in file.read().split()]

characters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789_"
decrypted = "".join(characters[number % 37] for number in numbers)

print(decrypted)
print(f"academy{{{decrypted.lower()}}}")
```

La bandera es:

```text
academy{r0und_n_r0und_81ce8a51}
```

## Notas Adicionales

- Los índices `0` a `25` corresponden a `A` a `Z`.
- Los índices `26` a `35` corresponden a los dígitos `0` a `9`.
- El índice `36` corresponde al guion bajo (`_`).
- Aunque el texto descifrado se muestra en mayúsculas por la tabla del reto, la bandera se presenta en minúsculas dentro del formato habitual de estos retos.

## Referencias
