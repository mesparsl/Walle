# Walle
El wall-e es nuestro proyecto final
https://3dwork.io/wall-e-replica-impresion-3d/

1. Código Arduino (pared-e)
Programa principal para controlar los motores y servos del robot. Las características incluyen:
Una cola de animación, que realiza un seguimiento de los próximos movimientos de servo que el robot debe realizar.
Un generador de movimiento aleatorio, que permite que el robot se mueva de forma autónoma y parezca animado.
Control de velocidad de todos los servomotores, facilitando aceleraciones y desaceleraciones suaves.
Análisis en serie sin bloqueo, que permite el control remoto de los movimientos del robot.
Supervisión del nivel de la batería mediante un circuito divisor de potencial.

2. Servidor web Raspberry Pi (interfaz_web)
La interfaz web está programada en Python y utilizaMatraz para generar un servidor. La Raspberry Pi se conecta a través de USB al microcontrolador Arduino. Las características principales son:
Un joystick JavaScript, con el que se puede controlar fácilmente el movimiento del robot.
Control manual de todos los servomotores.
Una lista de animaciones de movimiento que puede realizar el robot.
Una lista de sonidos que se pueden reproducir.
Página de configuración, donde se pueden modificar los parámetros del motor, el volumen del sonido y las opciones de video.
Soporte para mando de juegos; en cualquier navegador moderno, puede usar un controlador Xbox o PlayStation conectado para controlar el robot.
Una página de inicio de sesión simple para evitar que todos tengan acceso a los controles (nota: este no es un sistema de control de acceso completo, no use esta interfaz web en redes públicas/no confiables)
 Imagen de la interfaz web y el robot.


Instrucciones de configuración
1.Arduino
[a] Instalación básica
Asegúrese de que el cableado de la electrónica coincida con el diagrama que se muestra a continuación.
Descarga/clona la carpeta "wall-e" del repositorio de GitHub.
Abierto wall-e.ino en el IDE de Arduino; Los archivos animaciones.ino, MotorController.hpp y Cola.hpp también debería abrirse automáticamente en pestañas separadas del IDE.
Instala el Adafruit _ PWM Servo Driver.h biblioteca
Vaya a Sketch -> Incluir biblioteca -> Administrar bibliotecas...
BuscarServidor de Adafruit.
Instale la última versión de la biblioteca.
Conéctese a la computadora al microcontrolador con un cable USB. Asegúrese de que la correcta Junta y Puerto se seleccionan en el Herramientas menú.
Cargue el boceto en el microcontrolador.
 Diagrama que muestra el cableado de los componentes electrónicos del robot.

[b] Prueba del programa principal
Una vez que el boceto se haya cargado en el Arduino, encienda la batería de 12 V mientras el microcontrolador aún está conectado a la computadora.
Abre el monitor de serie (botón en la parte superior derecha de Arduino IDE). Establezca la velocidad en baudios en 115200.
Para controlar el movimiento del robot, envía los caracteres 'w', 'a', 's' o 'd' para avanzar, izquierda, atrás o derecha respectivamente. Envía 'q' para detener todo movimiento.
Para mover la cabeza, envía los caracteres 'j', 'l', 'i' o 'k' para inclinar la cabeza hacia la izquierda o hacia la derecha y los ojos hacia arriba o hacia abajo. En esta etapa, los servos pueden intentar moverse más de lo debido y pueden parecer descoordinados. Esto se resolverá realizando los pasos de calibración del servomotor a continuación.

