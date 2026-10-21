## Descripción

I have these 2 images, can you make a flag out of them?

## Solución

- Las dos imágenes tienen el mismo tamaño (`256 x 256`) y muestran ruido de colores. Esto sugiere que cada una es una capa de una imagen compartida visualmente.
- La pista de "apilar" imágenes indica que debemos combinar los píxeles de ambas capas. Probamos operaciones habituales como suma, diferencia y XOR.
- La operación correcta es sumar los valores de cada canal RGB y conservar el resultado módulo 256:
  $$C(x,y) = (A(x,y) + B(x,y)) \bmod 256$$
- Al aplicar esta suma modular píxel a píxel, el ruido se cancela y aparece el texto oculto:
  ```text
  academy{2f16d0c4}
  ```

Se puede reproducir con Python y Pillow:

```python
from PIL import Image
import numpy as np

image1 = np.array(Image.open("scrambled1.png").convert("RGB"), dtype=np.uint8)
image2 = np.array(Image.open("scrambled2.png").convert("RGB"), dtype=np.uint8)

result = (image1.astype(np.uint16) + image2.astype(np.uint16)).astype(np.uint8)
Image.fromarray(result).save("pixelated-result.png")
```

Al abrir `pixelated-result.png`, la bandera se ve en el centro de la imagen.

## Notas Adicionales

- La técnica está relacionada con la criptografía visual: las capas individuales parecen ruido, pero al superponerse revelan la información compartida.
- En este reto, "apilar" significa combinar las capas mediante suma modular. La conversión temporal a `uint16` evita que la suma desborde antes de aplicar el módulo 256 al convertir de nuevo a `uint8`.
- La operación se realiza por separado sobre los canales rojo, verde y azul, por lo que no es necesario convertir las imágenes a escala de grises.

## Referencias

- [Visual cryptography - Wikipedia](https://en.wikipedia.org/wiki/Visual_cryptography)
- [Modular arithmetic](https://en.wikipedia.org/wiki/Modular_arithmetic)
