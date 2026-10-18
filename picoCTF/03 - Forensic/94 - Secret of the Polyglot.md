## Descripción
The Network Operations Center (NOC) of your local institution picked up a suspicious file, they're getting conflicting information on what type of file it is. They've brought you in as an external expert to examine the file. Can you extract all the information from this strange file?

## Solución
- Analizamos el archivo proporcionado `flag2of2-final.pdf` con la utilidad `file` y `binwalk`:
  ```bash
  file flag2of2-final.pdf
  binwalk flag2of2-final.pdf
  ```
  Salida:
  ```text
  flag2of2-final.pdf: PNG image data, 200 x 40, 4-bit colormap, non-interlaced

  DECIMAL       HEXADECIMAL     DESCRIPTION
  --------------------------------------------------------------------------------
  0             0x0             PNG image, 200 x 40, 4-bit colormap, non-interlaced
  453           0x1C5           PDF document, version: "1.4"
  688           0x2B0           Zlib compressed data, default compression
  ```
- Se trata de un archivo **políglota** (polyglot): un archivo válido simultáneamente como imagen PNG y como documento PDF.
- Para obtener la primera mitad de la bandera, tratamos el archivo como una imagen PNG (abriendo el archivo con un visor de imágenes o convirtiéndolo/copiándolo a extensión `.png`):
  ```bash
  cp flag2of2-final.pdf flag_part1.png
  ```
  Al visualizar la imagen, se observa la primera parte de la bandera:
  `academy{f1u3n7_`

- Para obtener la segunda mitad de la bandera, tratamos el archivo como un documento PDF y extraemos su texto mediante `pdftotext`:
  ```bash
  pdftotext flag2of2-final.pdf -
  ```
  Salida obtenida:
  `1n_pn9_&_pdf_241519c6}`

- Unimos ambas partes para formar la bandera completa:
```text
academy{f1u3n7_1n_pn9_&_pdf_241519c6}
```

## Notas Adicionales
- Un archivo políglota es un archivo que es válido y ejecutable o interpretable por dos o más analizadores sintácticos de formatos diferentes (en este caso, los estándares PNG y PDF).
- Los visores de imágenes PNG comienzan leyendo la firma inicial `\x89PNG\r\n\x1a\n` y terminan en el bloque `IEND`, ignorando los datos posteriores.
- Los lectores de PDF escanean el archivo desde el final buscando el tráiler del PDF (`trailer` y `%%EOF`), permitiendo que el contenido PNG previo sea ignorado.
- Herramientas utilizadas en Kali Linux:
  - `file`: Identificación del tipo de archivo y cabecera inicial.
  - `binwalk`: Detección de múltiples estructuras de formatos en el mismo binario.
  - `pdftotext`: Extracción del texto del componente PDF.

## Referencias
- [PoC||GTFO (Proof of Concept or Get The Fuck Out) - Polyglot Files](https://github.com/angea/pocorgtfo)
- [PNG Specification (ISO/IEC 15948)](https://www.w3.org/TR/PNG/)
- [PDF Reference and Specifications (Adobe)](https://www.adobe.com/devnet/pdf/pdf_reference.html)
- [pdftotext(1) - Linux man page](https://manpages.debian.org/testing/poppler-utils/pdftotext.1.en.html)
