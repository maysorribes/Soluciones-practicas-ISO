<p style="font-size: 1.3em;"><strong>CFGS Administración de Sistemas Informáticos y en Red (1º curso)</strong></p>

Material elaborado para el módulo **Implantación de Sistemas Operativos**

# UT1.4. Estructura del SO: E/S y archivos — SOLUCIÓN

## Índice

1. [E/S programada y E/S por interrupciones](#ejercicio-1-es-programada-y-es-por-interrupciones)
2. [El buffer de impresión](#ejercicio-2-el-buffer-de-impresión)
3. [Los caracteres comodín](#ejercicio-3-los-caracteres-comodín)
4. [FAT32 y NTFS](#ejercicio-4-fat32-y-ntfs)
5. [Qué es un inodo](#ejercicio-5-qué-es-un-inodo)
6. [El journaling](#ejercicio-6-el-journaling)
7. [Mapa conceptual de la UT1](#ejercicio-7-mapa-conceptual-de-la-ut1)
8. [Partición de prueba con NTFS y ext4](#ejercicio-8-partición-de-prueba-con-ntfs-y-ext4)

---

## Ejercicio 1. E/S programada y E/S por interrupciones

- **E/S programada**: el procesador **espera activamente** al dispositivo. Le pregunta una y otra vez si ya está listo y, mientras tanto, **no hace nada más**.
- **E/S por interrupciones**: el procesador **sigue con otras tareas** y es el dispositivo quien le **avisa con una interrupción** cuando está listo.

**Por qué es más eficiente la segunda:** los dispositivos son muchísimo más lentos que el procesador. Con la E/S programada, el procesador pierde una enorme cantidad de tiempo esperando. Con las interrupciones, ese tiempo lo dedica a ejecutar otros procesos y solo atiende al dispositivo cuando realmente hace falta.

## Ejercicio 2. El buffer de impresión

Un **buffer** es una **zona de memoria intermedia** donde se guardan temporalmente los datos que van hacia un dispositivo (o vienen de él).

**Qué problema resuelve al imprimir:** la impresora es **mucho más lenta** que el ordenador. Sin buffer, el programa tendría que esperar a que la impresora procesara cada parte del documento, y el usuario no podría seguir trabajando hasta que terminara. Con el buffer, el documento se envía rápidamente a esa memoria intermedia y el programa **queda libre enseguida**. Después, los datos se van mandando a la impresora **al ritmo que esta puede procesarlos**, sin bloquear el resto del sistema.

## Ejercicio 3. Los caracteres comodín

- `*` sustituye a **cualquier secuencia de caracteres** (incluso a ninguno).
- `?` sustituye a **un único carácter** cualquiera.

Ejemplo, en una carpeta con los ficheros `tema1.pdf`, `tema2.pdf`, `tema10.pdf`, `notas.txt` y `tema1.odt`:

| Orden | Resultado | Explicación |
| ----- | --------- | ----------- |
| `ls *.pdf` (o `dir *.pdf` en Windows) | `tema1.pdf`, `tema2.pdf`, `tema10.pdf` | Todos los que acaban en `.pdf` |
| `ls tema1.*` | `tema1.pdf`, `tema1.odt` | Se llaman `tema1`, con cualquier extensión |
| `ls tema?.pdf` | `tema1.pdf`, `tema2.pdf` | `tema` + **un** carácter + `.pdf`. `tema10.pdf` no aparece porque tiene dos caracteres |
| `ls tema*.pdf` | `tema1.pdf`, `tema2.pdf`, `tema10.pdf` | `tema` + cualquier cosa + `.pdf` |

## Ejercicio 4. FAT32 y NTFS

| | FAT32 | NTFS |
| - | ----- | ---- |
| Tamaño máximo de archivo | **4 GB** | Sin límite práctico |
| Permisos de usuario | **No** | **Sí**, detallados |
| *Journaling* (recuperación ante fallos) | **No** | **Sí** |
| Cifrado y compresión | **No** | **Sí** |
| Compatibilidad | **Máxima**: Windows, Linux, macOS, televisores, consolas, coches, cámaras… | Nativo en Windows; Linux lo lee y escribe, macOS solo lo lee |

**Cuándo seguiría usando FAT32 hoy:** en una **memoria USB o tarjeta SD pequeña** que tenga que funcionar en **cualquier dispositivo** (un televisor, una radio de coche, una impresora, una cámara…), siempre que no haga falta guardar archivos de más de 4 GB. También se usa siempre en la **partición del sistema EFI**, que debe estar en FAT32 para que la lea el firmware UEFI.

## Ejercicio 5. Qué es un inodo

Un **inodo** es la estructura de datos que, en los sistemas de archivos de Linux (como ext4), **guarda toda la información de un archivo**. Cada archivo tiene **un inodo**, identificado por un **número**.

| Lo que **sí** contiene | Lo que **no** contiene |
| ---------------------- | ---------------------- |
| Tipo de archivo (normal, directorio, enlace…) | El **nombre** del archivo |
| Permisos | El contenido en sí (los datos están en los bloques a los que apunta) |
| Propietario y grupo | |
| Tamaño | |
| Fechas (último acceso, última modificación, último cambio) | |
| Número de enlaces | |
| **Dónde están los bloques de datos** del archivo en el disco | |

El **nombre** se guarda en el **directorio** que contiene el archivo, que es en realidad una lista de pares *nombre → número de inodo*. Por eso un mismo inodo puede tener varios nombres (enlaces duros). El número de inodo de un archivo se ve con `ls -i`.

## Ejercicio 6. El journaling

Un sistema de archivos con **journaling** apunta en un **diario** (*journal*) cada operación **antes de hacerla**.

- **Sin journaling:** si se va la luz a mitad de una operación (por ejemplo, mientras se guarda un archivo), el sistema de archivos puede quedar **inconsistente**. Al reiniciar, hay que **revisar el disco entero** para buscar y corregir errores, lo que puede tardar mucho, y aun así pueden perderse datos.
- **Con journaling:** al reiniciar, el sistema **consulta el diario** y sabe exactamente qué operaciones se completaron y cuáles quedaron a medias. Así puede terminarlas o deshacerlas. La recuperación es **mucho más rápida y fiable**, y el sistema de archivos queda en un estado coherente.

Son sistemas de archivos con *journaling* ext3, ext4 y NTFS. FAT32 y exFAT no lo tienen.

## Ejercicio 7. Mapa conceptual de la UT1

Respuesta abierta: cada alumno puede hacerlo con la herramienta que prefiera. Lo importante es que aparezcan los cuatro bloques y, sobre todo, **cómo se relacionan**. Un ejemplo de esquema válido:

- **Sistema informático** = hardware + software + componente humano
    - **Hardware (UT1.1)**: procesador, memoria, unidades de E/S, buses y placa base
        - El **procesador** ejecuta las instrucciones (ciclo de instrucción) y atiende las **interrupciones**.
        - La **memoria** guarda los programas en ejecución.
        - Las **unidades de E/S** y los **discos** comunican con el exterior y guardan la información de forma permanente.
    - **Software (UT1.2)**: software de sistema y de aplicación
        - El **sistema operativo** es el software de sistema principal: se sitúa **entre el hardware y las aplicaciones** y gestiona los recursos.
        - Las **aplicaciones** usan los servicios del SO (a través de su API) para acceder al hardware.
        - Se instala, se configura y se usa mediante interfaces gráficas o de texto.
    - **Funciones del SO: procesos y memoria (UT1.3)**
        - Gestiona el **procesador**: crea los **procesos**, los planifica y los intercala (multitarea).
        - Gestiona la **memoria**: la reparte entre los procesos (particiones, paginación, memoria virtual).
    - **Funciones del SO: E/S y archivos (UT1.4)**
        - Gestiona los **dispositivos de E/S** mediante drivers, interrupciones, DMA y buffers.
        - Gestiona los **archivos** en los discos mediante **sistemas de archivos** (FAT32, NTFS, ext4…).

**Conexiones clave que deben aparecer:**

- El **SO (UT1.2)** es quien gestiona los **recursos del hardware (UT1.1)**. Esa gestión es lo que se estudia en la UT1.3 y la UT1.4.
- Las **interrupciones** de la UT1.1 son la base de la **multitarea** (UT1.3) y de la **E/S por interrupciones** (UT1.4).
- La **memoria** de la UT1.1 es el recurso que reparte la **gestión de memoria** (UT1.3). El **disco** es el que organizan los **sistemas de archivos** (UT1.4), y también el que se usa para la **memoria virtual**.

## Ejercicio 8. Partición de prueba con NTFS y ext4

Respuesta abierta. Una buena entrega incluye estos pasos, cada uno con su captura:

1. **Añadir un disco** a la máquina virtual en VirtualBox (por ejemplo, de 2 GB) y comprobar que el sistema lo detecta.
2. **Crear dos particiones** de prueba (o una partición que se formatea dos veces) con el *Administrador de discos* de Windows, con *Discos* o *GParted* en Linux, o con la terminal.
3. **Formatear** una en **NTFS** y otra en **ext4**. En Linux, por ejemplo: `sudo mkfs.ntfs -f /dev/sdb1` y `sudo mkfs.ext4 /dev/sdb2`.
4. **Montarlas** y crear algunos archivos en cada una.

**Diferencias que el alumno debería observar:**

- **Desde Windows**, la partición NTFS se ve y se usa con normalidad, con su letra de unidad. La partición **ext4 no se reconoce**: aparece sin letra y Windows ofrece formatearla.
- **Desde Linux** se pueden usar las dos. En la partición **ext4**, los archivos tienen **propietario y permisos de Linux** (`ls -l`), y aparece la carpeta `lost+found`. En la **NTFS**, los permisos que muestra `ls -l` son "simulados": todos los archivos aparecen con el mismo propietario y los mismos permisos, porque Linux no traduce los permisos de Windows.
- **Espacio libre:** recién formateadas, las dos muestran algo menos de espacio que el tamaño de la partición, porque cada sistema de archivos reserva espacio para sus propias estructuras (inodos y *journal* en ext4, la tabla MFT en NTFS).
