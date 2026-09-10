## Descripción
There is some interesting information hidden around this site. Can you find it?
## Solución
- La primera parte de la bandera se encuentra viendo el código fuente de la página
- La segunda parte viene viendo el archivo CSS desde las herramientas de desarrollo
- La tercera parte dentro del archivo .js nos da a entender que tenemos que ver los robots.txt de la página, ahí viene la tercera parte de la bandera y nos da una pista que es un servidor Apache
- La cuarta parte viene accediendo al archivo de configuración de Apache /.htaccess y nos da una pista relacionado con macOS
- La quinta y última bandera viene accediendo al archivo /.DS_Store
```text
picoCTF{th4ts_4_l0t_0f_pl4c3s_2_lO0k_9588550}
```
## Notas Adicionales
- Los servidores Apache usan el archivo .htaccess para configuraciones de acceso.
- macOS crea automáticamente archivos .DS_Store en las carpetas para guardar metadatos.
## Referencias