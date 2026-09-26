## Descripción
This website puts a two-factor prompt between you and the flag. Register an account, then take a close look at the requests your browser is actually sending.
## Solución
- En la interfaz inicial nos encontramos con un formulario de registro. Completamos los campos requeridos con datos de prueba arbitrarios para crear la cuenta e iniciar el flujo de autenticación.

- Al enviar el registro, la aplicación nos redirige a una segunda pantalla que nos solicita ingresar un código de verificación de dos factores (2FA / OTP).

- Configuramos y activamos el proxy de intercepción en **Burp Suite** (_Proxy > Intercept is on_). Ingresamos un código aleatorio en el campo de texto y enviamos el formulario para capturar la petición HTTP `POST` enviada por el navegador.

- Analizamos el cuerpo de la petición capturada en Burp Suite e identificamos el parámetro encargado de transmitir el código de verificación (por ejemplo, `otp=1234`).

- Dado que la interfaz del cliente siempre incluye esta variable al enviar el formulario, sospechamos que el backend podría carecer de una validación explícita que confirme la presencia del parámetro antes de evaluar su contenido.

- Eliminamos por completo la variable `otp` y su valor del cuerpo de la petición HTTP.

- Reenviamos la petición modificada al servidor haciendo clic en **Forward**.

- Al no encontrar el parámetro esperado ni existir un control que rechace la solicitud por su ausencia, la lógica del servidor omite el bloque de verificación del código, valida la autenticación como exitosa y nos despliega la bandera:
```text
academy{#0TP_Bypvss_SuCc3$S_10ea7681}
```
## Notas Adicionales
- **Falla de Lógica en Validación de Entradas:** La vulnerabilidad radica en asumir implícitamente que los parámetros de la interfaz web siempre llegarán en la petición. Al manipular el tráfico con un proxy, se demuestra que la presencia de los parámetros debe verificarse estrictamente en el servidor.
    
- **Bypass por Omisión de Parámetros:** Cuando el backend no comprueba la existencia de una variable requerida, el intérprete o framework puede asignar un valor nulo o saltar la condición de verificación, permitiendo el acceso no autorizado.
    
- **Mitigación:** Para evitar este tipo de bypass, el código del lado del servidor debe validar de forma explícita que los parámetros obligatorios existan, no estén vacíos y correspondan a un token válido antes de conceder acceso a recursos protegidos.
## Referencias