## Descripción
Can you find the flag in file without running it?
## Solución
Tenemos que usar el comando strings en conjunto del comando grep:
- `strings strings | grep picoCTF`
```text
picoCTF{5tRIng5_1T_1067EC4c}
```
## Notas Adicionales
El comando strings sirve para buscar y extraer secuencias de texto legible o caracteres imprimibles dentro de archivos binarios, ejecutables o imágenes de disco
## Referencias