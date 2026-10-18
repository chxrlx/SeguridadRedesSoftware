## Descripción
RED, RED, RED, RED

## Solución
- Analizamos los metadatos de la imagen `red.png` utilizando `exiftool`:
  ```bash
  exiftool red.png
  ```
- En el campo `Poem` de los metadatos encontramos el siguiente texto:
  ```text
  Crimson heart, vibrant and bold,
  Hearts flutter at your sight.
  Evenings glow softly red,
  Cherries burst with sweet life.
  Kisses linger with your warmth.
  Love deep as merlot.
  Scarlet leaves falling softly,
  Bold in every stroke.
  ```
- Al observar la primera letra de cada verso (`C`, `H`, `E`, `C`, `K`, `L`, `S`, `B`), se forma el acróstico **CHECK LSB**, indicando que la información está oculta mediante esteganografía en los bits menos significativos (LSB).
- Ejecutamos `zsteg` sobre la imagen:
  ```bash
  zsteg red.png
  ```
- En el canal `b1,rgba,lsb,xy`, encontramos una cadena codificada en Base64:
  ```text
  b1,rgba,lsb,xy      .. text: "YWNhZGVteXtyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ=="
  ```
- Decodificamos la cadena con `base64`:
  ```bash
  echo "YWNhZGVteXtyM2RfMXNfdGgzX3VsdDFtNHQzX2N1cjNfZjByXzU0ZG4zNTVffQ==" | base64 -d
  ```
- También es posible extraerla y decodificarla directamente en una sola línea de comandos:
  ```bash
  zsteg -E "b1,rgba,lsb,xy" red.png | grep -o "academy{[^}]*}"
  ```
- Obtenemos la bandera:
```text
academy{r3d_1s_th3_ult1m4t3_cur3_f0r_54dn355_}
```

## Notas Adicionales
- El reto combina el análisis de metadatos (donde se presenta un acróstico como pista) con esteganografía LSB en los cuatro canales de color RGBA.
- Herramientas utilizadas en Kali Linux:
  - `exiftool`: Inspección de metadatos del archivo PNG.
  - `zsteg`: Detección y extracción de datos en los bits menos significativos (LSB) de imágenes PNG.
  - `base64`: Decodificación de la cadena obtenida.

## Referencias
- [zsteg GitHub Repository](https://github.com/zed-0xff/zsteg)
- [Least Significant Bit (LSB) Steganography](https://en.wikipedia.org/wiki/Steganography#Digital_messages)
- [Acrostic Puzzles in CTFs](https://ctf-wiki.org/misc/crypto/classic/introduction/)
