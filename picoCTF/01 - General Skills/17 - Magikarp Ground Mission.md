## Descripción
Do you know how to move between directories and read files in the shell? Start the container, `ssh` to it, and then `ls` once connected to begin.
## Solución
- Me conecté por ssh: ssh -p 64824 ctf-player@wily-courier.picoctf.net
- Usé comando ls para ver contenido
- Luego usé: `cat 1of3.flag.txt` luego leí las instrucciones: `cat instructions-to-2of3.txt`
- Ahora usé: `cd /`
- Vi la segunda parte de la bandera: `cat 2of3.flag.txt` y luego leí las siguientes instrucciones: `cat instructions-to-3of3.txt`
- Me moví de directorio: `cd ~`
- Obtuve la última parte de la bandera: `cat 3of3.flag.txt`
```text
picoCTF{xxsh_0ut_0f_//4t3r_0b24fc4f}
```
## Notas Adicionales
Con el comando `cd` nos movemos entre directorios en la terminal
## Referencias