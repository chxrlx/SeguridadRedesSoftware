## Descripción
Download this image file and find the flag.

## Solución
- El archivo proporcionado `drawing.flag.svg` es una imagen vectorial en formato SVG (basado en XML).
- Al examinar los metadatos y el contenido de texto dentro del archivo SVG, encontramos un elemento `<text>` con identificador `text3723` renderizado con un tamaño de fuente microscópico (`font-size: 0.00352781px`):
  ```xml
  <text
     id="text3723">
     <tspan id="tspan3748">a </tspan>
     <tspan id="tspan3754">c </tspan>
     <tspan id="tspan3756">a </tspan>
     <tspan id="tspan3758">d </tspan>
     <tspan id="tspan3760">e </tspan>
     <tspan id="tspan3762">m </tspan>
     <tspan id="tspan3764">y { 3 n h 4 n </tspan>
     <tspan id="tspan3752">c 3 d _ 9 f 3 1 c 0 4 b }</tspan>
  </text>
  ```
- Para extraer y limpiar la bandera eliminando los espacios interpolados entre los caracteres de los elementos `<tspan>`, ejecutamos un script en Python:
  ```python
  import xml.etree.ElementTree as ET

  tree = ET.parse("drawing.flag.svg")
  root = tree.getroot()

  tspan_elements = root.findall(".//{http://www.w3.org/2000/svg}tspan")
  raw_text = "".join(elem.text for elem in tspan_elements if elem.text)
  flag = raw_text.replace(" ", "")
  print(flag)
  ```
- También es posible extraerla directamente con comandos de terminal:
  ```bash
  grep -oP '(?<=<tspan[^>]*>).*?(?=</tspan>)' drawing.flag.svg | tr -d '\n '
  ```
- Resultado obtenido:
```text
academy{3nh4nc3d_9f31c04b}
```

## Notas Adicionales
- Las imágenes SVG (Scalable Vector Graphics) se definen mediante sintaxis XML. En este tipo de retos, la información suele ocultarse reduciendo la escala visual del texto a dimensiones casi invisibles al ojo humano o colocándolo en coordenadas fuera del viewport principal, pero manteniéndose accesible en texto legible inspeccionando la estructura XML.
- Herramientas utilizadas:
  - `grep` y `tr` en bash.
  - Módulo `xml.etree.ElementTree` de Python 3.

## Referencias
- [W3C SVG Specification - Text](https://www.w3.org/TR/SVG11/text.html)
- [SVG Tutorial - MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial)
