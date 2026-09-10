## Descripción
Find the flag being held on this server to get ahead of the competition
## Solución
- El título del reto nos da una pista de qué petición debemos usar
- Usamos el comando para que envíe una petición con el método HEAD para que nos devuelva las cabeceras del sitio: `curl -I http://wily-courier.picoctf.net:55332/`
- Nos devuelve las cabeceras de la petición HTTP, ahí viene la bandera
```text
picoCTF{r3j3ct_th3_du4l1ty_8b13f07}
```
## Notas Adicionales
- El comando: curl -I sirve para enviar una petición HEAD y solo recibir las cabeceras del sitio
## Referencias
[Curl Command en Linux con ejemplos - GeeksforGeeks](https://www.geeksforgeeks.org/linux-unix/curl-command-in-linux-with-examples/)