<p style="font-size: 1.3em;"><strong>CFGS Administración de Sistemas Informáticos y en Red (1º curso)</strong></p>

Material elaborado para el módulo **Implantación de Sistemas Operativos**

# UT1.1. El sistema informático: hardware — SOLUCIÓN

## Índice

1. [Hardware, software y componente humano](#ejercicio-1-hardware-software-y-componente-humano)
2. [Las cuatro fases del ciclo de instrucción](#ejercicio-2-las-cuatro-fases-del-ciclo-de-instrucción)
3. [¿Qué es una interrupción?](#ejercicio-3-qué-es-una-interrupción)
4. [Clasificación de dispositivos](#ejercicio-4-clasificación-de-dispositivos)

---

## Ejercicio 1. Hardware, software y componente humano

- **Hardware**: es la parte física del sistema informático, todo lo que se puede tocar: el procesador, la memoria, el disco duro, el teclado, la pantalla, los cables, etc.

- **Software**: es la parte lógica, el conjunto de programas e instrucciones que le dicen al hardware qué hacer. Incluye el sistema operativo (como Windows o Android) y las aplicaciones (como un navegador o un procesador de texto).

- **Componente humano** (también llamado *humanware* o *elemento humano*): son las personas que interactúan con el sistema, ya sea usándolo (usuarios), diseñándolo (programadores, ingenieros) o manteniéndolo (técnicos, administradores). Sin personas, el hardware y el software no tendrían sentido ni propósito.

En resumen: el hardware es el "cuerpo", el software es la "mente" que lo dirige y el componente humano es quien crea, usa y da sentido a todo el sistema.

## Ejercicio 2. Las cuatro fases del ciclo de instrucción

1. **Búsqueda (*fetch*)**: el procesador va a la memoria y obtiene (lee) la siguiente instrucción que debe ejecutar, según indica el contador de programa.

2. **Decodificación (*decode*)**: la unidad de control interpreta la instrucción obtenida, determinando qué operación hay que realizar y con qué datos.

3. **Ejecución (*execute*)**: la CPU realiza realmente la operación indicada (por ejemplo, una suma, una comparación o mover datos), normalmente usando la ALU (unidad aritmético-lógica).

4. **Almacenamiento o escritura (*store / write-back*)**: el resultado de la operación se guarda, ya sea en un registro o en memoria.

Este ciclo se repite constantemente mientras el ordenador está funcionando.

## Ejercicio 3. ¿Qué es una interrupción?

Una **interrupción** es una señal que se envía al procesador para avisarle de que ha ocurrido un evento que necesita atención inmediata (por ejemplo, se ha pulsado una tecla, ha llegado un dato por la red o una impresora ha terminado de imprimir).

Cuando el procesador recibe una interrupción, **pausa temporalmente** la tarea que está ejecutando, atiende el evento (ejecutando una rutina específica llamada *rutina de servicio de interrupción*) y luego retoma la tarea original donde la había dejado.

Es fundamental porque, sin interrupciones, el procesador tendría que estar constantemente **preguntando** a cada dispositivo ("¿ha pasado algo?") para saber si necesita atención, lo cual sería muy ineficiente. Gracias a las interrupciones, el ordenador puede dedicarse a otras tareas y solo detenerse cuando realmente es necesario, respondiendo de forma rápida y eficiente a los eventos externos.

## Ejercicio 4. Clasificación de dispositivos

| Dispositivo | Tipo de ordenador |
| ----------- | ----------------- |
| Cajero automático | **Ordenador empotrado o embebido** (*embedded*): es un sistema especializado, dedicado a una tarea concreta dentro de una máquina mayor. |
| Ordenador de un centro de investigación meteorológica | **Superordenador**: se usa para cálculos masivos y complejos, como las simulaciones climáticas, que requieren una enorme capacidad de procesamiento. |
| Tu propio smartphone | **Dispositivo móvil**: dispositivo personal, portátil, de uso individual. |
| Servidor de correo de una empresa | **Servidor**: ordenador diseñado para dar servicio a muchos usuarios o clientes a la vez, gestionando y distribuyendo recursos (en este caso, el correo electrónico) de forma centralizada. |
