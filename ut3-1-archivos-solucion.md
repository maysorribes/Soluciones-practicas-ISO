<p style="font-size: 1.3em;"><strong>CFGS Administración de Sistemas Informáticos y en Red (1º curso)</strong></p>

Material elaborado para el módulo **Implantación de Sistemas Operativos**

# UT3 · Práctica 1. Archivos y directorios — SOLUCIÓN

> Entorno: Ubuntu 24.04. En las salidas el usuario es `alumno`, con directorio personal `/home/alumno`; en cada máquina saldrá el usuario de cada uno. Fechas y tamaños son orientativos.

## Índice

- [Parte A · Rutas y navegación](#parte-a--rutas-y-navegación) (ejercicios 1 a 9)
- [Parte B · Crear estructuras](#parte-b--crear-estructuras) (ejercicios 10 a 15)
- [Parte C · Copiar, mover y borrar](#parte-c--copiar-mover-y-borrar) (ejercicios 16 a 26)
- [Parte D · Empaquetar y comprimir](#parte-d--empaquetar-y-comprimir) (ejercicios 27 a 32)
- [Pregunta final](#pregunta-final)

---

## Parte A · Rutas y navegación

### Ejercicio 1. Directorio inicial

```bash
pwd
```

```text
/home/alumno
```

Al abrir la terminal estamos en nuestro **directorio personal**, cuya ruta absoluta es `/home/NOMBRE_DE_USUARIO`. También se puede ver con `echo $HOME`.

### Ejercicio 2. Crear `PRACTICA1`

```bash
mkdir ~/PRACTICA1
```

### Ejercicio 3. `PRUEBA1`, `PRUEBA2` y `PRUEBA2.1` con una sola orden

```bash
mkdir -p ~/PRACTICA1/PRUEBA1 ~/PRACTICA1/PRUEBA2/PRUEBA2.1
tree ~/PRACTICA1
```

```text
/home/alumno/PRACTICA1
├── PRUEBA1
└── PRUEBA2
    └── PRUEBA2.1
```

`-p` crea los directorios intermedios que falten (`PRUEBA2`) y no da error si ya existen. Si `tree` no está instalado: `sudo apt install tree`, o usar `ls -R ~/PRACTICA1`.

### Ejercicio 4. Ir a `PRUEBA2.1`

```bash
cd ~/PRACTICA1/PRUEBA2/PRUEBA2.1
pwd
```

```text
/home/alumno/PRACTICA1/PRUEBA2/PRUEBA2.1
```

### Ejercicio 5. Ir a la raíz y volver

```bash
cd /
cd -
```

`cd -` vuelve al directorio anterior (el guardado en la variable `OLDPWD`) y además muestra su ruta.

### Ejercicio 6. Volver al directorio personal

```bash
cd
```

`cd` sin argumentos lleva al directorio personal. `cd ~` hace lo mismo, pero es más larga.

### Ejercicio 7. Contenido de la raíz sin moverse

```bash
ls /
ls -l /
```

`-l` muestra el formato largo: tipo y permisos, enlaces, propietario, grupo, tamaño, fecha y nombre.

### Ejercicio 8. ¿Son equivalentes?

```bash
cd /etc ; pwd
cd ; cd ../../etc ; pwd
cd ; cd ; pwd
```

```text
/etc
/etc
/home/alumno
```

- `cd /etc` usa una **ruta absoluta**: lleva a `/etc` esté donde esté.
- `cd ../../etc` usa una **ruta relativa**: desde `/home/alumno`, `..` es `/home`, `../..` es `/` y por tanto llega a `/etc`. Solo es equivalente porque el directorio personal está **dos niveles por debajo** de la raíz; desde otro sitio llevaría a otro lugar o daría error.
- `cd` sin argumentos lleva al **directorio personal**, no a `/etc`.

Por tanto las dos primeras son equivalentes **en este caso**, y la tercera no.

### Ejercicio 9. Borrar `PRUEBA2.1` sin moverse

```bash
rmdir ~/PRACTICA1/PRUEBA2/PRUEBA2.1
```

`rmdir` solo borra directorios **vacíos**, que es lo más seguro. (`rm -r` también valdría.)

---

## Parte B · Crear estructuras

### Ejercicio 10. Estructura con el menor número de órdenes

Se puede hacer con **una sola orden** usando `-p` y la expansión de llaves:

```bash
cd ~/PRACTICA1
mkdir -p COPIAS MODELOS/{IMPRESOS,INFORMES,INSTANCIAS} TEMPORAL TRABAJOS/TEXTOS
tree
```

```text
.
├── COPIAS
├── MODELOS
│   ├── IMPRESOS
│   ├── INFORMES
│   └── INSTANCIAS
├── PRUEBA1
├── PRUEBA2
├── TEMPORAL
└── TRABAJOS
    └── TEXTOS
```

`MODELOS/{IMPRESOS,INFORMES,INSTANCIAS}` lo expande el shell a `MODELOS/IMPRESOS MODELOS/INFORMES MODELOS/INSTANCIAS`. (`PRUEBA1` y `PRUEBA2` quedan de la parte A; se pueden borrar con `rmdir PRUEBA1 PRUEBA2`.)

### Ejercicio 11. `INF`, `Inf` e `InF`

```bash
mkdir COPIAS/INF
mkdir COPIAS/INF
mkdir COPIAS/Inf COPIAS/InF
ls COPIAS
```

```text
mkdir: no se puede crear el directorio «COPIAS/INF»: El fichero ya existe
INF  InF  Inf
```

- Dos directorios con el **mismo nombre** en el mismo sitio **no** son posibles: el nombre identifica al fichero dentro de su directorio.
- `Inf` e `InF` **sí** se pueden crear, porque los sistemas de archivos de Linux (ext4) **distinguen mayúsculas y minúsculas**: `INF`, `Inf` e `InF` son tres nombres distintos. (En Windows con NTFS, por defecto, no se podría.)

### Ejercicio 12. Adónde lleva cada `cd` desde `INFORMES`

Partimos de `~/PRACTICA1/MODELOS/INFORMES`:

| Orden | Destino |
|---|---|
| `cd ..` | `~/PRACTICA1/MODELOS` |
| `cd ../../` | `~/PRACTICA1` |
| `cd ../IMPRESOS` | `~/PRACTICA1/MODELOS/IMPRESOS` |
| `cd ../../TEXTOS` | **Error**: `~/PRACTICA1/TEXTOS` no existe (`TEXTOS` está dentro de `TRABAJOS`) |

Comprobación (volviendo a `INFORMES` antes de cada una):

```bash
cd ~/PRACTICA1/MODELOS/INFORMES ; cd .. ; pwd
cd ~/PRACTICA1/MODELOS/INFORMES ; cd ../../ ; pwd
cd ~/PRACTICA1/MODELOS/INFORMES ; cd ../IMPRESOS ; pwd
cd ~/PRACTICA1/MODELOS/INFORMES ; cd ../../TEXTOS
```

```text
/home/alumno/PRACTICA1/MODELOS
/home/alumno/PRACTICA1
/home/alumno/PRACTICA1/MODELOS/IMPRESOS
bash: cd: ../../TEXTOS: No existe el fichero o el directorio
```

### Ejercicio 13. De `IMPRESOS` a `INSTANCIAS`

```bash
cd /home/alumno/PRACTICA1/MODELOS/INSTANCIAS   # ruta absoluta (o ~/PRACTICA1/MODELOS/INSTANCIAS)
cd ../INSTANCIAS                               # ruta relativa
```

### Ejercicio 14. `mkdir carpeta de trabajo` con y sin comillas

```bash
cd ~/PRACTICA1/TEMPORAL
mkdir carpeta de trabajo
mkdir "carpeta de trabajo"
ls -l
```

```text
drwxrwxr-x 2 alumno alumno 4096 nov 23 12:31 carpeta
drwxrwxr-x 2 alumno alumno 4096 nov 23 12:31 carpeta de trabajo
drwxrwxr-x 2 alumno alumno 4096 nov 23 12:31 de
drwxrwxr-x 2 alumno alumno 4096 nov 23 12:31 trabajo
```

- **Sin comillas**, el shell separa los argumentos por los espacios: `mkdir` recibe **tres** nombres y crea `carpeta`, `de` y `trabajo`.
- **Con comillas**, todo es **un único argumento** y se crea un solo directorio con espacios en el nombre. (También vale escapar los espacios: `mkdir carpeta\ de\ trabajo`.)

Dejar solo el de espacios:

```bash
rmdir carpeta de trabajo
ls
```

```text
carpeta de trabajo
```

### Ejercicio 15. Directorio oculto

```bash
mkdir ~/PRACTICA1/.INVISIBLE
ls ~/PRACTICA1
ls -a ~/PRACTICA1
```

En Linux un fichero o directorio es **oculto** si su nombre empieza por punto. `ls` no lo muestra; para verlo hay que usar `ls -a` (todos, incluidos `.` y `..`) o `ls -A` (todos menos `.` y `..`).

---

## Parte C · Copiar, mover y borrar

### Ejercicio 16. Copiar `/etc/os-release` como `sistema.txt`

```bash
cp /etc/os-release ~/PRACTICA1/TRABAJOS/TEXTOS/sistema.txt
cat ~/PRACTICA1/TRABAJOS/TEXTOS/sistema.txt
```

```text
PRETTY_NAME="Ubuntu 24.04.5 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
...
```

### Ejercicio 17. Copiar `sistema.txt` a tres sitios y comprobarlo

`cp` solo admite **un destino** por orden, así que hacen falta tres copias:

```bash
cd ~/PRACTICA1
cp TRABAJOS/TEXTOS/sistema.txt COPIAS/
cp TRABAJOS/TEXTOS/sistema.txt MODELOS/IMPRESOS/
cp TRABAJOS/TEXTOS/sistema.txt MODELOS/INFORMES/
ls COPIAS/sistema.txt MODELOS/IMPRESOS/sistema.txt MODELOS/INFORMES/sistema.txt
```

```text
COPIAS/sistema.txt
MODELOS/IMPRESOS/sistema.txt
MODELOS/INFORMES/sistema.txt
```

También vale `ls */sistema.txt */*/sistema.txt`, que además lo muestra en `TRABAJOS/TEXTOS`.

### Ejercicio 18. Copiar `/etc/passwd` como `listado_usuarios.txt`

Se puede copiar y renombrar en un solo paso:

```bash
cp /etc/passwd ~/PRACTICA1/TEMPORAL/listado_usuarios.txt
```

(O copiarlo y después renombrarlo con `mv passwd listado_usuarios.txt`.)

### Ejercicio 19. Copiar los `.conf` de `/etc`

```bash
cp /etc/*.conf ~/PRACTICA1/COPIAS/
```

- **No hace falta `sudo`** porque para **copiar** solo se necesita permiso de **lectura** sobre el origen, y casi todos los `.conf` de `/etc` tienen permiso de lectura para todos (`-rw-r--r--`). Lo que sí exige `sudo` es **escribir** en `/etc`, no leer de él. El destino es nuestro directorio, donde tenemos permiso de escritura.
- **Puede aparecer algún error**, según el sistema. Los más habituales:
    - `cp: -r no especificado; se omite el directorio '/etc/xxx.conf'`, si algún **directorio** acaba en `.conf`, porque `cp` sin `-r` no copia directorios.
    - `Permiso denegado`, si algún `.conf` no tiene lectura para los demás.
    - `No existe el fichero`, si es un **enlace simbólico roto** (en nuestra máquina pasó con `/etc/vconsole.conf`).

### Ejercicio 20. Copiar `/etc/apt` completo

```bash
cp -r /etc/apt ~/PRACTICA1/COPIAS/
ls ~/PRACTICA1/COPIAS/apt
```

```text
apt.conf.d  auth.conf.d  keyrings  preferences.d  sources.list  sources.list.d  trusted.gpg.d
```

`-r` (recursivo) copia el directorio con todos sus subdirectorios y ficheros.

### Ejercicio 21. Mover `TEXTOS` dentro de `MODELOS`

```bash
cd ~/PRACTICA1
mv TRABAJOS/TEXTOS MODELOS/
```

`mv` mueve directorios con todo su contenido sin necesidad de `-r`.

### Ejercicio 22. Renombrar `TEMPORAL`

```bash
mv TEMPORAL TMP_PRACTICA
```

En Linux, renombrar es **mover** a un nombre nuevo en el mismo directorio.

### Ejercicio 23. Ocultar `sistema.txt` de `INFORMES`

```bash
mv MODELOS/INFORMES/sistema.txt MODELOS/INFORMES/.sistema.txt
ls -a MODELOS/INFORMES
```

Basta con añadir un punto delante del nombre.

### Ejercicio 24. Crear `dira`…`dird` y borrar todos menos `dira`

```bash
cd ~/PRACTICA1/TMP_PRACTICA
mkdir dira dirb dirc dird        # o: mkdir dir{a,b,c,d}
rmdir dir[b-d]                   # o: rmdir dir[!a]
ls
```

```text
carpeta de trabajo  dira  listado_usuarios.txt
```

`dir[b-d]` encaja con `dirb`, `dirc` y `dird`. Otra opción: `rmdir dir{b,c,d}`.

### Ejercicio 25. Copiar el directorio personal de otro usuario

```bash
cp -r /home/maria ~/PRACTICA1/COPIAS/
```

```text
cp: no se puede acceder a '/home/maria': Permiso denegado
```

**No se puede copiar.** En Ubuntu 24.04 los directorios personales se crean con permisos `drwxr-x---` (750): los demás usuarios no tienen ni lectura (listar) ni ejecución (entrar) sobre ellos. Para copiar hace falta leer el contenido, y no tenemos permiso. (En versiones anteriores de Ubuntu el permiso era 755 y se podían copiar los ficheros legibles por todos, pero no los privados.)

Con `/etc/skel`, que es legible por todos, sí funciona:

```bash
cp -r /etc/skel ~/PRACTICA1/COPIAS/
ls -la ~/PRACTICA1/COPIAS/skel
```

```text
-rw-r--r-- 1 alumno alumno  220 nov 23 12:31 .bash_logout
-rw-r--r-- 1 alumno alumno 3771 nov 23 12:31 .bashrc
-rw-r--r-- 1 alumno alumno  807 nov 23 12:31 .profile
```

Al copiar sin `sudo`, las copias pasan a ser **nuestras**.

### Ejercicio 26. Borrar `COPIAS` con y sin confirmación

```bash
rm -ri ~/PRACTICA1/COPIAS     # pregunta por cada elemento; cancelamos con Ctrl+C
rm -r ~/PRACTICA1/COPIAS      # borra sin preguntar
```

```text
rm: ¿descender al directorio '/home/alumno/PRACTICA1/COPIAS'? s
rm: ¿borrar el fichero regular '/home/alumno/PRACTICA1/COPIAS/deluser.conf'? s
rm: ¿borrar el fichero regular '/home/alumno/PRACTICA1/COPIAS/e2scrub.conf'? ^C
```

Lo que se confirmó antes de cancelar ya está borrado; el resto se elimina con la segunda orden. Si algún fichero estuviera protegido contra escritura, `rm -r` preguntaría; `rm -rf` no pregunta nunca (hay que usarlo con mucho cuidado).

---

## Parte D · Empaquetar y comprimir

### Ejercicio 27. Empaquetar sin comprimir

```bash
cd ~
tar -cf ~/copia.tar PRACTICA1
```

`-c` crea un paquete y `-f` indica el nombre del fichero. Se ejecuta desde `~` para que dentro del paquete las rutas empiecen por `PRACTICA1/`.

### Ejercicio 28. Empaquetar y comprimir con gzip y bzip2

```bash
tar -czf ~/copia.tar.gz PRACTICA1     # z = gzip
tar -cjf ~/copia.tar.bz2 PRACTICA1    # j = bzip2
```

### Ejercicio 29. Comparar tamaños

Hecho **antes** de borrar `COPIAS` en el ejercicio 26 (si se hace después, los números serán mucho más pequeños, pero la conclusión es la misma):

```bash
ls -lh ~/copia.*
du -sh --apparent-size ~/PRACTICA1
```

```text
-rw-rw-r-- 1 alumno alumno 130K nov 23 12:31 /home/alumno/copia.tar
-rw-rw-r-- 1 alumno alumno  29K nov 23 12:31 /home/alumno/copia.tar.bz2
-rw-rw-r-- 1 alumno alumno  29K nov 23 12:31 /home/alumno/copia.tar.gz
71K	/home/alumno/PRACTICA1
```

- Los **comprimidos** ocupan bastante menos que el `.tar`. Normalmente **bzip2** comprime algo más que gzip (aunque es más lento); con ficheros tan pequeños como estos la diferencia es mínima.
- El `.tar` ocupa **más o menos lo mismo** que la carpeta porque **solo empaqueta**: junta todos los ficheros en uno, con su contenido tal cual, y añade una cabecera de 512 bytes por cada fichero o directorio (nombre, permisos, propietario, fechas). No reduce nada. Por eso aquí sale incluso algo mayor que los 71 KB de datos reales: hay muchos ficheros pequeños y cada uno añade su cabecera.

### Ejercicio 30. Ver el contenido sin extraer

```bash
tar -tzf ~/copia.tar.gz
```

```text
PRACTICA1/
PRACTICA1/COPIAS/
PRACTICA1/COPIAS/deluser.conf
...
```

`-t` lista el contenido. Con `-tvzf` se ven también permisos, propietario y tamaño.

### Ejercicio 31. Extraer en `/tmp/restaurar_TUUSUARIO`

```bash
mkdir /tmp/restaurar_alumno
tar -xzf ~/copia.tar.gz -C /tmp/restaurar_alumno
tree /tmp/restaurar_alumno
```

`-x` extrae y `-C` indica el directorio de destino. Se comprueba con `tree` o con `ls -R` que aparece `PRACTICA1` con toda su estructura.

### Ejercicio 32. Limpiar

```bash
rm -r /tmp/restaurar_alumno
rm ~/copia.tar ~/copia.tar.gz ~/copia.tar.bz2     # o: rm ~/copia.tar*
```

---

## Pregunta final

- **Empaquetar** es **juntar** muchos ficheros y directorios en **un solo fichero**, conservando la estructura de directorios, los permisos, los propietarios y las fechas. No reduce el tamaño. Es lo que hace `tar` por sí solo (`.tar`).
- **Comprimir** es **reducir el tamaño** de un fichero codificando sus datos de forma más eficiente (`gzip`, `bzip2`, `xz`). Estos compresores trabajan sobre **un único fichero**: no saben guardar una carpeta con su estructura.

Por eso con `tar` se hacen las dos cosas a la vez: `tar` reúne todo en un fichero y, con `-z`, `-j` o `-J`, le pasa ese fichero al compresor en el mismo paso. Así se obtiene un único fichero pequeño (`.tar.gz`, `.tar.bz2`, `.tar.xz`) que conserva toda la estructura, ideal para copias de seguridad y para enviar o descargar software.
