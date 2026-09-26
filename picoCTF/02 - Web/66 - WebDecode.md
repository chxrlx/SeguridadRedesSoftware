## Descripción
Do you know how to use the web inspector? Start searching here to find the flag

## Solución
- Accedemos a la página web del reto proporcionada por la instancia (`http://chatelaine.cylabacademy.net:15731/`).
- La aplicación web cuenta con un menú de navegación con tres apartados: *Home* (`index.html`), *About* (`about.html`) y *Contact* (`contact.html`).
- Navegamos a la sección **About** (`http://chatelaine.cylabacademy.net:15731/about.html`) e inspeccionamos el código fuente (`Ctrl + U`) o abrimos las herramientas de desarrollador (`F12`) en la pestaña **Elements**.
- Observamos que la etiqueta principal de la sección incluye un atributo no estándar llamado `notify_true`:
  ```html
  <section class="about" notify_true="YWNhZGVteXt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMDc5ODliMjV9">
  ```
- El valor del atributo es una cadena codificada en Base64. Procedemos a decodificarla:
  - **Desde la consola del navegador (JavaScript):**
    ```javascript
    atob("YWNhZGVteXt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMDc5ODliMjV9")
    ```
  - **Desde la terminal con base64:**
    ```bash
    echo "YWNhZGVteXt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMDc5ODliMjV9" | base64 -d
    ```
  - **En PowerShell:**
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("YWNhZGVteXt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMDc5ODliMjV9"))
    ```
- Obtenemos la bandera resultante:

```text
academy{web_succ3ssfully_d3c0ded_07989b25}
```

## Notas Adicionales
- **Atributos personalizados en el DOM:** Los atributos personalizados o atributos de datos (`data-*`) en etiquetas HTML son visibles e inspeccionables por cualquier cliente a través de las herramientas de desarrollo del navegador.
- **Decodificación nativa con `atob` y `btoa`:** En los navegadores web modernos, la función `window.atob()` (ASCII to Binary) permite decodificar cadenas en formato Base64 a texto ASCII de forma inmediata desde la consola de desarrollo, mientras que `window.btoa()` realiza la codificación inversa.
- **Codificación vs. Cifrado:** Base64 es un esquema de representación de datos que no ofrece seguridad ni privacidad criptográfica; nunca debe usarse para ocultar secretos en el lado del cliente.

## Referencias
- [MDN Web Docs - Window.atob()](https://developer.mozilla.org/es/docs/Web/API/Window/atob)
- [MDN Web Docs - Uso de atributos de datos en HTML](https://developer.mozilla.org/es/docs/Learn/HTML/Howto/Use_data_attributes)