## Descripción
How to automate tasks to run at intervals on linux servers?
## Solución
- Debido a la descripción, nos dice que cómo automatizar tareas en linux, así que indagando un poco, vi que en linux los cron jobs del  usuario raiz se guardan en la carpeta: `/etc/crontab`
- Así que ejecutamos el comando: `head /etc/crontab` para ver cuándo se invocaran esas tareas
- Nos arroja la bandera de una
```text
picoCTF{Sch3DUL7NG_T45K3_L1NUX_d83baed1}
```
## Notas Adicionales
- Las tareas del sistema en Linux se definen en `/etc/crontab` o dentro del directorio `/etc/cron.d/`
## Referencias
[Los 5 lugares donde se guardan los empleos cron](https://cronitor.io/guides/five-places-for-cron-jobs)