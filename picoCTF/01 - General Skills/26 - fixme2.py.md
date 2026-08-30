## Descripción
Fix the syntax error in the Python script to print the flag.
## Solución
- Ejecutamos el archivo: `python fixme2.py`
- Nos da el siguiente error:
  `File "/home/CharlyRifa-academy/fixme2.py", line 22`
    `if flag = "":`
       `^^^^^^^^^`
- Es un error de sintaxis y es debido a que está usando solo un signo de = (o sea, una asignación) en una estructura de control donde es necesario usar dos símbolos == para comparar
- Usamos el comando: `nano fixme2.py` para editar el archivo y agregamos el signo = faltante en la estructura de control y queda resuelto
```text
picoCTF{3qu4l1ty_n0t_4551gnm3nt_4863e11b}
```
## Notas Adicionales
En las estructuras de control como: if, elif, else, etc. Se necesita usar == para indicar que hará una comparación de valores y determinar si es true o false
## Referencias