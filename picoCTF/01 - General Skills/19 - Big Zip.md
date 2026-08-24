## Descripción
Unzip this archive and find the flag.

## Solución
- Descargamos el zip
- Descomprimimos el zip: `unzip big-zip-files.zip`
- Nos movemos a la carpeta: `cd big-zip-files`
- Al tener muchos archivos, directorios y subdirectorios, necesitamos un comando que busque de manera recursiva en todos los archivos y directorios existentes en la carpeta actual, así que usaremos el comando: `grep -r picoCTF *` y nos arrojará la bandera
```text
picoCTF{gr3p_15_m4g1c_ef8790dc}
```
## Notas Adicionales
Grep puede buscar de manera recursiva a través de directorios y subdirectorios con el parámetro -r
## Referencias
[How to use "grep" command to find text including subdirectories - Ask Ubuntu](https://askubuntu.com/questions/55325/how-to-use-grep-command-to-find-text-including-subdirectories)