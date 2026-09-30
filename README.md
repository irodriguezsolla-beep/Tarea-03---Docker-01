# Tarea 03 - Docker 01

## Paso1
Para descargar la imagen de Alpine sin arrancarla ejecuto el comando `docker pull alpine:3.20`, este comando me descargará la imagen de alpine sin arrancarla y para comprobar que la tengo lanzo el comando `docker images`.
![](fotos/Captura%20desde%202026-09-30%2008-25-48.png)

## Paso2
Para crear un contenedor a partir de la imagen de Alpine sin ponerlo en marcha ejecuto el comando `docker create alpine:3.20`. Este comando me creará el contenedor en el sistema sin arrancarlo, y para comprobar su estado y el nombre que le ha asignado lanzo el comando docker `ps -a`.

Al revisar el listado veo que el contenedor queda en estado Created y Docker le ha puesto un nombre aleatorio automáticamente que es vigilant_wing, ya que no he utilizado la opción --name para especificar uno.
![](fotos/Captura%20desde%202026-09-30%2008-33-41.png)

## Paso3
Para crear y arrancar el contenedor dam_alp1 ejecutando una shell e interactuar con él ejecuto el comando `docker run -it --name dam_alp1 alpine:3.20 /bin/sh`.

Las opciones que necesito para poder escribir dentro son -i e -t: la opción -i mantiene abierta la entrada estándar para poder introducir comandos, y la opción -t le asigna una terminal interactiva (TTY) para poder ver la consola y trabajar dentro de ella.
![](fotos/Captura%20desde%202026-09-30%2008-35-28.png)

## Paso4
Para ver la IP asignada y comprobar si hay salida a Internet ejecuto dentro de la terminal del contenedor los comandos ip a y ping -c 3 google.com.

Al revisar la salida del comando ip a en la interfaz eth0 veo que tiene asignada una IP de la red por defecto 172.17.0.2, y al lanzar ping -c 3 google.com compruebo que sí responde correctamente, lo que confirma que el contenedor tiene conexión a Internet y resolución DNS hacia el exterior a través de la red del servidor.
![](fotos/Captura%20desde%202026-09-30%2008-37-04.png)

## Paso 5

Para dejar dam_alp1 en marcha presiono la combinación de teclas Ctrl+P y luego Ctrl+Q, lo que me permite salir de su terminal sin pararlo. Luego, creo y arranco el segundo contenedor ejecutando `docker run -it --name dam_alp2 alpine:3.20 /bin/sh`.

Conecxión a dam_alp2
![](fotos/Captura%20desde%202026-09-30%2008-40-30.png)
![](fotos/Captura%20desde%202026-09-30%2008-42-21.png)
* Ping por IP `ping -c 3 172.17.0.3`: FUNCIONA.
  Responde correctamente porque ambos contenedores están conectados a la misma red por defecto (bridge) y la interfaz puente del servidor enruta el tráfico entre sus direcciones IP
* Ping por nombre `ping -c 3 dam_alp2`: FALLA
  Da un error de resolución de nombre (bad address) porque la red bridge por defecto de Docker no tiene activo un servidor DNS interno, por lo que los contenedores no pueden traducirse los nombres entre sí.

## Paso 6
Para averiguar el consumo de memoria de los contenedores en tiempo real ejecuto desde la terminal del servidor el comando docker stats.

Al revisar la salida en la columna MEM USAGE, veo que cada contenedor de Alpine consume una cantidad muy reducida de memoria RAM, me estan consumiendo 769Kib el dam_alp1 y 532Kib dam_alp2.
![](fotos/Captura%20desde%202026-09-30%2008-43-15.png)

## Paso 7
Para salir de la terminal del contenedor escribo el comando exit.

Al hacerlo, el contenedor se ha parado. Esto pasa porque en Docker la vida de un contenedor depende de su proceso principal, al cerrar la shell /bin/sh con el comando exit, el proceso principal termina y el contenedor se detiene inmediatamente.

Al repetir el comando docker stats, los contenedores ya no aparecen en el listado. Esto ocurre porque docker stats solo muestra el consumo de memoria y CPU de los contenedores que están en ejecución; al estar parados ya no consumen recursos del sistema y el comando los ignora.

![](fotos/Captura%20desde%202026-09-30%2008-47-55.png)
![](fotos/Captura%20desde%202026-09-30%2008-48-03.png)

## Paso 8

Para ver el espacio en disco utilizado ejecuto desde la terminal del servidor el comando docker system df.
![](fotos/Captura%20desde%202026-09-30%2008-50-49.png)
Al revisar la tabla que muestra este comando, distingo el espacio de la siguiente forma:
* En el tipo Images: Muestra en la columna SIZE el espacio total que ocupan las imágenes descargadas  y cuántas de ellas están en uso. En mi caso tengo 3 imágenes y me ocupan 25.08MB.
* En el tipo Containers: Muestra en la columna SIZE el espacio total acumulado por las capas de escritura de todos los contenedores.En mi caso tengo 5 contenedores y me ocupan 36.86KB.





