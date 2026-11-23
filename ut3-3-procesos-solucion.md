<p style="font-size: 1.3em;"><strong>CFGS Administración de Sistemas Informáticos y en Red (1º curso)</strong></p>

Material elaborado para el módulo **Implantación de Sistemas Operativos**

# UT3 · Práctica 3. Procesos y prioridades — SOLUCIÓN

> Entorno: Ubuntu 24.04 con 2 CPU, usuario `alumno`. Los **PID**, números de trabajo y porcentajes cambian en cada ejecución: las salidas son orientativas.

## Índice

- [Parte A · Trabajos](#parte-a--trabajos) (ejercicios 1 a 5)
- [Parte B · Supervisar procesos](#parte-b--supervisar-procesos) (ejercicios 6 a 12)
- [Parte C · Señales y prioridades](#parte-c--señales-y-prioridades) (ejercicios 13 a 19)
- [Pregunta final](#pregunta-final)

---

## Parte A · Trabajos

### Ejercicio 1. Suspender y pasar a segundo plano

```bash
sleep 900
# Ctrl+Z
jobs
bg
jobs
```

```text
^Z
[1]+  Detenido                sleep 900
[1]+  Detenido                sleep 900
[1]+ sleep 900 &
[1]+  Ejecutando              sleep 900 &
```

<kbd>Ctrl</kbd>+<kbd>Z</kbd> envía la señal `SIGTSTP` y **suspende** el proceso. `bg` lo reanuda en **segundo plano** (sin argumentos actúa sobre el trabajo actual, el marcado con `+`).

### Ejercicio 2. Tres `sleep` en segundo plano

```bash
sleep 1100 &
sleep 1200 &
sleep 1300 &
jobs -l
```

```text
[1]   4312 Ejecutando              sleep 900 &
[2]   4320 Ejecutando              sleep 1100 &
[3]-  4321 Ejecutando              sleep 1200 &
[4]+  4322 Ejecutando              sleep 1300 &
```

`jobs -l` añade el **PID** de cada trabajo.

### Ejercicio 3. Traer `sleep 1200` sin usar su número

```bash
fg %?1200
# Ctrl+C
```

`%?cadena` selecciona el trabajo cuya orden **contiene** esa cadena. (`%sleep` no serviría, porque hay varios trabajos que empiezan por `sleep` y daría "trabajo ambiguo".) Una vez en primer plano, <kbd>Ctrl</kbd>+<kbd>C</kbd> envía `SIGINT` y lo termina.

### Ejercicio 4. Número de trabajo frente a PID

- El **número de trabajo** (`%1`, `%2`...) lo asigna **Bash** y solo tiene sentido **dentro de esa terminal**: es una numeración local, pequeña y que empieza en 1 en cada shell.
- El **PID** lo asigna el **núcleo** y es único en **todo el sistema** mientras el proceso existe.

Desde **otra terminal**, `jobs` **no** muestra nada, porque cada shell solo conoce sus propios trabajos. Con `ps` **sí** se ven, porque `ps` consulta los procesos del sistema:

```bash
ps -u alumno | grep sleep
```

```text
   4312 pts/0    00:00:00 sleep
   4320 pts/0    00:00:00 sleep
   4322 pts/0    00:00:00 sleep
```

La columna `TTY` indica en qué terminal se lanzaron (`pts/0`).

### Ejercicio 5. `nano` suspendido

```bash
nano prueba.txt
# Ctrl+Z  (en el nano de Ubuntu 24.04 funciona directamente)
ps -o pid,stat,cmd -C nano
fg
# Ctrl+X para salir
```

```text
    PID STAT CMD
   4401 T    nano prueba.txt
```

El estado **`T`** significa **detenido** (*stopped*). Al traerlo con `fg` vuelve a estar en `S` (dormido esperando a que pulsemos una tecla) o `R`.

---

## Parte B · Supervisar procesos

### Ejercicio 6. Estilo BSD y estilo UNIX

```bash
ps aux      # estilo BSD (sin guion)
ps -ef      # estilo UNIX (con guion)
```

```text
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.3  23188 13900 ?        Ss   09:10   0:02 /sbin/init splash

UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 09:10 ?        00:00:02 /sbin/init splash
```

- Solo en **BSD** (`aux`): `%CPU`, `%MEM`, `VSZ` (memoria virtual), `RSS` (memoria física) y `STAT` (estado).
- Solo en **UNIX** (`-ef`): `PPID` (PID del proceso padre) y `C` (uso de CPU).
- Las dos tienen usuario, PID, terminal, hora de inicio, tiempo de CPU y orden.

### Ejercicio 7. Árbol de procesos

```bash
pstree -p
ps -ef --forest      # alternativa con ps
```

```text
systemd(1)─┬─ModemManager(812)─┬─...
           ├─gdm3(1050)───gdm-session-wor(1302)───...
           ...
           └─gnome-terminal-(3120)─┬─bash(3135)───pstree(4510)
```

- El **PID 1** es **`systemd`**, el primer proceso que lanza el núcleo y padre de todos los demás (en `ps` aparece como `/sbin/init`, que es un enlace a `systemd`).
- El padre de nuestra shell es el **emulador de terminal** (`gnome-terminal-server`), o `sshd` si entramos por SSH. Se puede comprobar con:

```bash
ps -o ppid= -p $$
ps -p $(ps -o ppid= -p $$)
```

`$$` es el PID de la shell actual.

### Ejercicio 8. Los 5 procesos que más memoria consumen

```bash
ps -eo pid,user,%cpu,%mem,comm --sort=-%mem | head -6
```

```text
    PID USER     %CPU %MEM COMMAND
   2410 alumno    3.1 10.2 gnome-shell
   3550 alumno    1.4  6.8 firefox
   ...
```

`-o` elige las columnas, `--sort=-%mem` ordena por memoria de mayor a menor y `head -6` muestra la cabecera y 5 procesos.

### Ejercicio 9. Procesos de `root` y de mi usuario

```bash
ps -U root --no-headers | wc -l
ps -U $USER --no-headers | wc -l
```

`--no-headers` evita contar la línea de la cabecera. (En Ubuntu de escritorio, `root` suele tener más de un centenar de procesos, muchos de ellos hilos del núcleo.)

### Ejercicio 10. `top`

**a) Cabecera:**

```text
top - 10:15:02 up  1:05,  1 user,  load average: 0,25, 0,30, 0,28
Tareas: 245 total,   1 ejecutar, 244 hibernar,   0 detener,   0 zombie
%Cpu(s):  2,1 us,  0,8 sy,  0,0 ni, 96,9 id,  0,1 wa,  0,0 hi,  0,1 si,  0,0 st
MiB Mem :   3915,2 total,    985,4 libre,   1650,3 usado,   1279,5 búf/caché
MiB Intercambio:   2048,0 total,   2048,0 libre,      0,0 usado.   2010,6 dispon Mem
```

| Línea | Qué indica |
|---|---|
| 1 | Hora, tiempo encendido (`up`), usuarios conectados y **carga media** del último minuto, 5 y 15 minutos |
| 2 | Número de procesos: total, en ejecución, dormidos, detenidos y **zombis** |
| 3 | Reparto de la CPU: `us` usuario, `sy` sistema, `ni` procesos con *nice* positivo, `id` ociosa, `wa` esperando E/S, `hi`/`si` interrupciones, `st` robado por el hipervisor |
| 4 | Memoria RAM: total, libre, usada y usada como búferes/caché |
| 5 | Intercambio (*swap*): total, libre, usado y memoria **disponible** de verdad para nuevos programas |

**b)** Ordenar por memoria: tecla <kbd>Shift</kbd>+<kbd>M</kbd> (por CPU: <kbd>Shift</kbd>+<kbd>P</kbd>).

**c)** Solo mis procesos: tecla <kbd>u</kbd> y escribir el usuario. (O lanzar `top -u alumno`.)

**d)** Cada CPU por separado: tecla <kbd>1</kbd>.

