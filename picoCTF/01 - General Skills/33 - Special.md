## Descripción
Don't power users get tired of making spelling mistakes in the shell? Not anymore! Enter Special, the Spell Checked Interface for Affecting Linux. Now, every word is properly spelled and capitalized... automatically and behind-the-scenes! Be the first to test Special in beta, and feel free to tell us all about how Special streamlines every development process that you face. When your co-workers see your amazing shell interface, just tell them: That's Special (TM)
## Solución
- Al ejecutar cualquier comando al parecer nos convierte la primera letra en mayúscula, por ejemplo: si ejecutamos ls nos arroja que el comando Ls no existe
- Una vez que tengamos eso en cuenta, tenemos que engañar el script que está usando la terminal para que pase a un comando válido, en mi caso usé el comando: `a=1; cat */*` para que me diera la bandera
- ¿Por qué funciona? Porque en el comando primero hacemos una asignación que es válida en la terminal, si el script convierte la primera letra a mayúscula, entonces sería A=1 que sigue siendo válido para la terminal y ejecutará ese comando y el punto y coma sirve para decirle a la terminal que ejecute dos instrucciones independientes, así que procede a ejecutar el comando `cat */*` y este funciona porque el asterisco sirve como comodin, busca cualquier carpeta en el directorio actual (*) y lee todos los archivos que contenga en su interior (/*)
```text
picoCTF{5p311ch3ck_15_7h3_w0r57_f578af59}
```
## Notas Adicionales
## Referencias
[Cómo usar comodines en Linux/Unix: 6 ejemplos de *, ? | Warp](https://www-warp-dev.translate.goog/terminus/linux-wildcards?_x_tr_sl=en&_x_tr_tl=es&_x_tr_hl=es&_x_tr_pto=tc&_x_tr_hist=true)