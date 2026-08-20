## Descripción
Sometimes you need to handle process data outside of a file. Can you find a way to keep the output from this program and search for the flag?
## Solución
Usé dos comandos a la vez para que me arroje solo la bandera
- El comando que usé: `nc fickle-tempest.picoctf.net 64367 | grep picoCTF`
```text
picoCTF{digital_plumb3r_00da27CC}
```
## Notas Adicionales
El comando grep es dios en este caso
## Referencias
[How to grep netcat output - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/176498/how-to-grep-netcat-output)