Se sale con <kbd>q</kbd>.

### Ejercicio 11. Carga media y núcleos

```bash
uptime
nproc
```

```text
 10:16:40 up  1:06,  1 user,  load average: 0,22, 0,28, 0,27
2
```

La carga media es el número medio de procesos **ejecutándose o esperando** CPU (o E/S en estado `D`). Hay que compararla con el número de **núcleos**: con 2 núcleos, una carga de 2 significa que están ocupados al 100 %. Aquí `0,22` es muy inferior a 2, así que la máquina **no está sobrecargada**. Si la carga se mantuviera por encima de 2 (sobre todo la de 15 minutos), sí lo estaría.

### Ejercicio 12. `watch` con la memoria libre

```bash
watch -n 2 free -h
```

```text
Cada 2,0s: free -h
               total       usado       libre  compartido   búf/caché   disponible
Mem:           3,8Gi       1,6Gi       985Mi        45Mi       1,2Gi       2,1Gi
Inter:         2,0Gi          0B       2,0Gi
```

Al abrir Firefox, `usado` sube varios cientos de MB y `libre`/`disponible` bajan; al cerrarlo vuelven aproximadamente a los valores anteriores. Se sale con <kbd>Ctrl</kbd>+<kbd>C</kbd>.

---

## Parte C · Señales y prioridades

