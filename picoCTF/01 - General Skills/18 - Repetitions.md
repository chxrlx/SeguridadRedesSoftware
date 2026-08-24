## Descripción
Can you make sense of this file?
## Solución
- Descargamos el archivo que nos dan.
- Vemos qué contiene: `cat enc_flag`
- Nos arroja una cadena que está en base64.
- La cadena tiene múltiples codificaciones, así que tenemos que decodificarla varias veces hasta llegar a la bandera usando una herramienta web para decodificar
```text
picoCTF{base64_n3st3d_dic0d!n8_d0wnl04d3d_9b59b35c}
```
## Notas Adicionales
Un elemento puede codificarse múltiples veces
## Referencias
[Base64 Decode and Encode - Online](https://www.base64decode.org/)