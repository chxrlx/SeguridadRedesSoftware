## Descripción
Check the admin scratchpad!
## Solución
- Ingresamos al sitio web del reto, escribimos cualquier nombre de usuario (en mi caso, charly) y guardamos el JWT que la aplicación nos asigna (lo copiamos desde las herramientas de desarrollador del navegador en Application > Cookies o en la respuesta HTTP)
- Guardamos el token completo en un archivo de texto llamado jwt.txt.
- Ejecutamos John the Ripper utilizando la lista de palabras rockyou.txt para encontrar el secreto:
`john jwt.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=HMAC-SHA256`
- (John identificará la clave secreta en cuestión de segundos, en mi caso: ilovepico)
- Vamos a usar una herramienta para decodificar y codificar el JWT usando una página (jwt.io)
- En la sección JWT Decoder vamos a ingresar el jwt que habíamos extraído anteriormente y en la parte de JWT Signature Verification vamos a ingresar la contraseña que nos dio John The Ripper (ilovepico)
- Nos movemos a la sección JWT Encoder y en la sección de payload, cambiamos nuestro usuario por el usuario admin:
`{`
  `"user": "admin"`
`}`
- Copiamos el Encoded JWT que nos da y lo cargamos en el valor de la cookie donde extrajimos el JWT, recargamos la página y nos dará la bandera
```text
picoCTF{jawt_was_just_what_you_thought_bbb82bd4a57564aefb32d69dafb60583}
```
## Notas Adicionales
- Firma HMAC-SHA256: La seguridad de un JWT depende de la fortaleza de su clave secreta. Si la clave es una palabra común, un ataque por diccionario puede descifrarla fácilmente offline.
- Fuerza bruta offline: El servidor no bloquea tus intentos porque el ataque no se realiza contra la web, sino computando hashes localmente con John the Ripper a partir del token que el servidor te entregó.
## Referencias
https://www.jwt.io/