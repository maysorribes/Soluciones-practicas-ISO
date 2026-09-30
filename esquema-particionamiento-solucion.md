<p style="font-size: 1.3em;"><strong>CFGS Administración de Sistemas Informáticos y en Red (1º curso)</strong></p>

Material elaborado para el módulo **Implantación de Sistemas Operativos**

# Esquema de particionamiento — SOLUCIÓN

## Índice

1. [Sistemas de archivos](#ejercicio-1-sistemas-de-archivos)
2. [GPT y MBR](#ejercicio-2-gpt-y-mbr)
3. [Identificar las particiones del disco GPT](#ejercicio-3-identificar-las-particiones-del-disco-gpt)
4. [Ejemplo con MBR](#ejercicio-4-ejemplo-con-mbr)
5. [Comprueba tu máquina](#ejercicio-5-comprueba-tu-máquina)

---

## Ejercicio 1. Sistemas de archivos

- **¿Qué es un sistema de archivos?** Es la forma en que un sistema operativo organiza y guarda la información dentro de una partición. Define cómo se nombran los archivos, cómo se agrupan en carpetas, dónde se guardan físicamente los datos en el disco y qué información adicional se almacena de cada archivo (tamaño, fechas, permisos, propietario…). Una partición no se puede usar hasta que se le da formato con un sistema de archivos.

- **Tipos más conocidos:** **FAT16, FAT32 y exFAT** (Microsoft, muy compatibles); **NTFS** y **ReFS** (Windows); **ext2, ext3 y ext4**, **Btrfs** y **XFS** (Linux); **HFS+** y **APFS** (Apple); **ISO 9660** y **UDF** (CD, DVD y Blu-ray). También puede citarse **swap**, que no es un sistema de archivos propiamente dicho, sino el formato del área de intercambio de Linux.

### Relación con los sistemas operativos

| Sistema de archivos | Sistema operativo | Uso habitual y observaciones |
| ------------------- | ----------------- | ---------------------------- |
| ext4 | **Linux** (nativo). | Sistema por defecto de muchas distribuciones (Ubuntu, Debian, Mint). Tiene *journaling* y permisos. Windows y macOS no lo leen sin herramientas adicionales. |
| FAT32 | **Todos**: Windows, Linux, macOS, Android, televisores, consolas… | Memorias USB, tarjetas SD pequeñas y la partición del sistema EFI. Límite de **4 GB por archivo**; Windows solo formatea en FAT32 volúmenes de hasta 32 GB. |
| NTFS | **Windows** (nativo). Linux lee y escribe; macOS solo lee. | Partición del sistema de Windows y discos internos. Tiene *journaling*, permisos, cifrado y compresión. Sin límite práctico de tamaño de archivo. |
| APFS | **macOS** e iOS/iPadOS (desde 2017). | Sistema de los Mac actuales, optimizado para SSD, con cifrado e instantáneas. Windows y Linux no lo soportan de forma nativa. |
| exFAT | **Windows, macOS y Linux** (en Linux, nativo desde el kernel 5.4). | Memorias USB, discos externos y tarjetas SDXC. Sin el límite de 4 GB de FAT32, pero sin *journaling* ni permisos. |

## Ejercicio 2. GPT y MBR

### Ventajas de GPT frente a MBR

- **Discos de más de 2 TB**: MBR direcciona sectores con 32 bits, lo que limita el disco a unos 2 TB; GPT usa 64 bits.

- **Más particiones**: MBR admite 4 primarias (o 3 y una extendida con lógicas); GPT admite normalmente 128, todas iguales.

- **Más robustez**: GPT guarda una copia de la tabla al final del disco y usa sumas de verificación (CRC32) para detectar daños.

- **Identificadores únicos (GUID)** para el disco y para cada partición, y nombres de partición.

- Es el esquema de **UEFI**, necesario para usar **Secure Boot** y obligatorio para arrancar Windows 11.

### ¿Por qué no se usa GPT en todos los ordenadores?

- Los **ordenadores antiguos con BIOS clásica** no pueden arrancar Windows desde un disco GPT (Linux sí puede, con una pequeña partición *BIOS boot*).

- Algunos **sistemas operativos antiguos** no lo soportan: Windows XP de 32 bits no lee discos GPT, y ninguna versión de Windows de 32 bits arranca desde ellos.

- **Compatibilidad de dispositivos**: muchas memorias USB, tarjetas SD y discos externos siguen en MBR para que funcionen en televisores, coches, consolas o equipos antiguos.

- **Máquinas virtuales o equipos configurados en modo Legacy**, como la del ejemplo del ejercicio 4.

Aun así, hoy **GPT es el estándar** en todos los ordenadores nuevos, independientemente del tamaño del disco.

## Ejercicio 3. Identificar las particiones del disco GPT

| Partición | Tamaño | Sist. archivos | ¿Qué es / para qué sirve? | Sistema operativo |
| --------- | ------ | -------------- | ------------------------- | ----------------- |
| 1 | 273 MB | FAT32 | **Partición del sistema EFI (ESP)**, etiquetada *SYSTEM*. Contiene los cargadores de arranque de ambos sistemas. Linux la monta en `/boot/efi`. | Compartida (la lee el firmware UEFI) |
| 2 | 17 MB | Desconocido | **Partición reservada de Microsoft (MSR)**. Espacio reservado para uso interno de Windows; no tiene datos ni se ve en Windows. | Windows |
| 3 | 708 GB | NTFS | **Partición de Windows (C:)**, de tipo *Basic data partition*. Sistema operativo, programas y datos de usuario. | Windows |
| 4 | 957 MB | NTFS | **Partición de recuperación** (Windows RE). Herramientas para reparar o restablecer Windows. | Windows |
| 5 | 210 GB | ext4 | **Partición de datos** (*Dades*). Documentos y archivos del usuario, separados del sistema. | Linux |
| 6 | 99 GB | ext4 | **Partición raíz ( / )** de Linux. Sistema operativo y programas. | Linux |
| 7 | 2,0 GB | ext4 | **Partición /boot**. Kernel e imágenes de arranque (initrd) de Linux. | Linux |
| 8 | 4,0 GB | swap | **Área de intercambio (swap)**. Memoria virtual y, si se configura, hibernación. | Linux |
| Final | 533 MB | — | **Espacio sin asignar**. Sobrante sin utilizar. | — |

!!! note "Nota"
    Las particiones 6 y 7 se distinguen por el tamaño (la raíz es mucho mayor), pero para confirmarlo con seguridad habría que ver su punto de montaje seleccionándolas en *Discos* o con el comando `lsblk`.

- **Nombres de dispositivo `/dev/nvme0n1pX`.** El disco es un **SSD NVMe** (conectado por PCI Express), no SATA. Linux nombra estos discos como `nvme0n1` (controladora 0, *namespace* 1) y sus particiones como `p1`, `p2`… Los discos SATA, USB o virtuales de VirtualBox se nombran `sda`, `sdb`… con particiones `sda1`, `sda2`…

- **¿Puede Windows leer la partición Dades?** **No de forma nativa**, porque está en **ext4** y Windows no incluye controlador para ese sistema. Solo sería accesible desde Linux (o, con cierta complejidad, desde WSL 2 en Windows). Para una partición de datos compartida entre los dos sistemas sería mejor **NTFS** (Linux lo lee y escribe sin problemas) o **exFAT** (sencillo y compatible con todos, aunque sin *journaling* ni permisos).

## Ejercicio 4. Ejemplo con MBR

1. **Estilo de partición.** El Disco 0 usa **MBR**. Se ve en *Propiedades → Volúmenes*, donde pone *Estilo de partición: Registro de arranque maestro (MBR)*.

2. **Modo de arranque.** En `msinfo32` el *Modo de BIOS* es **Heredado** (Legacy). Windows solo arranca en modo heredado desde discos MBR y en modo UEFI desde discos GPT, así que ambos datos son coherentes.

3. **Arranque seguro no compatible.** Secure Boot es una función exclusiva de UEFI. En modo heredado no existe, por eso aparece como *No compatible*.

4. **Reservado para el sistema.** Es una partición NTFS de 549 MB que contiene los archivos de arranque de Windows (*bootmgr* y la configuración *BCD*) y el entorno de recuperación. Está marcada como **Activa** porque, en MBR, la BIOS carga el código del sector de arranque y este busca la partición primaria activa para seguir arrancando. Es el equivalente a la partición EFI en los discos GPT.

5. **Particiones del Disco 1.** Son dos particiones primarias (1,86 GB y 27,94 GB) con un sistema de archivos que Windows no reconoce, por eso no tienen letra. Lo más probable es que pertenezcan a un **Linux instalado en ese segundo disco**: la pequeña sería el área de **swap** y la grande la partición raíz en **ext4**, como en la práctica de arranque dual.

## Ejercicio 5. Comprueba tu máquina

Respuesta abierta. Si la máquina virtual se creó con la opción *Habilitar EFI* de VirtualBox, se verá **UEFI** y **GPT**, con particiones EFI, MSR (invisible en Windows), C: y recuperación, como en el ejercicio 3. Si se creó sin EFI, se verá **Heredado** y **MBR**, con la partición *Reservado para el sistema*, como en el ejercicio 4.

Lo importante es relacionar correctamente ambos datos: **UEFI ↔ GPT** y **Heredado ↔ MBR**.
