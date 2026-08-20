## Descripción
Can you find the flag in the file? This would be really tedious to look through manually, something tells me there is a better way.
## Solución
Usaré el comando grep para encontrar texto en el archivo que descargué
```shell
grep picoCTF file
```

Lo que nos arroja un texto resaltado entre todo que contiene la bandera entre paréntesis
```text
picoCTF{grep_is_good_to_find_things_e3C4b360}
```
## Notas Adicionales
grep es muy útil para encontrar patrones en texto
## Referencias
[grep command in Unix/Linux - GeeksforGeeks](https://www.geeksforgeeks.org/linux-unix/grep-command-in-unixlinux/)