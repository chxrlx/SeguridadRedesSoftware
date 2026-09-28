## Descripción
Help us test the form by submitting the username as `test` and password as `test!`
`http://chatelaine.cylabacademy.net:23288/`

## Solución
- Ingresamos a la página web provista: `http://chatelaine.cylabacademy.net:23288/`
- Encontramos un formulario de inicio de sesión con las credenciales indicadas:
  - **Username**: `test`
  - **Password**: `test!`
- Para no perder el rastro de las redirecciones automáticas, abrimos las herramientas de desarrollo (`F12`), vamos a la pestaña **Network** y activamos la opción **Preserve log** (o bien analizamos el tráfico mediante `curl` o un proxy como Burp Suite)
- Al enviar el formulario, el servidor responde con una redirección HTTP 302 hacia:
  `/next-page/id=YWNhZGVteXtwcm94aWVzX2Fs`
- Al cargarse esa página, un temporizador en JavaScript redirige rápidamente (`window.location`) hacia una segunda ruta:
  `/next-page/id=bF90aGVfd2F5X2E1Yjg0YzY2fQ==`
- Finalmente, la página redirige a `/home`
- Observamos que los dos valores de `id` son fragmentos codificados en Base64:
  - Primer fragmento: `YWNhZGVteXtwcm94aWVzX2Fs`
  - Segundo fragmento: `bF90aGVfd2F5X2E1Yjg0YzY2fQ==`
- Decodificamos ambos fragmentos en la terminal:
  ```bash
  echo -n "YWNhZGVteXtwcm94aWVzX2Fs" | base64 -d
  # Resultado: academy{proxies_al

  echo "bF90aGVfd2F5X2E1Yjg0YzY2fQ==" | base64 -d
  # Resultado: l_the_way_a5b84c66}
  ```
- Uniendo ambos fragmentos obtenemos la bandera completa

```text
academy{proxies_all_the_way_a5b84c66}
```

## Notas Adicionales
- **Preserve Log en DevTools**: Los navegadores por defecto limpian el registro de la pestaña Network cada vez que ocurre una navegación. La opción "Preserve Log" (o "Persist Logs") es fundamental en auditorías web para capturar solicitudes intermedias y respuestas de redirección (`301`/`302`).
- **Redirecciones HTTP vs Client-Side**: El reto muestra dos mecanismos de redirección comunes:
  - A nivel de protocolo: Encabezado HTTP `Location` con estado `302 Found`.
  - A nivel de cliente: Manipulación del DOM mediante `window.location` en JavaScript.

## Referencias
- [MDN Web Docs - Códigos de estado de redirección HTTP](https://developer.mozilla.org/es/docs/Web/HTTP/Status#redirecciones)
- [Chrome DevTools - Network features reference (Preserve log)](https://developer.chrome.com/docs/devtools/network/reference#preserve-log)