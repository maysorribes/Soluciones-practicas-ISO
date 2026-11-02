<p style="font-size: 1.3em;"><strong>CFGS Administración de Sistemas Informáticos y en Red (1º curso)</strong></p>

Material elaborado para el módulo **Implantación de Sistemas Operativos**

# UT3 · Práctica 5. Usuarios y grupos — SOLUCIÓN

> Entorno: Ubuntu 24.04 con un usuario con `sudo`. Los **UID y GID** que aparecen en las salidas son orientativos: en cada máquina pueden ser distintos según los usuarios que ya existieran.

## Índice

- [Parte A · Crear usuarios y grupos](#parte-a--crear-usuarios-y-grupos) (ejercicios 1 a 7)
- [Parte B · Plantillas y políticas de contraseñas](#parte-b--plantillas-y-políticas-de-contraseñas) (ejercicios 8 a 10)
- [Parte C · Modificar y eliminar](#parte-c--modificar-y-eliminar) (ejercicios 11 a 16)
- [Parte D · Los ficheros de cuentas](#parte-d--los-ficheros-de-cuentas) (ejercicios 17 a 20)
- [Pregunta final](#pregunta-final)

---

## Parte A · Crear usuarios y grupos

### Ejercicio 1. Grupo `alumnos` y usuario `alumno1`

```bash
sudo groupadd alumnos
sudo useradd -m -s /bin/bash -G alumnos alumno1
sudo passwd alumno1
```

- `-m` crea el directorio personal (`/home/alumno1`).
- `-s /bin/bash` le asigna la shell Bash.
- `-G alumnos` lo añade a `alumnos` como **grupo secundario** (G mayúscula).

Comprobación:

```text
$ id alumno1
uid=1001(alumno1) gid=1002(alumno1) groups=1002(alumno1),1001(alumnos)
```

### Ejercicio 2. Grupo `progs`

```bash
sudo groupadd progs
getent group progs
```

```text
progs:x:1003:
```

### Ejercicio 3. Usuario `juan` con `progs` como grupo primario

```bash
sudo useradd -m -s /bin/bash -g progs juan
```

`-g progs` (g minúscula) fija el **grupo primario**. Comprobación:

```text
$ id juan
uid=1002(juan) gid=1003(progs) groups=1003(progs)
```

### Ejercicio 4. Grupo `externos`

```bash
sudo groupadd externos
getent group externos
```

### Ejercicio 5. Usuario `luis`

```bash
sudo useradd -m -s /bin/bash -g externos -G progs luis
```

`-g externos` es el grupo primario y `-G progs` el secundario. (El enunciado no pide `-m` ni `-s`, pero se ponen para que `luis` tenga un usuario completo; en el ejercicio 12 se le cambia la shell.)

```text
$ id luis
uid=1003(luis) gid=1004(externos) groups=1004(externos),1003(progs)
```

### Ejercicio 6. Contraseña de `luis`

```bash
sudo passwd luis          # se escribe SuJJh_4 dos veces
```

Alternativa sin interacción, útil en scripts:

```bash
echo 'luis:SuJJh_4' | sudo chpasswd
```

Comprobación: `sudo passwd -S luis` muestra `P` (*password set*) y la fecha del cambio.

### Ejercicio 7. Grupos de los tres usuarios

```text
$ id alumno1
uid=1001(alumno1) gid=1002(alumno1) groups=1002(alumno1),1001(alumnos)
$ id juan
uid=1002(juan) gid=1003(progs) groups=1003(progs)
$ id luis
uid=1003(luis) gid=1004(externos) groups=1004(externos),1003(progs)
```

**¿Por qué `juan` no tiene un grupo `juan` y `alumno1` sí?** Porque en Ubuntu está activado `USERGROUPS_ENAB yes` en `/etc/login.defs`: cuando se crea un usuario **sin indicar grupo primario**, `useradd` crea automáticamente un **grupo privado** con su mismo nombre y lo usa como primario. A `alumno1` no le indicamos grupo primario (`-G` solo añade secundarios), así que se creó el grupo `alumno1`. A `juan` le indicamos `-g progs`, así que no hacía falta crear ningún grupo nuevo.

```bash
grep USERGROUPS_ENAB /etc/login.defs
```

---

## Parte B · Plantillas y políticas de contraseñas

### Ejercicio 8. Plantilla de los directorios personales (`/etc/skel`)

Todo lo que hay en `/etc/skel` se **copia** al directorio personal de cada usuario nuevo creado con `useradd -m`.

```bash
sudo mkdir /etc/skel/entregas
echo 'Normas del aula de informática' | sudo tee /etc/skel/normas.txt
ls -la /etc/skel

sudo useradd -m -s /bin/bash ana
sudo passwd ana
sudo ls -la /home/ana
```

```text
drwxr-x--- 3 ana  ana  4096 nov  2 13:23 .
drwxr-xr-x 6 root root 4096 nov  2 13:23 ..
-rw-r--r-- 1 ana  ana   220 mar 31  2024 .bash_logout
-rw-r--r-- 1 ana  ana  3771 mar 31  2024 .bashrc
-rw-r--r-- 1 ana  ana   807 mar 31  2024 .profile
drwxr-xr-x 2 ana  ana  4096 nov  2 13:23 entregas
-rw-r--r-- 1 ana  ana    32 nov  2 13:23 normas.txt
```

**¿Los tiene `juan`?** No:

```bash
sudo ls -la /home/juan     # solo .bash_logout, .bashrc y .profile
```

El contenido de `/etc/skel` se copia **solo en el momento de crear** el usuario. Cambiar la plantilla después no afecta a los usuarios que ya existían; habría que copiarles los ficheros a mano (y cambiar el propietario con `chown`).

### Ejercicio 9. Caducidad de 30 días para todos

Son **dos** cosas distintas:

**a) Usuarios que se creen a partir de ahora** → valor por defecto en `/etc/login.defs`:

```bash
sudo nano /etc/login.defs       # cambiar la línea a:  PASS_MAX_DAYS   30
grep ^PASS_MAX_DAYS /etc/login.defs
```

**b) Usuarios que ya existen** → `login.defs` no se aplica con efecto retroactivo, así que hay que cambiarlos uno a uno con `chage -M`:

```bash
for u in alumno1 juan luis ana; do sudo chage -M 30 "$u"; done
```

Comprobación (para cada usuario):

```text
$ sudo chage -l juan | grep Maximum
Maximum number of days between password change		: 30
```

### Ejercicio 10. Configuración de la cuenta de `ana`

```bash
sudo chage -W 5 ana             # a) avisar 5 días antes de que caduque la contraseña
sudo chage -d 0 ana             # b) obligar a cambiarla en el primer inicio de sesión
sudo chage -E 2027-06-18 ana    # c) la cuenta expira el último día lectivo
```

- `-d 0` pone la fecha del último cambio de contraseña en el día 0 (1 de enero de 1970), así que la contraseña figura como caducada. Equivale a `sudo passwd -e ana`.
- Para c) se ha tomado el **18 de junio de 2027**, último día lectivo del curso 2026-2027 en la Comunitat Valenciana. Si el centro tiene otra fecha para FP, se cambia por la suya.

```text
$ sudo chage -l ana
Last password change					: password must be changed
Password expires					: password must be changed
Password inactive					: password must be changed
Account expires						: Jun 18, 2027
Minimum number of days between password change		: 0
Maximum number of days between password change		: 30
Number of days of warning before password expires	: 5
```

Entrar como `ana`:

```text
$ su - ana
Password:
You are required to change your password immediately (administrator enforced).
Changing password for ana.
Current password:
New password:
Retype new password:
```

---

## Parte C · Modificar y eliminar

### Ejercicio 11. Añadir `juan` a `alumnos` sin quitarle de los demás

```bash
sudo usermod -aG alumnos juan
id juan
```

```text
uid=1002(juan) gid=1003(progs) groups=1003(progs),1001(alumnos)
```

La clave es la **`-a`** (*append*). Con `usermod -G alumnos juan` (sin `-a`), la lista de grupos secundarios se **sustituiría** por `alumnos` y `juan` perdería los demás secundarios que tuviera. Alternativa equivalente: `sudo gpasswd -a juan alumnos`.

### Ejercicio 12. Comentario y shell de `luis`

```bash
sudo usermod -c "Luis García (externo)" -s /bin/sh luis
getent passwd luis
```

```text
luis:x:1003:1004:Luis García (externo):/home/luis:/bin/sh
```

### Ejercicio 13. `juan` como administrador de `progs`

```bash
sudo gpasswd -A juan progs
sudo getent gshadow progs
```

```text
progs:!:juan:luis
```

El tercer campo de `/etc/gshadow` es la lista de **administradores** del grupo (`juan`) y el cuarto, la de miembros (`luis`).

`juan` aún no tiene contraseña (se creó sin ella), así que primero se le pone una para poder entrar:

```bash
sudo passwd juan
su - juan
gpasswd -a alumno1 progs        # sin sudo: juan es administrador del grupo
exit
id alumno1
```

```text
Adding user alumno1 to group progs
uid=1001(alumno1) gid=1002(alumno1) groups=1002(alumno1),1001(alumnos),1003(progs)
```

Si lo intentara otro usuario que no es administrador del grupo, `gpasswd` respondería `Permission denied`.

### Ejercicio 14. Bloquear y desbloquear a `luis`

```bash
sudo usermod -L luis                  # o: sudo passwd -l luis
sudo grep ^luis: /etc/shadow
```

```text
luis:!$y$j9T$.otHKqEpDeLH...:20759:0:30:7:::
```

El bloqueo se ve en el **`!` delante del hash** de la contraseña: el hash ya no coincide con ninguna contraseña, así que no se puede entrar con ella.

Prueba (desde el usuario normal, **no** desde root, porque a root `su` no le pide contraseña):

```text
$ su - luis
Password:            ← se escribe SuJJh_4
su: Authentication failure
```

Desbloquear y comprobar que el `!` ha desaparecido:

```bash
sudo usermod -U luis                  # o: sudo passwd -u luis
sudo grep ^luis: /etc/shadow
su - luis                             # ahora sí entra
```

> Nota: `usermod -L` bloquea **la contraseña**, no la cuenta. Un usuario con claves SSH podría seguir entrando. Para bloquear la cuenta entera se le pone además una fecha de expiración pasada: `sudo usermod -L -e 1 luis`.

### Ejercicio 15. Eliminar `ana` y `alumno1`

```bash
sudo userdel -r ana          # -r: borra también /home/ana (y su buzón de correo)
sudo userdel alumno1         # sin -r: el directorio se conserva
ls -ld /home/ana /home/alumno1
```

```text
ls: cannot access '/home/ana': No such file or directory
drwxr-x--- 2 1001 1002 4096 nov  2 13:23 /home/alumno1
```

(Puede aparecer el aviso `userdel: ana mail spool (/var/mail/ana) not found`: solo indica que `ana` no tenía buzón, no es un error.)

**¿Qué pasa con `/home/alumno1`?** Sigue existiendo con todo su contenido, pero ahora aparece como propietario un **número** (UID `1001`, GID `1002`) en lugar de un nombre. Los ficheros guardan el UID y el GID, no el nombre; `ls` traduce el número a nombre consultando `/etc/passwd` y `/etc/group`, y como `alumno1` y su grupo privado ya no existen, no puede traducirlo.

**Peligro:** si más adelante se crea otro usuario y recibe ese mismo UID `1001`, pasará a ser el **dueño de esos ficheros**. Por eso, antes de borrar un usuario sin `-r`, conviene archivar o cambiar el propietario de sus datos.

### Ejercicio 16. Eliminar el grupo `externos`

```text
$ sudo groupdel externos
groupdel: cannot remove the primary group of user 'luis'
```

No se puede porque `externos` es el **grupo primario** de `luis`: todo usuario debe tener siempre un grupo primario. Antes habría que:

- cambiar el grupo primario de `luis` a otro (por ejemplo, `sudo usermod -g progs luis`), o
- eliminar al usuario `luis`.

Después ya funcionaría `sudo groupdel externos`.

---

## Parte D · Los ficheros de cuentas

### Ejercicio 17. Las líneas de `juan` y `luis`

```bash
grep -E '^(juan|luis):' /etc/passwd
sudo grep -E '^(juan|luis):' /etc/shadow
grep -E 'juan|luis' /etc/group
sudo grep -E 'juan|luis' /etc/gshadow
```

**`/etc/passwd`**

```text
juan:x:1002:1003::/home/juan:/bin/bash
luis:x:1003:1004:Luis García (externo):/home/luis:/bin/sh
```

| Campo | Ejemplo (`luis`) | Significado |
| ----- | ---------------- | ----------- |
| 1 | `luis` | Nombre de usuario |
| 2 | `x` | La contraseña está en `/etc/shadow` |
| 3 | `1003` | UID |
| 4 | `1004` | GID del grupo **primario** (`externos`) |
| 5 | `Luis García (externo)` | Comentario o GECOS (nombre completo). En `juan` está vacío |
| 6 | `/home/luis` | Directorio personal |
| 7 | `/bin/sh` | Shell de inicio de sesión |

**`/etc/shadow`**

```text
juan:$y$j9T$...:20759:0:30:7:::
luis:$y$j9T$...:20759:0:30:7:::
```

| Campo | Ejemplo | Significado |
| ----- | ------- | ----------- |
| 1 | `luis` | Nombre de usuario |
| 2 | `$y$j9T$...` | Contraseña cifrada. `!` delante = bloqueada; `!` o `*` solo = sin contraseña válida |
| 3 | `20759` | Fecha del último cambio de contraseña, en **días desde el 1/1/1970** (20759 = 2 de noviembre de 2026). `0` = debe cambiarla |
| 4 | `0` | Días mínimos entre cambios |
| 5 | `30` | Días máximos de validez (el del ejercicio 9) |
| 6 | `7` | Días de aviso antes de que caduque |
| 7 | *(vacío)* | Días de gracia tras caducar antes de desactivar la cuenta |
| 8 | *(vacío)* | Fecha de expiración de la **cuenta** (días desde 1970). En `ana` era la de junio de 2027 |
| 9 | *(vacío)* | Reservado |

**`/etc/group`** (no existen grupos `juan` ni `luis`; aparecen como miembros de otros grupos)

```text
alumnos:x:1001:juan
progs:x:1003:luis
```

| Campo | Ejemplo | Significado |
| ----- | ------- | ----------- |
| 1 | `progs` | Nombre del grupo |
| 2 | `x` | La contraseña del grupo está en `/etc/gshadow` |
| 3 | `1003` | GID |
| 4 | `luis` | Miembros **secundarios**, separados por comas |

**`/etc/gshadow`**

```text
alumnos:!::juan
progs:!:juan:luis
```

| Campo | Ejemplo | Significado |
| ----- | ------- | ----------- |
| 1 | `progs` | Nombre del grupo |
| 2 | `!` | Contraseña del grupo (`!` = sin contraseña) |
| 3 | `juan` | **Administradores** del grupo (ejercicio 13) |
| 4 | `luis` | Miembros secundarios |

### Ejercicio 18. ¿Por qué `progs` muestra a `luis` y no a `juan`?

Porque el cuarto campo de `/etc/group` solo lista los miembros **secundarios**. `progs` es el grupo **primario** de `juan`, y esa pertenencia no se guarda en `/etc/group`, sino en el cuarto campo de **`/etc/passwd`** (el GID `1003` de su línea). `luis` tiene `progs` como secundario, por eso sí aparece.

(En `/etc/gshadow` sí aparece `juan`, pero en el campo de **administradores**, no en el de miembros.)

### Ejercicio 19. Usuarios normales del sistema

Los usuarios normales tienen UID a partir de `UID_MIN` (1000 en Ubuntu, ver `/etc/login.defs`). Se excluye el 65534, que es la cuenta `nobody`:

```bash
awk -F: '$3 >= 1000 && $3 < 65534 {print $1}' /etc/passwd
```

```text
profe
juan
luis
```

(`profe` = el usuario creado al instalar la máquina. Con `getent passwd` en lugar de `/etc/passwd` también se incluirían usuarios de LDAP u otras fuentes.)

### Ejercicio 20. Algoritmo de cifrado de las contraseñas

Se ve en el prefijo del hash en `/etc/shadow`:

| Prefijo | Algoritmo |
| ------- | --------- |
| `$1$` | MD5 (obsoleto) |
| `$5$` | SHA-256 |
| `$6$` | SHA-512 |
| `$y$` | **yescrypt** |

En Ubuntu 24.04 los hashes empiezan por **`$y$`**, así que el algoritmo es **yescrypt**.

**Dónde se configura:**

- En **`/etc/pam.d/common-password`**, en la línea de `pam_unix.so`. Es lo que usan `passwd` y `chpasswd`, que pasan por PAM:

  ```text
  password  [success=1 default=ignore]  pam_unix.so obscure yescrypt
  ```

- En **`/etc/login.defs`**, la variable `ENCRYPT_METHOD`, que usan las herramientas que cifran sin pasar por PAM (por ejemplo, `newusers`). Ojo: en Ubuntu 24.04 puede seguir poniendo `SHA512` aunque las contraseñas reales salgan en yescrypt, porque manda lo que dice PAM.

---

## Pregunta final

**Ventajas de que las cuentas caduquen automáticamente al final de curso (300 alumnos):**

- **Seguridad:** no quedan cientos de cuentas activas de alumnos que ya no están en el centro, que son una puerta de entrada si alguien conoce o adivina su contraseña.
- **Menos trabajo y menos errores:** el administrador no tiene que acordarse de desactivar 300 cuentas a mano; se configura una vez (al crearlas, con `useradd -e` o `chage -E`).
- **Control:** la cuenta queda desactivada pero **no se borra**, así que se pueden revisar o archivar sus datos y, si el alumno continúa al curso siguiente, basta con ampliar la fecha (`chage -E`).
- Ayuda a cumplir la **protección de datos**: el acceso a la información dura solo lo que dura la relación con el centro.

**Diferencia entre caducar la contraseña y caducar la cuenta:**

- **Caducar la contraseña** (`chage -M`, campo 5 de `/etc/shadow`): pasado el plazo, el usuario **puede seguir entrando**, pero el sistema le obliga a **cambiar la contraseña**. Sirve para que las contraseñas se renueven.
- **Caducar la cuenta** (`chage -E`, campo 8 de `/etc/shadow`): a partir de esa fecha la cuenta queda **desactivada** y el usuario **no puede entrar**, aunque sepa la contraseña. Solo el administrador puede reactivarla.
