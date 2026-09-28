## Descripción
Cookie Monster has hidden his top-secret cookie recipe somewhere on his website. As an aspiring cookie detective, your mission is to uncover this delectable secret. Can you outsmart Cookie Monster and find the hidden recipe?
`http://chatelaine.cylabacademy.net:22281/`

## Solución
- Ingresamos a la página web del reto: `http://chatelaine.cylabacademy.net:22281/`
- Encontramos un formulario de login con campos de usuario y contraseña
- Intentamos iniciar sesión con cualquier credencial de prueba (por ejemplo, `admin` / `admin`)
- El servidor nos rechaza el acceso con el mensaje:
  ```text
  Cookie Monster says: 'Me no need password. Me just need cookies!'
  Hint: Have you checked your cookies lately?
  ```
- Siguiendo la pista, inspeccionamos las cookies guardadas por el navegador abriendo las herramientas de desarrollo (`F12`), yendo a la pestaña **Application** (o **Storage**) > **Cookies** (o revisando las cabeceras HTTP con `curl -i`):
  ```http
  Set-Cookie: secret_recipe=YWNhZGVteXtjMDBrMWVfbTBuc3Rlcl9sMHZlc19jMDBraWVzXzRBMjQ5MzIyfQ%3D%3D;
  ```
- Observamos la cookie llamada `secret_recipe`. Su valor termina en `%3D%3D` (caracteres de relleno `=` en codificación URL), lo cual indica que está codificada en Base64:
  `YWNhZGVteXtjMDBrMWVfbTBuc3Rlcl9sMHZlc19jMDBraWVzXzRBMjQ5MzIyfQ==`
- Decodificamos la cadena desde la terminal:
  ```bash
  echo "YWNhZGVteXtjMDBrMWVfbTBuc3Rlcl9sMHZlc19jMDBraWVzXzRBMjQ5MzIyfQ==" | base64 -d
  ```
- Al decodificarla obtenemos la bandera directamente

```text
academy{c00k1e_m0nster_l0ves_c00kies_4A249322}
```

## Notas Adicionales
- **Cookies HTTP**: Son pequeños bloques de información generados por el servidor web y almacenados en el navegador del cliente mediante la cabecera `Set-Cookie`.
- **Codificación Base64**: Base64 no es un algoritmo de cifrado ni proporciona confidencialidad; es simplemente una forma de representar datos binarios o texto mediante un conjunto estándar de caracteres ASCII imprimibles. Nunca debe utilizarse para proteger secretos en el cliente sin cifrado real previo.

## Referencias
- [MDN Web Docs - Uso de cookies HTTP](https://developer.mozilla.org/es/docs/Web/HTTP/Cookies)
- [CyberChef - Herramienta para decodificación Base64](https://gchq.github.io/CyberChef/)