[c] Calibración del servomotor
Descarga/clona la carpeta "wall-e_calibration" del repositorio de GitHub
Abierto wall-e_calibration.ino en el IDE de Arduino.
Cargue el boceto en el microcontrolador, abra el monitor serie y establezca la velocidad de transmisión en 115200.
El esquema se usa para calibrar las longitudes de pulso PWM máximas y mínimas requeridas para mover cada servomotor en su rango de movimiento deseado. Las posiciones estándar BAJA y ALTA de cada uno de los servos se pueden ver en los diagramas en mi sitio web.
Al iniciar el boceto y abrir el monitor serie, debe aparecer un mensaje después de 2-3 segundos, diciendo que está listo para calibrar la posición BAJA del primer servomotor (la rotación del cabezal).
Envía el carácter 'a' y 'd' para mover el motor hacia adelante y hacia atrás en -10 y +10. Para un control más preciso, utilice los caracteres 'z' y 'c' para mover el motor en -1 y +1.
Una vez que el motor esté en la posición correcta, envíe el carácter 'n' para continuar con el paso de calibración. Pasará a la posición ALTA del mismo servo, después de lo cual el proceso se repetirá para cada uno de los 7 servos del robot.
Cuando todas las articulaciones estén calibradas, el boceto generará una matriz que contiene los valores de calibración en el monitor en serie.
Copie la matriz y péguela en lineas 144 al 150 del programawall-e.ino. La matriz debería verse similar a esto:
int preset[][2] = {{410,120}, // rotación de la cabeza
                    {532178}, // parte superior del cuello
                    {120310}, // parte inferior del cuello
                    {465271}, // ojo derecho
                    {278479}, // ojo izquierdo
                    {340135}, // brazo izquierdo
                    {150360}}; // brazo derecho

[d] Detección de nivel de batería (opcional)
Al usar baterías para alimentar el robot, es importante realizar un seguimiento de cuánta energía queda. Algunas baterías pueden romperse si se descargan en exceso, y la tarjeta SD de la Raspberry Pi puede dañarse si no se suministra suficiente energía.
Para usar la función de detección de nivel de batería en el Arduino, conecte las siguientes resistencias y cableado como se muestra en la imagen a continuación. Las resistencias (divisor de potencial) reducen el voltaje de 12 V a un valor inferior a 5 V, lo que es seguro para que Arduino lo mida usando sus pines analógicos. Los valores de resistencia recomendados sonR1 = 100 kΩ yR2 = 47kΩ.
Descomentar línea 54 #definir BAT_L en el boceto principal de Arduinowall-e.ino.
Si está utilizando diferentes valores de resistencia, cambie el valor del factor de ganancia del divisor potencial en la línea 54 del esquema, de acuerdo con la fórmula:POT_DIV = R2 / (R1 + R2).
El programa ahora debería verificar automáticamente el nivel de la batería cada 10 segundos, y este nivel se mostrará en la interfaz web de Raspberry Pi en la sección "Estado".

Diagrama que muestra el cableado del circuito de detección de nivel de batería

[e] Pantalla LED (Opcional) (Aportado por:escarabajo)
Es posible integrar una pequeña pantalla oLED que mostrará el nivel de batería del robot en el panel indicador de batería frontal. Esta característica requiere que el circuito de detección de nivel de batería en la sección anterior esté habilitado, y la pantalla se actualizará cada vez que se calcule el nivel de batería. Esta función utiliza la biblioteca de visualización u8g2 en modo página; en el Arduino UNO puede recibir una advertencia de que el uso de la memoria es alto, pero esta advertencia puede ignorarse.
Para usar la función de pantalla oLed en el Arduino, conecte una pantalla oLed i2c en el bus i2c en el módulo del servomotor (vea el diagrama).
Instale la biblioteca U8g2 en el administrador de bibliotecas de Arduino:
Vaya a Sketch -> Incluir biblioteca -> Administrar bibliotecas...
BuscarU8gt. El editor de la biblioteca es "oliver".
Instale la última versión de la biblioteca.
Descomentar línea 74 #definir OLED en el boceto principal de Arduinowall-e.ino.
Si está utilizando una pantalla diferente que es compatible con la biblioteca, puede cambiar el constructor enlínea 78 como se documenta en elpágina de referencia de la biblioteca. El valor predeterminado es para una pantalla SH1106_128X64_NONAME.

Diagrama que muestra el cableado de la pantalla oLed

