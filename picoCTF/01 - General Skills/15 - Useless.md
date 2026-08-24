## Descripción
There's an interesting script in the user's home directory
## Solución
- Nos conectamos por ssh: `ssh -p 62434 picoplayer@saturn.picoctf.net`
- Leemos el código del archivo useless con `cat`
- Si no le pasamos ningún parámetro, usaremos el comando `man useless` para que nos proporcione el manual
```text
picoCTF{us3l3ss_ch4ll3ng3_3xpl0it3d_8504}
```
## Notas Adicionales
El comando man nos proporciona el manual de algún script si es que lo contiene
## Referencias