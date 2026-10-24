<p style="font-size: 1.3em;"><strong>CFGS Administración de Sistemas Informáticos y en Red (1º curso)</strong></p>

Material elaborado para el módulo **Implantación de Sistemas Operativos**

# UT1.2. Componentes software: el sistema operativo — SOLUCIÓN

## Índice

1. [Software de sistema y software de aplicación](#ejercicio-1-software-de-sistema-y-software-de-aplicación)
2. [Licencia permisiva y copyleft](#ejercicio-2-licencia-permisiva-y-copyleft)
3. [Licencia OEM y Retail/FPP](#ejercicio-3-licencia-oem-y-retailfpp)
4. [Windows y Ubuntu](#ejercicio-4-windows-y-ubuntu)
5. [Parámetros básicos en la instalación de un SO](#ejercicio-5-parámetros-básicos-en-la-instalación-de-un-so)
6. [CMD y PowerShell](#ejercicio-6-cmd-y-powershell)
7. [Gestor de arranque: Windows y GRUB](#ejercicio-7-gestor-de-arranque-windows-y-grub)
8. [Instalación documentada (práctica en el aula)](#ejercicio-8-instalación-documentada-práctica-en-el-aula)

---

## Ejercicio 1. Software de sistema y software de aplicación

**Software de sistema:** es el conjunto de programas que gestionan el hardware y ofrecen una base sobre la que funcionan el resto de programas. El usuario no lo usa directamente para tareas productivas, sino que permite que el equipo funcione.

- Ejemplos: el propio sistema operativo (Windows, Linux) o los controladores (*drivers*) de los dispositivos, como el de la tarjeta gráfica.

**Software de aplicación:** son los programas que el usuario utiliza para realizar tareas concretas (escribir, navegar, editar imágenes, etc.).

- Ejemplos: un procesador de textos (Microsoft Word, LibreOffice Writer) o un navegador web (Firefox, Chrome).

## Ejercicio 2. Licencia permisiva y copyleft

- **Permisiva:** permite usar, modificar y redistribuir el software casi sin restricciones. Incluso se puede incorporar a software privativo y cerrar el código de las versiones modificadas.
    - Ejemplo: licencia **MIT** o **BSD**.

- **Copyleft:** también permite modificar y redistribuir, pero obliga a que cualquier versión derivada se distribuya bajo la misma licencia, es decir, que siga siendo libre y con el código abierto.
    - Ejemplo: licencia **GPL** (*General Public License*), que usa el núcleo Linux.

## Ejercicio 3. Licencia OEM y Retail/FPP

- **OEM (*Original Equipment Manufacturer*):** está vinculada al equipo en el que se instala por primera vez. Suele ser más barata, pero no se puede transferir a otro ordenador, aunque el original se estropee o se sustituya. La suelen vender los fabricantes junto con el equipo.

- **Retail/FPP (*Full Packaged Product*):** es más cara, pero más flexible: se puede desinstalar de un equipo e instalar en otro (es transferible), y suele incluir más soporte del fabricante.

**Para una empresa que compra 100 ordenadores nuevos**, lo más adecuado normalmente es una licencia **OEM** (o, mejor aún, un contrato de **licencias por volumen**), ya que resulta mucho más económica cuando se compran tantos equipos nuevos y no se necesita la posibilidad de trasladarla que ofrece la Retail.

## Ejercicio 4. Windows y Ubuntu

| | Ventajas | Inconvenientes |
| - | -------- | -------------- |
| **Windows** | Mayor compatibilidad con el software comercial y los videojuegos. Interfaz muy extendida y familiar para la mayoría de usuarios. | Licencia de pago. Más expuesto a virus y malware por su gran cuota de mercado. |
| **Ubuntu (Linux)** | Gratuito y de código abierto. Mayor seguridad y estabilidad, especialmente en servidores. | Menor compatibilidad con software comercial específico (algunos programas no tienen versión para Linux). Curva de aprendizaje más pronunciada para usuarios no técnicos. |

## Ejercicio 5. Parámetros básicos en la instalación de un SO

Un orden típico sería:

1. Selección del idioma, la zona horaria y la distribución del teclado.
2. Tipo de instalación (limpia, actualización, arranque dual).
3. Particionado del disco duro.
4. Selección de componentes o paquetes que se van a instalar.
5. Creación del usuario y su contraseña (cuenta de administrador).
6. Configuración de red.
7. Configuración del gestor de arranque.
8. Instalación de controladores y actualizaciones finales.

## Ejercicio 6. CMD y PowerShell

- **CMD (símbolo del sistema):** es el intérprete de comandos tradicional de Windows, heredado de MS-DOS. Trabaja con **texto plano**: cada comando devuelve una cadena de caracteres que hay que interpretar o procesar a mano si se quiere reutilizar.

- **PowerShell:** es un intérprete más moderno y potente, orientado a la administración avanzada y a la automatización. Se dice que trabaja con **objetos** porque cada comando (*cmdlet*) no devuelve simple texto, sino objetos con propiedades y métodos. Esto permite, por ejemplo, filtrar, ordenar o manipular directamente los datos de salida de un comando (como una lista de procesos) sin tener que tratar el texto, lo que lo hace mucho más flexible para scripts complejos.

## Ejercicio 7. Gestor de arranque: Windows y GRUB

Un **gestor de arranque** es el programa que se ejecuta justo al encender el equipo y se encarga de cargar el sistema operativo, permitiendo elegir entre varios si hay más de uno instalado (arranque dual o múltiple).

- En **Windows**, esta función la realiza **BOOTMGR**, apoyado en el almacén **BCD**, que se gestiona con `bcdedit`.

- En **Linux**, el gestor más habitual es **GRUB** (*GRand Unified Bootloader*), más flexible y de código abierto. Puede detectar automáticamente los sistemas operativos instalados (incluido Windows) y mostrar un menú de selección que el usuario puede modificar mediante archivos de configuración de texto plano.

La diferencia clave es que GRUB puede arrancar sistemas muy distintos y se configura a mano con facilidad, mientras que BOOTMGR está pensado principalmente para Windows y se gestiona con herramientas propias de Microsoft.

## Ejercicio 8. Instalación documentada (práctica en el aula)

Respuesta abierta: cada alumno documenta su propia instalación en una máquina virtual (VirtualBox, VMware…), con una captura de cada pantalla del proceso. Siguiendo el esquema del punto 8 de los apuntes, la documentación debe recoger como mínimo:

- **Identificador** del equipo (nombre de la máquina virtual y del equipo).
- **Características del hardware** asignado: CPU, RAM, disco y tipo de red.
- **Sistema operativo** instalado: nombre y versión exactos, fecha de instalación y clave de licencia si procede.
- **Parámetros** configurados durante la instalación (idioma, zona horaria, particionado, usuario…). La contraseña no se escribe.
- **Software adicional** instalado.
- **Configuración de red** aplicada.
- **Incidencias** surgidas y cómo se resolvieron.
