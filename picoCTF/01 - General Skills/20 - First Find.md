## Descripción
Unzip this archive and find the file named 'uber-secret.txt'
## Solución
- Descargamos el archivo
- Descomprimimos el archivo: `unzip files.zip`
- Usamos el siguiente comando para encontrar el archivo que nos piden: `find files -name "uber-secret.txt"`
- Con la ruta que nos arrojó, usaremos el siguiente comando para que nos de la bandera: `cat files/adequate_books/more_books/.secret/deeper_secrets/deepest_secrets/uber-secret.txt`
```text
picoCTF{f1nd_15_f457_ab443fd1}
```
## Notas Adicionales
La estructura que usé para el comando find fue: find <ruta_directorio> -name <nombre_archivo>
## Referencias
[Linux find | Cómo usar el comando find de Linux - IONOS México](https://www.ionos.mx/digitalguide/servidores/configuracion/comando-linux-find/)