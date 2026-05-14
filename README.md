# IFS0 – Práctica 8

## Administración de Servidores Linux mediante CLI

---

# 1. Datos generales

- **Carrera:**  
- **Asignatura:** IFS0 – Analizando Necesidades de Infraestructura de Servidores  
- **Docente:**  
- **Unidad:** 3  
- **Práctica:** 8 – Administración de Servidores Linux  
- **Fecha de entrega:**  

## Integrantes
- Josue Alexander - GG100923
- Jaime Aparicio - AP100216
- Rodrigo Solis - SA100223

---

# 2. Introducción

En este documento se presenta el análisis del estado de un servidor Linux utilizando herramientas de línea de comandos (CLI).

El objetivo es evaluar el comportamiento del sistema en términos de:

- Uso de CPU
- Uso de memoria
- Almacenamiento
- Red
- Servicios
- Usuarios

y demostrar la capacidad de interpretar métricas para la toma de decisiones técnicas.

---

# 3. Información del sistema

## Comandos ejecutados

```bash
uname -a
hostnamectl
uptime
```

## Resultados

```bash
$ uname -a
Linux laptoplinux 6.12.74+deb13+1-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.12.74-2 (2026-03-08) x86_64 GNU/Linux
```

```bash
$ hostnamectl
Operating System: Debian GNU/Linux 13 (trixie)
Kernel: Linux 6.12.74+deb13+1-amd64
Architecture: x86-64
Hardware Model: Inspiron 15 3520
```

```bash
$ uptime
19:19:34 up 2:02, 1 user, load average: 2.10, 1.36, 1.09
```

## Análisis

- **Versión del sistema operativo:** Debian GNU/Linux 13 (trixie)
- **Arquitectura:** x86-64
- **Tiempo de actividad:** aproximadamente 2 horas
- **Load average:** 2.10, 1.36 y 1.09

## Interpretación técnica

El servidor mantiene un tiempo de actividad corto, por lo que el estado es reciente y estable desde el arranque.

La carga promedio es moderada, sin señales de saturación crítica; el comportamiento observado es coherente con un sistema en uso activo, pero todavía dentro de un rango aceptable para tareas normales.

---

# 4. Análisis de CPU y procesos

## Comandos ejecutados

```bash
top
ps aux
ps aux --sort=-%cpu | head
```

## Resultados

```bash
$ top -b -n 1 | head -n 20
top - 19:19:51 up 2:03, 1 user, load average: 1.74, 1.32, 1.08
Tasks: 413 total, 1 running, 412 sleeping, 0 stopped, 0 zombie
%Cpu(s): 11.7 us, 4.4 sy, 0.0 ni, 70.8 id, 13.1 wa, 0.0 hi, 0.0 si, 0.0 st
MiB Mem : 15684.3 total, 3718.9 free, 7243.1 used, 5758.1 buff/cache
```

```bash
$ ps aux --sort=-%cpu | head
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
AlexGomez+  118579 79.7  2.3 1463348076 378756 ?   Rl   19:18   0:56 /usr/share/code/code --type=zygote
AlexGomez+  118684 43.2  5.0 1480253572 809940 ?   Rl   19:18   0:30 /proc/self/exe --type=utility ...
AlexGomez+  118532 34.4  1.0 34515656 161860 ?     Rl   19:18   0:24 /usr/share/code/code --type=zygote --no-zygote-sandbox
```

## Análisis

- **Proceso con mayor uso de CPU:** procesos de Visual Studio Code y sus subprocesos
- **CPU utilizada:** hasta 79.7% en la captura de `ps` y 54.5% en `top`

## Interpretación técnica

No se observa una sobrecarga crítica del sistema.

El consumo elevado proviene principalmente del entorno gráfico y del editor, junto con extensiones y procesos de soporte, no de un daemon de servidor con comportamiento anómalo.

La carga se explica por el trabajo interactivo local y no por una condición de falla del sistema.

---

# 5. Análisis de memoria

## Comandos ejecutados

```bash
free -h
vmstat 1 2
```