### Ejercicio 13. Lista de señales

```bash
kill -l
```

```text
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
...
19) SIGSTOP	20) SIGTSTP ...
```

| Señal | Nº | Para qué sirve |
|---|---|---|
| `SIGTERM` | 15 | Pide al proceso que **termine de forma ordenada**: puede guardar datos y cerrar ficheros. Es la que envía `kill` por defecto. El proceso puede capturarla o ignorarla. |
| `SIGKILL` | 9 | Termina el proceso **inmediatamente**; la ejecuta el núcleo y el proceso **no puede capturarla ni ignorarla**. No le deja limpiar nada. |
| `SIGHUP` | 1 | "Se ha colgado la terminal": la reciben los procesos cuando se cierra su terminal. Muchos servicios la usan para **releer su configuración** sin reiniciarse. |
| `SIGSTOP` | 19 | **Detiene** (pausa) el proceso; tampoco se puede capturar ni ignorar. Se reanuda con `SIGCONT`. |

### Ejercicio 14. `SIGSTOP`, `SIGCONT` y `SIGTERM` por PID

```bash
sleep 3000 &
```

```text
[1] 4620
```

```bash
kill -STOP 4620
ps -o pid,stat,cmd -p 4620
kill -CONT 4620
ps -o pid,stat,cmd -p 4620
kill -TERM 4620
ps -p 4620
```

```text
    PID STAT CMD
   4620 T    sleep 3000
    PID STAT CMD
   4620 S    sleep 3000
    PID TTY          TIME CMD
[1]+  Terminado               sleep 3000
```

Tras `SIGSTOP` el estado es `T` (detenido); tras `SIGCONT` vuelve a `S` (dormido, porque `sleep` está esperando); tras `SIGTERM` el proceso ya no existe.

### Ejercicio 15. Terminar varios procesos por nombre

```bash
sleep 500 & sleep 600 & sleep 700 &
pkill sleep          # forma 1
sleep 500 & sleep 600 & sleep 700 &
killall sleep        # forma 2
```

- `pkill` busca por **patrón** en el nombre (como `pgrep`).
- `killall` busca por nombre **exacto**.

Las dos envían `SIGTERM` por defecto y solo pueden terminar procesos **nuestros** (sin `sudo`). Una tercera forma: `kill $(pgrep sleep)`.

### Ejercicio 16. Tres `yes` con prioridad normal

```bash
yes > /dev/null &
yes > /dev/null &
yes > /dev/null &
top
```

