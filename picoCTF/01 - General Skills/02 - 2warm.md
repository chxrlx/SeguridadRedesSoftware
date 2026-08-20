
## Descripción
Can you convert the number 42 (base 10) to binary (base 2)?
## Solución
Convertir el número 42 con base 10 a su representación binaria base 2
- Podemos hacer una función en python para convertir el número 42 a binario
```python
def decimal_a_binario(n: int) -> str:

    if n == 0:

        return "0"

    es_negativo = n < 0

    n = abs(n)

    bits = []

  

    while n > 0:

        bits.append(str(n % 2))

        n //= 2

  

    resultado = "".join(reversed(bits))

    return "-" + resultado if es_negativo else resultado

  

print(decimal_a_binario(42))
```

```text
picoCTF{101010}
```

## Notas Adicionales
Puede usarse una página para convertir el número 42 a binario.
## Referencias
[Base 10 to Base 2 converter](https://math.tools/calculator/base/10-2)