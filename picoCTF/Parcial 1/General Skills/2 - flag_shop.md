## Descripción
There's a flag shop selling stuff, can you buy a flag?
Archivo fuente: `store.c`
Conexión: `netcat

## Solución
- Analizamos el código fuente en C provisto en `store.c`
- El programa nos asigna un saldo inicial de 1100 (`account_balance = 1100`), mientras que la bandera auténtica ("1337 Flag") cuesta 100,000
- Al inspeccionar la función para comprar banderas falsas ("Defintely not the flag Flag"), identificamos una vulnerabilidad de desbordamiento de enteros (*Integer Overflow*):
  ```c
  int number_flags = 0;
  fflush(stdin);
  scanf("%d", &number_flags);
  if(number_flags > 0){
      int total_cost = 0;
      total_cost = 900*number_flags;
      printf("\nThe final cost is: %d\n", total_cost);
      if(total_cost <= account_balance){
          account_balance = account_balance - total_cost;
          printf("\nYour current balance after transaction: %d\n\n", account_balance);
      }
  ...
  ```
- Las variables `number_flags`, `total_cost` y `account_balance` son enteros de 32 bits con signo (`signed int`), cuyo valor máximo es `2147483647` (0x7FFFFFFF)
- Si ingresamos un número de banderas lo bastante grande para que `900 * number_flags` supere el límite superior de un entero con signo, el valor se desborda y se interpreta como un número negativo
- Al restarle un costo negativo al saldo (`account_balance = account_balance - total_cost`), el saldo en realidad se incrementa en lugar de disminuir
- Si compramos `3000000` banderas:
  - $900 \times 3000000 = 2700000000$
  - Al superar `2147483647`, el valor con signo se convierte en `-1594967296`
  - Nuevo saldo: $1100 - (-1594967296) = 1594968396$
- Nos conectamos al servicio con `nc localhost 3224`:
  1. Ingresamos `2` (Buy Flags)
  2. Ingresamos `1` (Defintely not the flag Flag)
  3. Ingresamos la cantidad `3000000`
  4. Observamos que el saldo se eleva a `1594968396`
  5. Volvemos al menú `2` (Buy Flags)
  6. Seleccionamos `2` (1337 Flag)
  7. Ingresamos `1` para comprarla
- El sistema comprueba que tenemos más de 100,000 fondos y nos revela la bandera

```text
academy{m0n3y_bag5_cbd0Cf8B}
```

## Notas Adicionales
- **Integer Overflow**: En la aritmética de enteros en C, cuando el resultado de una operación supera el valor máximo almacenable por el tipo de dato, se produce un desbordamiento circular (*wraparound*), haciendo que un número positivo muy grande se convierta en negativo.
- **Prevención**: Para prevenir desbordamientos aritméticos se deben validar los operandos antes de realizar el cálculo:
  ```c
  if (number_flags > INT_MAX / 900) {
      printf("Cantidad no permitida\n");
  }
  ```

## Referencias
- [CWE-190: Integer Overflow or Wraparound](https://cwe.mitre.org/data/definitions/190.html)
- [SEI CERT C Coding Standard - INT32-C](https://wiki.sei.cmu.edu/confluence/display/c/INT32-C.+Ensure+that+operations+on+signed+integers+do+not+result+in+overflow)