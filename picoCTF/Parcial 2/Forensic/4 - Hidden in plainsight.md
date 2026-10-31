## Descripción
You’re given a seemingly ordinary JPG image. Something is tucked away out of sight inside the file. Your task is to discover the hidden payload and extract the flag. Download the jpg image here.

## Solución
- Analizamos los metadatos de la imagen `img.jpg` utilizando `exiftool`:
  ```bash
  exiftool img.jpg
  ```
- Observamos un comentario sospechoso en los metadatos:
  ```text
  Comment                         : c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9
  ```
- Al decodificar el comentario en Base64:
  ```bash
  echo "c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9" | base64 -d
  ```
  Obtenemos:
  ```text
  steghide:cEF6endvcmQ=
  ```
- Esto nos indica que se utilizó la herramienta de esteganografía `steghide` y que la contraseña está codificada en Base64 (`cEF6endvcmQ=`). Procedemos a decodificar la contraseña:
  ```bash
  echo "cEF6endvcmQ=" | base64 -d
  ```
  Obteniendo la contraseña:
  ```text
  pAzzword
  ```
- Con la herramienta `steghide` y la clave recuperada, extraemos el archivo embebido (`flag.txt`):
  ```bash
  steghide extract -sf img.jpg -p "pAzzword"
  ```
- Leemos el contenido de `flag.txt`:
  ```bash
  cat flag.txt
  ```
- Obtenemos la bandera:
```text
academy{h1dd3n_1n_1m4g3_a2158aa5}
```

## Notas Adicionales
- `steghide` es una herramienta clásica de esteganografía que incrusta datos ocultos en varios formatos de imagen (como JPEG y BMP) y audio (WAV, AU) comprimiendo y cifrando los datos incrustados mediante algoritmos como Rijndael (AES).
- En este caso, el autor del reto dejó una pista de doble codificación Base64 en el comentario del encabezado JFIF indicando tanto el software utilizado como la contraseña necesaria para la extracción.
- Herramientas utilizadas:
  - `exiftool`: Extracción de metadatos JFIF / EXIF.
  - `base64`: Decodificación en terminal.
  - `steghide`: Extracción de carga útil esteganográfica.

## Referencias
- [Steghide Documentation & SourceForge](http://steghide.sourceforge.net/)
- [ExifTool by Phil Harvey](https://exiftool.org/)