## Resultados

```bash
$ free -h
               total        used        free      shared  buff/cache   available
Mem:            15Gi       7.0Gi       3.7Gi       686Mi       5.6Gi       8.3Gi
Swap:           15Gi          0B        15Gi
```

```bash
$ vmstat 1 2
procs -----------memory---------- ---swap-- -----io---- -system-- -------cpu-------
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st gu
 3  2      0 3817220 297540 5584444    0    0   569   442 3003    5  5  1 78 15  0  0
 0  2      0 3817032 297552 5584164    0    0     0   128 3626 13711  8  2 75 14  0  0
```

## Análisis

- **Memoria total:** 15 GiB
- **Memoria usada:** 7.0 GiB
- **Memoria libre:** 3.7 GiB
- **Uso de swap:** 0 B de 15 GiB

## Interpretación técnica

El uso de memoria es adecuado porque todavía existe una cantidad importante de memoria disponible y el sistema no está recurriendo a swap.

La ausencia de swap activo indica que la RAM está respondiendo bien y que el rendimiento no se está degradando por paginación excesiva.

---

# 6. Análisis de almacenamiento

## Comandos ejecutados

```bash
df -h
du -sh /var/log
lsblk
```

## Resultados

```bash
$ df -h
/dev/sda2       863G  118G  702G  15% /
/dev/sda1       975M  8.8M  966M   1% /boot/efi
/dev/dm-0       454G  386G   69G  85% /media/AlexGomez/OS
```

```bash
$ du -sh /var/log
577M    /var/log
```

```bash
$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 863.6G  0 disk
├─sda1   8:1    0   976M  0 part /boot/efi
└─sda2   8:2    0 862.6G  0 part /
```

## Análisis

- **Partición con mayor uso:** `/dev/dm-0` con 85%
- **Directorios con mayor consumo:** `/var/log` con 577 MiB

## Interpretación técnica

Existe un nivel de uso alto en la partición montada en `/media/mauriciodmo/OS`, aunque todavía no alcanza un umbral crítico.

El directorio `/var/log` ya ocupa un tamaño relevante y debe vigilarse para evitar crecimiento descontrolado.

Como medida preventiva se recomienda revisar rotación de logs, limpiar archivos temporales innecesarios y monitorear el consumo de disco de forma periódica.

---

# 7. Análisis de red

## Comandos ejecutados

```bash
ip a
ping 8.8.8.8
ss -tuln
```

## Resultados

```bash
$ ip a
inet 172.17.3.58/24 scope global dynamic noprefixroute wlp0s20f3
inet 10.10.10.76/24 scope global zttqh2xyhk
```

```bash
$ ping -c 4 8.8.8.8
4 packets transmitted, 0 received, 100% packet loss
```

```bash
$ ss -tuln
tcp LISTEN 0 128 0.0.0.0:22
tcp LISTEN 0 511 *:80
tcp LISTEN 0 5 0.0.0.0:9993
tcp LISTEN 0 200 127.0.0.1:5432
tcp LISTEN 0 4096 127.0.0.1:631
tcp LISTEN 0 4096 127.0.0.1:9050
```

## Análisis

- **Dirección IP del servidor:** `172.17.3.58/24` y `10.10.10.76/24`
- **Conectividad:** no hubo respuesta a ICMP hacia `8.8.8.8`
- **Puertos abiertos:** 22, 80, 9993, 631, 5432 y 9050

## Interpretación técnica

El servidor está accesible en la red local porque posee interfaces activas con dirección IPv4 válida.

Sin embargo, la prueba ICMP a Internet falló, lo que sugiere una restricción de red, una ruta no disponible o filtrado de paquetes.

Sí existen servicios expuestos, especialmente SSH y HTTP, por lo que conviene validar el endurecimiento de acceso y los controles de firewall.

---

# 8. Gestión de servicios

## Comandos ejecutados

```bash
systemctl status ssh
systemctl restart ssh
systemctl enable ssh
```

## Resultados