```text
    PID USER      PR  NI    VIRT    RES  %CPU  COMMAND
   4701 alumno    20   0    8240   1792  66,8  yes
   4702 alumno    20   0    8240   1792  66,4  yes
   4703 alumno    20   0    8240   1792  66,1  yes
```

Con 2 CPU hay 200 % de CPU disponible para repartir entre 3 procesos: cada uno recibe alrededor del **66 %**. (Con 1 CPU sería un 33 % cada uno.)

### Ejercicio 17. Tres `yes` más con prioridad mínima

La prioridad **mínima** es el valor *nice* **19**:

```bash
nice -n 19 yes > /dev/null &
nice -n 19 yes > /dev/null &
nice -n 19 yes > /dev/null &
top
```

```text
    PID USER      PR  NI  %CPU  COMMAND
   4701 alumno    20   0  66,4  yes
   4702 alumno    20   0  66,2  yes
   4703 alumno    20   0  66,0  yes
   4720 alumno    39  19   0,7  yes
   4721 alumno    39  19   0,7  yes
   4722 alumno    39  19   0,3  yes
```

- `NI` es el valor *nice* (de -20 a 19): 0 para los primeros y 19 para los nuevos.
- `PR` es la prioridad que usa el núcleo: **20 + NI**. Cuanto **mayor** el número, **menor** la prioridad.
- Los tres primeros siguen llevándose casi toda la CPU; los de *nice* 19 apenas reciben un 1 %, solo lo que sobra. El planificador reparte el tiempo según el peso de cada *nice*, y la diferencia entre 0 y 19 es enorme.

### Ejercicio 18. `renice`

```bash
renice -n 10 -p 4701
renice -n 0 -p 4701
sudo renice -n 0 -p 4701
```

```text
4701 (process ID) prioridad anterior 0, nueva prioridad 10
renice: fallo al establecer la prioridad de 4701 (process ID): Permiso denegado
4701 (process ID) prioridad anterior 10, nueva prioridad 0
```

- Un usuario normal puede **bajar** la prioridad de sus procesos (subir el *nice*), pero **no puede volver a subirla** (bajar el *nice*), ni siquiera al valor que tenía antes. Así se evita que un usuario se "cuele" por delante de los demás.
- Con `sudo` sí se puede: **root** puede poner cualquier valor, incluidos los negativos (-20 a -1).

En `top`, tras el `renice -n 10`, ese `yes` baja su `%CPU` y los otros dos de *nice* 0 se reparten más CPU.

### Ejercicio 19. Terminar todos los `yes`

```bash
killall yes      # o: pkill yes
top              # o: uptime
```

En `top`, la CPU vuelve a estar casi toda en `id` (ociosa). La carga media del último minuto baja enseguida; la de 5 y 15 minutos tarda más, porque son medias de un periodo más largo.

---

## Pregunta final

**Antes de matarlo:**

1. **Identificar** el proceso y a su dueño: `ps -fp PID` o `top` (qué orden es, qué usuario la lanzó, cuánto tiempo lleva).
2. Ver **qué está haciendo**: `ps -o pid,ppid,stat,etime,cmd -p PID`, qué ficheros tiene abiertos (`lsof -p PID`) o su árbol (`pstree -p PID`).
3. **Hablar con el compañero** (o con el responsable): puede ser un trabajo legítimo, como una compilación, una copia de seguridad o un cálculo largo.
4. Si molesta a los demás pero puede ser importante, una opción intermedia es **bajarle la prioridad** (`sudo renice -n 19 -p PID`) o **pausarlo** (`kill -STOP PID`) hasta aclararlo.

**Qué señal usar primero:** **`SIGTERM`** (`kill PID`), porque pide al proceso que termine **de forma ordenada**: puede guardar su trabajo, cerrar ficheros y borrar temporales, así que es menos probable que se pierdan o se corrompan datos. Solo si no responde tras unos segundos se usaría **`SIGKILL`** (`kill -9 PID`), que lo mata en el acto sin dejarle hacer nada.
