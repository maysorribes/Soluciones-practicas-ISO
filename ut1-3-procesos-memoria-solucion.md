<p style="font-size: 1.3em;"><strong>CFGS Administración de Sistemas Informáticos y en Red (1º curso)</strong></p>

Material elaborado para el módulo **Implantación de Sistemas Operativos**

# UT1.3. Estructura del SO: procesos y memoria — SOLUCIÓN

## Índice

1. [Estructura monolítica y micronúcleo](#ejercicio-1-estructura-monolítica-y-micronúcleo)
2. [Programa y proceso](#ejercicio-2-programa-y-proceso)
3. [Multitarea con un único núcleo](#ejercicio-3-multitarea-con-un-único-núcleo)
4. [FCFS y Round Robin](#ejercicio-4-fcfs-y-round-robin)
5. [Particiones fijas, variables y paginación](#ejercicio-5-particiones-fijas-variables-y-paginación)
6. [Memoria virtual: ventaja e inconveniente](#ejercicio-6-memoria-virtual-ventaja-e-inconveniente)
7. [Observar los procesos (práctica en el aula)](#ejercicio-7-observar-los-procesos-práctica-en-el-aula)

---

## Ejercicio 1. Estructura monolítica y micronúcleo

- **Monolítica:** todo el sistema operativo (procesos, memoria, dispositivos, sistema de archivos) se ejecuta como un único bloque de código en el espacio del núcleo, con comunicación directa entre sus partes. Es rápida, pero un fallo en cualquier componente puede tumbar todo el sistema.

- **Micronúcleo:** el núcleo solo incluye las funciones mínimas imprescindibles (comunicación entre procesos y gestión básica). El resto de servicios se ejecutan como procesos independientes en el espacio de usuario y se comunican mediante paso de mensajes.

**¿Cuál prioriza más la robustez?** El **micronúcleo**. Al mantener los servicios aislados unos de otros, un fallo en un componente (por ejemplo, un *driver*) no afecta al resto del sistema, que puede seguir funcionando o reiniciar ese proceso concreto. En cambio, en una estructura monolítica todos los componentes comparten el mismo espacio de memoria, así que un fallo puede propagarse y colapsar el sistema entero.

## Ejercicio 2. Programa y proceso

Un **programa** es un conjunto de instrucciones almacenadas en un archivo del disco (por ejemplo, el ejecutable de un navegador). Es algo **estático**: no hace nada por sí solo, simplemente existe como código guardado.

Un **proceso** es ese programa **en ejecución**: cuando el sistema operativo carga el programa en memoria y le asigna recursos (CPU, memoria, identificador de proceso, estado…), se convierte en un proceso activo, con su propio contexto (variables, pila, contador de programa, etc.).

Una analogía sencilla: el programa sería la receta de una tarta escrita en un papel y el proceso, el momento en que alguien está cocinando esa tarta siguiendo la receta, con ingredientes y utensilios reales. Además, un mismo programa puede dar lugar a **varios procesos** simultáneos (por ejemplo, tener el navegador abierto en varias ventanas independientes).

## Ejercicio 3. Multitarea con un único núcleo

Aunque parezca que varios programas se ejecutan "a la vez", en un procesador con un solo núcleo **solo se ejecuta un proceso en cada instante**. Lo que hace el sistema operativo es aplicar una técnica llamada **tiempo compartido** (*time-sharing*):

1. El **planificador de procesos** (*scheduler*) reparte el tiempo de CPU en intervalos muy cortos (unos pocos milisegundos), llamados **quantum** o rodajas de tiempo.

2. La CPU ejecuta un proceso durante ese breve intervalo y luego el sistema operativo realiza un **cambio de contexto**: guarda el estado del proceso actual (registros, contador de programa, etc.) y carga el estado del siguiente proceso.

3. Este cambio ocurre tan rápido y tantas veces por segundo que, para el usuario, da la sensación de que todos los programas se ejecutan simultáneamente, aunque en realidad se van turnando.

## Ejercicio 4. FCFS y Round Robin

- **FCFS (*First Come, First Served*):** los procesos se ejecutan estrictamente en el orden en que llegan, sin interrupción hasta que terminan. Es sencillo, pero si un proceso tarda mucho, todos los que están detrás tienen que esperar (efecto "convoy").

- **Round Robin:** a cada proceso se le asigna un pequeño intervalo de tiempo fijo (*quantum*). Si no termina en ese tiempo, se pausa y pasa al final de la cola, dando paso al siguiente proceso.

**Para un sistema interactivo es mucho más adecuado Round Robin**, porque garantiza que ningún proceso acapare la CPU durante mucho tiempo seguido: todos los procesos, incluido el que atiende al usuario, reciben CPU con frecuencia, lo que se traduce en una respuesta rápida y fluida. Con FCFS, si justo antes ha llegado un proceso largo, el usuario podría quedarse esperando bastante tiempo sin ver ninguna respuesta, algo inaceptable en un entorno interactivo como un sistema de escritorio.

## Ejercicio 5. Particiones fijas, variables y paginación

- **Particiones fijas:** la memoria se divide de antemano en un número fijo de bloques de tamaño predeterminado (iguales o distintos), y cada proceso ocupa una partición completa. Problemas: **fragmentación interna** (si un proceso es más pequeño que la partición, se desperdicia el espacio sobrante) y un número limitado de procesos que caben a la vez.

- **Particiones variables:** las particiones se crean dinámicamente con el tamaño exacto que necesita cada proceso. Esto evita la fragmentación interna, pero genera **fragmentación externa**: con el tiempo quedan huecos pequeños y dispersos entre los procesos que, aunque sumen espacio suficiente, no son contiguos y no se pueden aprovechar para un proceso nuevo.

**La paginación** resuelve ambos problemas. Divide cada proceso en bloques pequeños de **tamaño fijo** llamados **páginas**, y la memoria física en bloques del mismo tamaño llamados **marcos**. Las páginas de un proceso no necesitan estar contiguas en la memoria física: el sistema operativo lleva una **tabla de páginas** que indica en qué marco está cada una. Así se elimina la fragmentación externa y, al ser bloques pequeños, la fragmentación interna se reduce al mínimo (como mucho se desperdicia parte de la última página).

## Ejercicio 6. Memoria virtual: ventaja e inconveniente

**Qué es:** una técnica que permite a cada proceso trabajar como si dispusiera de un espacio de memoria propio y mucho mayor que la RAM real. El sistema operativo combina la RAM física con espacio en disco (archivo de paginación o *swap*) para simular esa capacidad extra, traduciendo las direcciones virtuales a físicas de forma transparente para el programa.

**Ventaja principal:** permite ejecutar más procesos, o procesos más grandes, de los que cabrían físicamente en la RAM disponible. Además, aísla la memoria de cada proceso del resto, lo que mejora la seguridad y la estabilidad del sistema.

**Inconveniente de usarla en exceso:** si la RAM física es insuficiente y el sistema tiene que recurrir constantemente al disco para intercambiar páginas (fenómeno llamado **hiperpaginación** o *thrashing*), el rendimiento se desploma, porque el acceso al disco (incluso a un SSD) es mucho más lento que el acceso a la RAM. El sistema puede llegar a pasar más tiempo moviendo datos entre la RAM y el disco que ejecutando las tareas del usuario.

## Ejercicio 7. Observar los procesos (práctica en el aula)

Respuesta abierta: cada alumno lo hace en su propio equipo.

- **En Windows:** se abre el Administrador de tareas (`Ctrl + Mayús + Esc`) y se consulta la pestaña *Procesos* o *Detalles*.
- **En Linux:** se abre una terminal y se ejecuta `top` o, si está instalado, `htop` (más visual).

La entrega debe incluir una captura y una tabla con el **nombre**, el **% de CPU** y la **memoria** de cada proceso elegido:

- **Procesos de sistema típicos:** `System`, `svchost.exe`, `wininit.exe` (Windows); `systemd`, `kthreadd`, `kworker` (Linux).
- **Procesos de aplicación típicos:** el navegador (`chrome.exe`, `firefox`), un editor de texto, un reproductor multimedia, etc.
