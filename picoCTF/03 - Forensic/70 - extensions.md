## Descripción
This is a really weird text file. Can you find the flag? Get the flag from [TXT](https://challenge-files.cylabacademy.net/library/a5d365fedad883763fb28c78e25a1765220bef6501848c84f2a7a7adf74d83cd/flag.txt).
## Solución
- El archivo `flag.txt` aparenta ser un archivo de texto plano debido a su extensión `.txt`, sin embargo, al analizarlo con la herramienta `file` en Kali Linux descubrimos que sus números mágicos corresponden a una imagen en formato PNG:
  ```bash
  # Identificar el tipo real del archivo
  file flag.txt
  # Salida: flag.txt: PNG image data, 1697 x 608, 8-bit/color RGB, non-interlaced
  ```
- Para visualizar la imagen, le asignamos la extensión correspondiente (`.png`) o creamos una copia con dicha extensión
- Al abrir la imagen, se observa claramente la bandera escrita en ella:
```text
academy{now_you_know_about_extensions}
```

## Notas Adicionales
- Los sistemas operativos Unix/Linux identifican el formato de un archivo mediante los *magic numbers* (firmas de bytes en la cabecera) y no por su extensión. En este caso, los primeros 8 bytes son `\x89PNG\r\n\x1a\n` (`89 50 4E 47 0D 0A 1A 0A`), identificador estándar de imágenes PNG.
- Herramientas utilizadas en Kali Linux:
  - `file`: Utilidad para determinar el tipo de archivo mediante el análisis de cabeceras.
  - Visor de imágenes (`ristretto`, `eog`, `xdg-open`).

## Referencias
- [file(1) - Linux man page](https://man7.org/linux/man-pages/man1/file.1.html)
- [PNG (Portable Network Graphics) Specification](https://www.w3.org/TR/PNG/)
- [List of file signatures - Wikipedia](https://en.wikipedia.org/wiki/List_of_file_signatures)