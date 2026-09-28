## Descripción
Why search for the flag when I can make a bookmarklet to print it for me?
Browse here, and find the flag!
`http://xebec.cylabacademy.net:45844/`

## Solución
- Ingresamos a la página web provista en el reto: `http://xebec.cylabacademy.net:45844/`
- En la interfaz nos encontramos con un área de texto que contiene el código de un bookmarklet en JavaScript:
  ```javascript
  javascript:(function() {
      var encryptedFlag = "ÑÌÄÓÈáßëÙ£Ö–ÓÚåÛÑ¢ÕÓ—Ò¡ÆÓ˜Ù–í";
      var key = "picoctf";
      var decryptedFlag = "";
      for (var i = 0; i < encryptedFlag.length; i++) {
          decryptedFlag += String.fromCharCode((encryptedFlag.charCodeAt(i) - key.charCodeAt(i % key.length) + 256) % 256);
      }
      alert(decryptedFlag);
  })();
  ```
- Para ejecutarlo y obtener la bandera tenemos dos opciones sencillas:
  1. **Consola del Navegador (DevTools)**: Presionamos `F12` o `Ctrl + Shift + I` en el navegador, nos dirigimos a la pestaña **Console**, pegamos la función sin el prefijo `javascript:` y presionamos `Enter`.
  2. **Como Bookmarklet**: Creamos un nuevo marcador en el navegador, en el campo de URL pegamos todo el contenido (incluyendo `javascript:...`) y luego hacemos clic sobre el marcador estando en la página.
- Al ejecutarse la función, se desencripta la cadena utilizando la clave `picoctf` y aparece una ventana de alerta (`alert`) mostrando la bandera.

```text
academy{p@g3_turn3r_1b8cd5e0}
```

## Notas Adicionales
- **Bookmarklets**: Son marcadores de navegador convencionales que, en lugar de contener una URL web (como `https://...`), almacenan código JavaScript ejecutable mediante el pseudo-protocolo `javascript:`. Al hacer clic en ellos, el navegador ejecuta dicho código en el contexto de la pestaña activa.
- **Seguridad en el lado del cliente (Client-side)**: Cualquier mecanismo de cifrado u ofuscación que dependa exclusivamente de código que corre en el navegador del cliente no es seguro frente a inspección, ya que tanto la clave como el algoritmo están completamente expuestos al usuario.

## Referencias
- [MDN Web Docs - JavaScript: URLs](https://developer.mozilla.org/es/docs/Web/URI/Schemes/javascript)
- [Wikipedia - Bookmarklet](https://es.wikipedia.org/wiki/Bookmarklet)