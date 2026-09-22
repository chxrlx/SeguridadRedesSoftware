## Descripción
How about trying to match a regular expression
## Solución
- Accedemos al sitio web del reto proporcionado por la instancia.
- Inspeccionamos el código fuente de la página web (Ctrl + U o con las herramientas de desarrollador del navegador).
- En el código fuente, dentro de la etiqueta `<script>`, encontramos la función `send_request()` que contiene un comentario que revela la expresión regular esperada:
```javascript
function send_request() {
	let val = document.getElementById("name").value;
	// ^p.....F!?
	fetch(`/flag?input=${val}`)
		.then(res => res.text())
		.then(res => {
			const res_json = JSON.parse(res);
			alert(res_json.flag)
			return false;
		})
	return false;
}
```
- El comentario `^p.....F!?` indica el patrón de la expresión regular (Regex) que valida el servidor:
  - `^`: Coincide con el inicio de la cadena.
  - `p`: El carácter literal 'p'.
  - `.....`: 5 caracteres arbitrarios (el punto `.` coincide con cualquier carácter).
  - `F`: El carácter literal 'F'.
- La palabra `picoCTF` coincide perfectamente con este patrón (`p` + 5 caracteres `icoCT` + `F`). También funcionaría cualquier otra cadena que cumpla con la estructura (por ejemplo, `pxxxxxF`).
- Introducimos `picoCTF` en el campo de texto y hacemos clic en **SUBMIT** (o realizamos la petición directamente al endpoint `/flag?input=picoCTF`).
- La aplicación despliega una ventana de alerta con la bandera:
```text
picoCTF{succ3ssfully_matchtheregex_f89ea585}
```
## Notas Adicionales
- Expresiones Regulares (Regex): Patrones empleados para identificar o validar secuencias de caracteres dentro de cadenas de texto.
- Metacarácter `^`: Representa una aserción de límite que indica el comienzo de la cadena o línea.
- Metacarácter `.`: En expresiones regulares, coincide con cualquier carácter único (excepto terminadores de línea). Cinco puntos seguidos exigen exactamente cinco caracteres intermedios.
- Inspección de código frontend: En muchos retos iniciales de desarrollo web o CTFs, los desarrolladores dejan pistas, comentarios o incluso lógica sensible sin sanitizar directamente en el frontend.
## Referencias
- [MDN Web Docs - Expresiones Regulares en JavaScript](https://developer.mozilla.org/es/docs/Web/JavaScript/Guide/Regular_expressions)
- [Regex101: Online regex tester and debugger](https://regex101.com/)