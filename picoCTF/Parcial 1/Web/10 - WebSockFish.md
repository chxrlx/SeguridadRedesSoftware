## Descripción
Can you win in a convincing manner against this chess bot? He won't go easy on you!

## Solución
- Accedemos a la página web del reto, donde encontramos una partida interactiva de ajedrez contra un bot ("WebSockfish")
- Abrimos las herramientas de desarrollo (`F12`) y examinamos los scripts en el código fuente de la página
- Identificamos que el cliente establece una conexión WebSocket persistente hacia la ruta `/ws/`:
  ```javascript
  var ws_address = "ws://" + location.hostname + ":" + location.port + "/ws/";
  const ws = new WebSocket(ws_address);

  ws.onmessage = (event) => {
    const message = event.data;
    updateChat(message);
  };

  function sendMessage(message) {
    ws.send(message);
  }
  ```
- Vemos que el análisis de la partida se ejecuta en el navegador del cliente mediante un Web Worker de Stockfish y los resultados de evaluación se envían al backend con el formato `eval <puntuacion>` o `mate <turnos>`
- El servidor confía ciegamente en la evaluación enviada por el cliente. Si recibe una evaluación con un valor negativo sumamente alto (que indique una ventaja muy alta para el jugador), el bot asume que la partida está perdida y se rinde
- Para forzar la rendición, abrimos la consola de desarrollador (`Console`) y llamamos directamente a la función expuesta:
  ```javascript
  sendMessage("eval -2200000");
  ```
- El servidor responde por el WebSocket con el mensaje de rendición y la bandera:
  ```text
  Huh???? How can I be losing this badly... I resign... here's your flag: academy{c1i3nt_s1d3_w3b_s0ck3t5_c8e7db95}
  ```

```text
academy{c1i3nt_s1d3_w3b_s0ck3t5_c8e7db95}
```

## Notas Adicionales
- **WebSockets**: Es un estándar de comunicación bidireccional sobre una única conexión TCP continua. Se inicializa mediante una petición HTTP con cabeceras `Upgrade: websocket` y `Connection: Upgrade`.
- **Falta de Validación en el Servidor (Trusting the Client)**: Delegar la lógica de negocio o la verificación del estado del juego enteramente al cliente es una vulnerabilidad crítica. Cualquier dato enviado por el frontend (incluyendo mensajes WebSocket) puede ser interceptado, modificado o fabricado por el usuario.
- **Alternativa mediante scripts**: Es posible conectarse al WebSocket directamente desde un script (en Python o Node.js) o con herramientas como `wscat` para enviar el mensaje sin interactuar con la interfaz gráfica.

## Referencias
- [MDN Web Docs - La API WebSocket](https://developer.mozilla.org/es/docs/Web/API/WebSocket)
- [OWASP - Testing WebSockets](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/10-Testing_WebSockets)
- [RFC 6455 - The WebSocket Protocol](https://datatracker.ietf.org/doc/html/rfc6455)