[f] Adición de sus propias animaciones servo (opcional)
Mi código viene con dos animaciones que replican escenas de la película; el movimiento de los ojos que hace Wall-E cuando arranca, y una secuencia de movimientos mientras Wall-E mira inquisitivamente a su alrededor. A partir de la versión 2.7 del código, ahora es más fácil agregar sus propias animaciones de servomotor para que pueda hacer que su Wall-E realice otros movimientos...
Abre el archivo animaciones.ino, que se encuentra en la misma carpeta que el boceto principal de Arduino.
Cada comando de animación consta de las posiciones a las que desea que se mueva cada uno de los servomotores y la cantidad de tiempo que la animación debe esperar hasta pasar a la siguiente instrucción.
Puede agregar una nueva animación insertando un extra caso sección en la sentencia switch. Debe insertar su código adicional en el espacio sobre el por defecto sección. Por ejemplo:
caso 3:
        // --- Título de su nueva secuencia de movimiento ---
        // tiempo,cabeza,necT,necB,eyeR,eyeL,armL,armR
        cola.push({ 12, 48, 40, 0, 35, 45, 60, 59});
        cola.push({1500, 48, 40, 20, 100, 0, 80, 80});
        // Agregue tantos movimientos adicionales aquí como necesite para completar la animación
        // queue.push({tiempo, rotación de la cabeza, parte superior del cuello, parte inferior del cuello, ojo derecho, ojo izquierdo, brazo izquierdo, brazo derecho})
        romper;
El tiempo debe ser un número en milisegundos (por ejemplo, 3,5 segundos = 3500)
Los comandos de posición del servomotor deben ser un número entero entre 0 y 100, donde 0 = BAJO y 100 = ALTO posición del servo según lo calibrado en el  wall-e_calibration.ino bosquejo.
Si desea deshabilitar un motor para un movimiento específico, puede usar -1.


2. Servidor web Raspberry Pi
[a] Instalación básica
Configure Raspberry Pi para ejecutar la última versión de Raspberry Pi OS (Raspbian) - Full. Las instrucciones de configuración se pueden encontrar en el sitio web de frambuesa pi.
Abra la terminal de línea de comandos en la Raspberry Pi.
Asegúrese de que la lista de paquetes se haya actualizado (esto puede llevar algún tiempo):sudo apt actualizar
InstalarMatraz - este es un marco de Python utilizado para crear servidores web:
Asegúrese de que pip esté instalado:sudo apt instalar python3-pip
Instale Flask y sus dependencias:sudo pip3 instalar matraz
(Opcional) ElLleno La versión de Raspbian incluye estos paquetes de forma predeterminada, pero si está utilizando un sistema operativo diferente (por ejemplo, elUn poco versión), deberá ejecutar estos comandos:
sudo apt install git libsdl1.2 libsdl-mixer1.2
sudo pip3 instalar pygame pyserial
Clone el repositorio en el directorio de inicio de Raspberry Pi:
discos compactos ~
clon de git https://github.com/chillibasket/walle-replica.git
Configure la contraseña del servidor web:
Abiertoapp.py:nano ~/walle-réplica/web_interface/app.py
En línea26 deapp.py donde esta diceponer_contraseña_aquí, inserte la contraseña que desea utilizar para la interfaz web.
(Opcional) Cambie el directorio de audio predeterminado y la ubicación de la secuencia de comandos utilizada para iniciar/detener la transmisión de video.
Si siguió exactamente los pasos anteriores, no hay necesidad de hacerlo. Sin embargo, si desea mover los archivos de la interfaz web a un directorio diferente en Raspberry Pi, deberá cambiar la ubicación donde el programa buscará los archivos de audio.
En línea29 deapp.py, escriba el directorio donde se encuentran los archivos de audio. Asegúrese de que la ubicación del directorio termine con una barra diagonal:/.
En línea28 deapp.py, se puede modificar la ubicación del script utilizado para iniciar y detener la transmisión de la cámara de video.
Conéctese al Arduino/microcontrolador:
Conecte el Arduino/microcontrolador al puerto USB de la Raspberry Pi.
Si desea que el puerto serie utilizado por Arduino se seleccione de forma predeterminada en la interfaz web, puede configurar un dispositivo de puerto serie preferido en el código. ir a la línea27 deapp.py y reemplace el texto "ARDUINO" con el nombre de su dispositivo. El nombre debe coincidir con el que aparece en el menú desplegable en la pestaña "Configuración" de la interfaz web.
Para hacer que la interfaz se conecte automáticamente al Arduino cuando se inicia, puede cambiar la línea31 aautoStartArduino = Verdadero
PrensaCTRL+O para ahorrar yCTRL + X para salir del editor nano.

