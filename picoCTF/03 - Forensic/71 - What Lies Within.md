## Descripción
There's something in the [building](https://challenge-files.cylabacademy.net/library/5235bfccd8f3cc2d059ee828f774846542f28f7ec66cb4f0c6ceb536c38026d4/buildings.png). Can you retrieve the flag?
## Solución
- Para resolver este reto en Kali Linux inspeccionamos la imagen `buildings.png` en busca de esteganografía LSB (*Least Significant Bit*), técnica que oculta información en los bits menos significativos de los canales de color (RGB).
- Empleamos la herramienta `zsteg`, especializada en detectar y extraer datos ocultos en archivos PNG/BMP:
  ```bash
  # Analizar los canales y extraer la bandera con zsteg:
  zsteg buildings.png | grep -i "academy{"
  # Salida: b1,rgb,lsb,xy .. text: "academy{h1d1ng_1n_th3_b1t5}"
  ```
  
```text
academy{h1d1ng_1n_th3_b1t5}
```

## Notas Adicionales
- La bandera se encontraba incrustada en el bit menos significativo (LSB) del plano RGB (`b1,rgb,lsb,xy`).
- Herramientas utilizadas en Kali Linux:
  - `zsteg`: Herramienta en Ruby especializada en esteganografía en imágenes PNG/BMP.
## Referencias
- [zsteg GitHub Repository](https://github.com/zed-0xff/zsteg)
- [Steganography - Least Significant Bit (Wikipedia)](https://en.wikipedia.org/wiki/Steganography)