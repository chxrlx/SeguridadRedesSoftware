## Descripción
Can you invoke help flags for a tool or binary? This program has extraordinarily helpful information...
## Solución
- Descargamos el archivo con el comando `wget`
- Vemos qué tipo de archivo es con el comando `file warm`
- Al ser un ejecutable debemos darle permisos de ejecución: `chmod +x warm`
- Teniendo el permiso ejecutamos el ejecutable usando: `./warm -h` lo que nos arroja la bandera
```text
picoCTF{b1scu1ts_4nd_gr4vy_ac5832c}
```
## Notas Adicionales
- No podemos ejecutar un archivo si no tiene permisos de ejecución, por eso usamos chmod +x
- ELF es un archivo ejecutable usado en Linux, el equivalente a EXE en Windows
## Referencias