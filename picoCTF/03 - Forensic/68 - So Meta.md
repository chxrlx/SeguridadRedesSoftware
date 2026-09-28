## Descripción
Find the flag in this picture.
## Solución
 - Para resolver este reto en Kali Linux examinamos los metadatos de la imagen `pico_img.png` mediante la herramienta `exiftool` y la búsqueda de cadenas de texto con `strings`:
  ```bash
  # Opción 1: Extraer cadenas de texto imprimibles directamente
  strings pico_img.png | grep -i "academy{"

  # Opción 2: Inspeccionar los metadatos de la imagen
  exiftool pico_img.png | grep -i "Artist"
  ```
- La bandera se encontraba incrustada en los metadatos XMP de la imagen PNG, específicamente en el atributo `Artist` generado con Adobe Photoshop CS6 / ImageReady:
  `Artist : academy{s0_m3ta_b52e28f5}`
```text
academy{s0_m3ta_b52e28f5}
```
## Notas Adicionales
- Ambas herramientas forman parte del arsenal forense estándar disponible en Kali Linux (`exiftool` y `binutils/strings`).
## Referencias
- [ExifTool by Phil Harvey](https://exiftool.org/)
- [strings(1) - Linux man page](https://man7.org/linux/man-pages/man1/strings.1.html)