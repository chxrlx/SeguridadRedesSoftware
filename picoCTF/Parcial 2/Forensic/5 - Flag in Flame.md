## Descripción
The SOC team discovered a suspiciously large log file after a recent breach. When they opened it, they found an enormous block of encoded text instead of typical logs. Could there be something hidden within? Your mission is to inspect the resulting file and reveal the real purpose of it. The team is relying on your skills to uncover any concealed information within this unusual log. Download the encoded data here: Logs Data. Be prepared—the file is large, and examining it thoroughly is crucial.

## Solución
- Al inspeccionar el contenido del archivo `logs.txt`, se observa un bloque masivo de texto codificado en Base64 de aproximadamente 1.6 MB.
- Analizando los primeros caracteres del flujo:
  ```text
  iVBORw0KGgoAAAANSUhEUg...
  ```
- La cabecera `iVBORw0KGgo` corresponde a la representación Base64 del encabezado estándar de un archivo de imagen en formato PNG (`\x89PNG\r\n\x1a\n\x00\x00\x00\rIHDR...`).
- Decodificamos el archivo de Base64 a un archivo binario PNG:
  ```bash
  base64 -d logs.txt > output.png
  ```
- Verificamos el tipo de archivo obtenido mediante `file`:
  ```bash
  file output.png
  # output.png: PNG image data, 896 x 1152, 8-bit/color RGB, non-interlaced
  ```
- Al abrir e inspeccionar visualmente la imagen resultante (`output.png`), se aprecia una ilustración de temática ciberseguridad/hacker. En el borde inferior de la imagen se encuentra impresa una cadena de texto en codificación hexadecimal:
  ```text
  61636164656D797B666F72656E736963735F616E616C797369735F69735F616D617A696E675F61626531643662617D
  ```
- Decodificamos la cadena hexadecimal a texto ASCII mediante Python o xxd:
  ```python
  hex_str = "61636164656D797B666F72656E736963735F616E616C797369735F69735F616D617A696E675F61626531643662617D"
  flag = bytes.fromhex(hex_str).decode("ascii")
  print(flag)
  ```
  O en la terminal:
  ```bash
  echo "61636164656D797B666F72656E736963735F616E616C797369735F69735F616D617A696E675F61626531643662617D" | xxd -r -p
  ```
- Obtenemos la bandera:
```text
academy{forensics_analysis_is_amazing_abe1d6ba}
```

## Notas Adicionales
- La técnica de exfiltración o encubrimiento de artefactos binarios dentro de registros de texto mediante esquemas de codificación como Base64 es común en incidentes de seguridad (Data Exfiltration y Living off the Land).
- Los números mágicos o firmas de archivo en Base64 permiten reconocer rápidamente el formato subyacente sin necesidad de decodificar previamente (`iVBORw0KGgo` = PNG, `/9j/` = JPEG, `JVBERi0` = PDF).
- Herramientas utilizadas:
  - `base64`: Para reconstruir el archivo binario.
  - `file`: Verificación del tipo MIME y dimensiones.
  - `xxd` / `python3`: Decodificación hexadecimal a ASCII.

## Referencias
- [PNG (Portable Network Graphics) Specification](https://www.w3.org/TR/png/)
- [List of file signatures - Wikipedia](https://en.wikipedia.org/wiki/List_of_file_signatures)