[b] Uso del servidor web
Para determinar la dirección IP actual de Raspberry Pi en su red, escriba el comando:nombre de host -I
Para iniciar el servidor:python3 ~/walle-réplica/web_interface/app.py
Para acceder a la interfaz web, abra un navegador en cualquier computadora/dispositivo en la misma red y escriba la dirección IP de la Raspberry Pi, siga por:5000. Por ejemplo192.168.1.10:5000
Para detener el servidor presione:CTRL + C
Para comenzar a controlar el robot, primero debe iniciar la comunicación en serie con el Arduino. Para ello, acceda a laAjustes pestaña de la interfaz web, seleccione el puerto serie correcto de la lista desplegable y presione en elreconectar botón.

[c] Adición de una secuencia de cámara (opcional)
Si está utilizando la cámara oficial de Raspberry Pi, primero deberá habilitar la cámara ensudo raspi-config. En la pantalla de configuración que aparece, vaya a "Opciones de interfaz" > "Cámara" > "Habilitar".
Instalarstreamer-mjpg - esto se usa para transmitir el video al servidor web. Los pasos a continuación se basan en las instruccionesdescrito aquí para el proyecto CNC JS. Para instalar las bibliotecas necesarias, ejecute todos los siguientes comandos:
# Actualizar e instalar herramientas
sudo apt-get update-y
sudo apt-get upgrade-y
sudo apt-get install build-essential git imagemagick libv4l-dev libjpeg-dev cmake -y

# Clonar repositorio en /tmp
cd/tmp
clon de git https://github.com/jacksonliam/mjpg-streamer.git
cd mjpg-streamer/mjpg-streamer-experimental

# Hacer
hacer
sudo hacer instalar


Cree un nuevo script que se usará para iniciar y detener la transmisión de la cámara:nano ~/mjpg-streamer.sh
Pegue el siguiente código en el archivo de script; puede modificar la configuración de la velocidad de fotogramas, la calidad y la resolución para adaptarla a la cámara que está utilizando:
#!/bin/bash
# chmod +x mjpg-streamer.sh
# Crontab: @reboot /home/pi/mjpg-streamer/mjpg-streamer.sh inicio
# Crontab: @reboot /home/pi/mjpg-streamer/mjpg-streamer-experimental/mjpg-streamer.sh inicio

MJPG_STREAMER_BIN="/usr/local/bin/mjpg_streamer" # "$(dirname $0)/mjpg_streamer"
MJPG_STREAMER_WWW="/usr/local/share/mjpg-streamer/www"
MJPG_STREAMER_LOG_FILE="${0%.*}.log" # "$(dirname $0)/mjpg-streamer.log"
RUNNING_CHECK_INTERVAL="2" # con qué frecuencia verificar para asegurarse de que el servidor se está ejecutando (en segundos)
HANGING_CHECK_INTERVAL="3" # con qué frecuencia verificar para asegurarse de que el servidor no se cuelgue (en segundos)

VIDEO_DEV="/dev/video0"
FRAME_RATE="5"
CALIDAD="80"
RESOLUTION="1280x720"  # 160x120 176x144 320x240 352x288 424x240 432x240 640x360 640x480 800x448 800x600 960x544 1280x720 1920x1080 (QVGA, VGA, SVGA, WXGA)   #  lsusb -s 001:006 -v | egrep "Ancho|Alto" # https://www.textfixer.com/tools/alphabetical-order.php # v4l2-ctl --list-formats-ext # Mostrar formatos de video admitidos
PUERTO="8080"
YUV="verdadero"

###############INPUT_OPTIONS="-r ${RESOLUCIÓN} -d ${VIDEO_DEV} -f ${FRAME_RATE} -q ${CALIDAD} -pl 60hz"
INPUT_OPTIONS="-r ${RESOLUTION} -d ${VIDEO_DEV} -q ${QUALITY} -pl 60hz --every_frame 2" # Limite la velocidad de fotogramas con "--every_frame", ( mjpg_streamer --input "input_uvc.so -- ayuda" )


if [ "${YUV}" == "verdadero" ]; entonces
    INPUT_OPTIONS+=" -y"
ser

OUTPUT_OPTIONS="-p ${PUERTO} -w ${MJPG_STREAMER_WWW}"

# ================================================= =========
función en ejecución() {
    si ps aux | grep${MJPG_STREAMER_BIN} | grep ${VIDEO_DEV} >/dev/null 2>&1; entonces
        volver 0

    demás
        volver 1

    ser
}

