## Descripción
There is a nice program that you can talk to by using this command in a shell:

$ nc wily-courier.picoctf.net 61060, but it doesn't speak English...
## Solución
Cuando me conecto usando netcat usando esos datos, me devuelve un montón de números, que están en decimal, así que tengo que convertirlos a ASCII usando cyberchef
```text
picoCTF{g00d_k1tty!_n1c3_k1tty!_a94e7}
```
## Notas Adicionales
Puede usarse python, pero es más rápido usar herramientas web en caso de que se cuente con acceso a internet
## Referencias
[From Decimal - CyberChef](https://cyberchef.io/#recipe=From_Decimal\('Space',false\)&input=MTEyIAoxMDUgCjk5IAoxMTEgCjY3IAo4NCAKNzAgCjEyMyAKMTAzIAo0OCAKNDggCjEwMCAKOTUgCjEwNyAKNDkgCjExNiAKMTE2IAoxMjEgCjMzIAo5NSAKMTEwIAo0OSAKOTkgCjUxIAo5NSAKMTA3IAo0OSAKMTE2IAoxMTYgCjEyMSAKMzMgCjk1IAo5NyAKNTcgCjUyIAoxMDEgCjU1IAoxMjUgCjEwIA)