```bash
$ systemctl status ssh --no-pager --lines=20
ssh.service - OpenBSD Secure Shell server
Active: active (running)
Loaded: loaded ... enabled
Main PID: 1627 (sshd)
```

```bash
$ systemctl restart ssh
```

```bash
$ systemctl enable ssh

Synchronizing state of ssh.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable ssh
```

## Análisis

- **Estado del servicio:** activo y habilitado al arranque
- **Acción realizada:** consulta, reinicio y validación

## Interpretación técnica

SSH es crítico porque habilita la administración remota segura del servidor.

Si falla, se pierde un canal principal de administración y soporte remoto, lo que complica la resolución de incidentes y la continuidad operativa.

---

# 9. Gestión de usuarios y permisos

## Comandos ejecutados

```bash
who
useradd usuario_prueba
passwd usuario_prueba
chmod 755 /tmp/archivo.txt
```

## Resultados

```bash
$ who
AlexGomez seat0 2026-04-10 17:16 (:0)
```

```bash
$ useradd usuario_prueba
Command 'useradd' is available in the following places
 * /sbin/useradd
 * /usr/sbin/useradd
The command could not be located because '/sbin:/usr/sbin' is not included in the PATH environment variable.
This is most likely caused by the lack of administrative privileges associated with your user account.
useradd: command not found
```

```bash
$ passwd usuario_prueba
passwd: user 'usuario_prueba' does not exist
```

```bash
$ chmod 755 /tmp/archivo.txt

$ ls -l /tmp/archivo.txt
-rwxr-xr-x 1 AlexGomez AlexGomez ... /tmp/archivo.txt
```

## Análisis

- **Usuario activo observado:** `AlexGomez`
- **Usuario creado:** no se completó la creación
- **Permisos asignados:** `755` equivalente a `rwxr-xr-x`

## Interpretación técnica

El permiso `755` concede lectura, escritura y ejecución al propietario, y solo lectura y ejecución a grupo y otros.

Es útil para scripts ejecutables, pero si se aplica de forma incorrecta sobre archivos sensibles puede exponer contenido o permitir cambios no autorizados.

---

# 10. Automatización con Bash

## Script desarrollado

```bash
#!/bin/bash

echo "Estado del sistema:"
uptime

echo "Uso de memoria:"
free -h

echo "Uso de disco:"
df -h
```

## Explicación del script

### ¿Qué hace el script?

Muestra un resumen rápido del estado del sistema, memoria y almacenamiento.

### ¿Qué comandos utiliza?

- `uptime`
- `free -h`
- `df -h`

## Resultado de ejecución

```bash
Estado del sistema:
19:20:40 up 2:04, 1 user, load average: 1.72, 1.36, 1.11

Uso de memoria:
Mem: 15Gi total, 7.2Gi used, 3.5Gi free, 8.1Gi available
Swap: 15Gi total, 0B used

Uso de disco:
/dev/sda2 863G 118G 702G 15% /
/dev/dm-0 454G 386G 69G 85% /media/mauriciodmo/OS
```

## Mejora propuesta

Para uso real en producción se puede agregar:

- Validación de errores
- Registro con fecha y hora
- Salida a archivo de log
- Alertas cuando se superen umbrales de CPU, memoria o disco
- Diagnósticos adicionales:

```bash
ss -tuln
ps aux --sort=-%cpu | head
```

---

# 11. Conclusión

## ¿Qué componente representa mayor riesgo?

El almacenamiento montado en `/media/mauriciodmo/OS` debido a su 85% de uso.

## ¿El servidor está estable?

Sí, aunque con carga moderada y un uso de disco que requiere vigilancia.

## ¿Qué recomendaciones haría como administrador?

- Monitorear CPU y disco
- Revisar logs
- Confirmar conectividad externa
- Mantener SSH y servicios protegidos
- Aplicar firewall y actualizaciones de seguridad

---

# Restricciones

- No usar interfaz gráfica (GUI)
- No omitir comandos solicitados
- No presentar resultados sin análisis
- Todo debe ejecutarse en CLI