inicio de función () {
    si está corriendo; entonces
        echo "[$VIDEO_DEV] ya comenzó"
        volver 1
    ser

    export LD_LIBRARY_PATH="$(dirname $MJPG_STREAMER_BIN):".

    echo "Iniciando: [$VIDEO_DEV] ${MJPG_STREAMER_BIN} -i \"input_uvc.so ${INPUT_OPTIONS}\" -o \"output_http.so ${OUTPUT_OPTIONS}\""
    ${MJPG_STREAMER_BIN} -i "input_uvc.so ${INPUT_OPTIONS}" -o "output_http.so ${OUTPUT_OPTIONS}" >> ${MJPG_STREAMER_LOG_FILE} 2>&1 &

    dormir 1

    si está corriendo; entonces
        if [ "$1" != "nocheck" ]; entonces
            check_running & > /dev/null 2>&1 # iniciar la tarea de comprobación en ejecución
            check_hanging & > /dev/null 2>&1 # iniciar la tarea de comprobación de colgantes
        ser

        echo "[$VIDEO_DEV] comenzó"
        volver 0

    demás
        echo "[$VIDEO_DEV] no se pudo iniciar"
        volver 1

    ser
}

función detener() {
    si ! correr; entonces
        echo "[$VIDEO_DEV] no se está ejecutando"
        volver 1
    ser

    propio_pid=$$

    if [ "$1" != "nocheck" ]; entonces
        # detener el script que ejecuta la tarea de verificación
        ps auxiliar | grep $0 | inicio de grep | tr -s ' ' | cortar -d ' ' -f 2 | grep -v ${propio_pid} | xargs -r matar
        dormir 0.5
    ser

    # detener el servidor
    ps auxiliar | grep${MJPG_STREAMER_BIN} | grep ${VIDEO_DEV} | tr -s ' ' | cortar -d ' ' -f 2 | grep -v ${propio_pid} | xargs -r matar

    echo "[$VIDEO_DEV] detenido"
    volver 0
}

función verificar_ejecutar() {
    echo "[$VIDEO_DEV] comenzando a ejecutar la tarea de verificación" >> ${MJPG_STREAMER_LOG_FILE}

    mientras que cierto; hacer
        dormir ${RUNNING_CHECK_INTERVAL}

        si ! correr; entonces
            echo "[$VIDEO_DEV] servidor detenido, iniciando" >> ${MJPG_STREAMER_LOG_FILE}
            empezar sin verificar
        ser
    hecho
}

función comprobar_colgar() {
    echo "[$VIDEO_DEV] iniciando la tarea de comprobación pendiente" >> ${MJPG_STREAMER_LOG_FILE}

    mientras que cierto; hacer
        dormir ${HANGING_CHECK_INTERVAL}

        # tratar el caso de "error al capturar fotogramas"
        si cola -n2 ${MJPG_STREAMER_LOG_FILE} | grep -i "error al capturar fotogramas" > /dev/null; entonces
            echo "[$VIDEO_DEV] el servidor está colgando, matando" >> ${MJPG_STREAMER_LOG_FILE}
            no dejes de comprobar
        ser
    hecho
}

función de ayuda () {
    echo "Uso: $0 [iniciar|detener|reiniciar|estado]"
    volver 0
}

if [ "$1" == "inicio" ]; entonces
    iniciar && salir 0 || salida -1

elif [ "$1" == "detener" ]; entonces
    detener && salir 0 || salida -1

elif [ "$1" == "reiniciar" ]; entonces
    detener y dormir 1
    iniciar && salir 0 || salida -1

elif [ "$1" == "estado" ]; entonces
    si está corriendo; entonces
        echo "[$VIDEO_DEV] en ejecución"
        salida 0

    demás
        echo "[$VIDEO_DEV] detenido"
        salida 1

    ser

demás
    ayuda

ser


Presiona CTRL+O para ahorrar yCTRL + X para salir del editor nano.
Asegúrese de que la secuencia de comandos del administrador que creó tenga el nombre correcto y esté en el directorio correcto:/home/pi/mjpg-streamer.sh. Si desea guardar el script en una ubicación diferente, debe actualizar la línea 22 deapp.py.
Para hacer que el script sea ejecutable por el servidor web, ejecute este comando en la terminal:chmod +x /home/pi/mjpg-streamer.sh
Si desea que la cámara se inicie automáticamente cuando abra la interfaz web, puede cambiar la línea32 deapp.py aautoStartCamera = Verdadero
