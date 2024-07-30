#### Ejercicio 1 Listar dispositivos de bloque con información detallada
    ls -l /dev | grep “^b”
#### Ejercicio 2 Descartar la salida de un comando que muestra información de la memoria RAM
    cat /proc/meminfo > /dev/null
#### Ejercicio 3 Verificar los últimos registros de autenticación del sistema
```bash
ubuntu@foo:~$ last
ubuntu   pts/1        172.19.128.1     Tue Jul 30 13:02   still logged in
ubuntu   pts/0        172.19.128.1     Mon Jul 29 23:16 - 14:09  (14:53)
ubuntu   pts/0        172.19.128.1     Mon Jul 29 23:00 - 23:15  (00:14)
ubuntu   pts/0        172.24.0.1       Wed Jul 24 22:56 - 06:33  (07:36)
ubuntu   pts/0        172.24.0.1       Wed Jul 24 03:09 - 13:22  (10:13)
ubuntu   pts/0        172.24.0.1       Tue Jul 23 14:13 - 00:51  (10:37)
ubuntu   pts/0        172.24.0.1       Tue Jul 23 01:14 - 13:42  (12:28)
ubuntu   pts/0        172.24.0.1       Mon Jul 22 18:19 - 00:53  (06:34)
reboot   system boot  6.8.0-38-generic Mon Jul 22 14:28   still running
reboot   system boot  6.8.0-38-generic Mon Jul 22 14:23   still running
ubuntu   pts/0        172.24.0.1       Sun Jul 21 12:05 - 01:27  (13:21)
ubuntu   pts/0        172.24.0.1       Sat Jul 20 17:09 - 11:29  (18:20)
reboot   system boot  6.8.0-36-generic Sat Jul 20 16:54 - 01:27 (1+08:32)

wtmp begins Sat Jul 20 16:54:30 2024
```
#### Ejercicio 4 Mostrar información del sistema operativo
```bash
ubuntu@foo:~$ uname -a
Linux foo 6.8.0-38-generic #38-Ubuntu SMP PREEMPT_DYNAMIC Fri Jun  7 15:25:01 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux
```
#### Ejercicio 5 Listar dispositivos de carácter con sus permisos
    ls -l /dev | grep “^c”
#### Ejercicio 6 Mostrar informacion sobre el uso de memoria intercambiada unicamente
    cat /proc/meminfo | grep -i swap
#### Ejercicio 7 Investigar una manera de mostrar el tiempo de actividad del sistema de manera mas amigable
```bash
ubuntu@foo:~$ uptime
 14:42:04 up 8 days, 12 min,  2 users,  load average: 0.00, 0.00, 0.00
ubuntu@foo:~$ who -b
         system boot  2024-07-22 14:28
```