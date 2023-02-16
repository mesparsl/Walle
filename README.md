# Walle
El wall-e es nuestro proyecto final nos hemos basado en este wall-e que encontramos por internet https://3dwork.io/wall-e-replica-impresion-3d/
!

[image](https://user-images.githubusercontent.com/124558681/219344904-b29a2cb5-0929-498d-a3b3-13a339d1f519.png)

![image](https://user-images.githubusercontent.com/124558681/219345127-665d8ab7-9a26-4f61-9961-66b2fd04af53.png)

![image](https://user-images.githubusercontent.com/124558681/219345233-ce226052-e31f-40c0-b928-545467cb2395.png)

![image](https://user-images.githubusercontent.com/124558681/219345407-40a3ea62-2361-4a4c-a030-557085c171d3.png)


1. Código Arduino (wall-e)

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

![image](https://user-images.githubusercontent.com/124558681/219345832-1cede5c3-23d4-419e-b573-4160666d22b8.png)

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

![image](https://user-images.githubusercontent.com/124558681/219345675-00d50894-3fb8-43b6-ab68-519fc2384b34.png)

2. Servidor web Raspberry Pi

[a] Instalación básica

Configure Raspberry Pi para ejecutar la última versión de Raspberry Pi OS (Raspbian) - Full. Las instrucciones de configuración se pueden encontrar en el sitio web de frambuesa pi.

Abra la terminal de línea de comandos en la Raspberry Pi.

Asegúrese de que la lista de paquetes se haya actualizado (esto puede llevar algún tiempo):sudo apt update

Instalar Matraz - este es un marco de Python utilizado para crear servidores web:

Asegúrese de que pip esté instalado:sudo apt-get install python3-pip

Instale Flask y sus dependencias:sudo apt-get install python3-flask

sudo apt install git libsdl1.2-dev

sudo apt install git libsdl-mixer1.2-dev

sudo pip3 instalar pygame pyserial

Clone el repositorio en el directorio de inicio de Raspberry Pi:

cd ~
git clone https://github.com/chillibasket/walle-replica.git

Configure la contraseña del servidor web:

nano ~/walle-replica/web_interface/app.py

En línea26 deapp.py donde esta dice put_password_here, inserte la contraseña que desea utilizar para la interfaz web.

(Opcional) Cambie el directorio de audio predeterminado y la ubicación de la secuencia de comandos utilizada para iniciar/detener la transmisión de video.

Si siguió exactamente los pasos anteriores, no hay necesidad de hacerlo. Sin embargo, si desea mover los archivos de la interfaz web a un directorio diferente en Raspberry Pi, deberá cambiar la ubicación donde el programa buscará los archivos de audio.

En línea29 de app.py, escriba el directorio donde se encuentran los archivos de audio. Asegúrese de que la ubicación del directorio termine con una barra diagonal:/.

En línea28 de app.py, se puede modificar la ubicación del script utilizado para iniciar y detener la transmisión de la cámara de video.

Conéctese al Arduino/microcontrolador:

Conecte el Arduino/microcontrolador al puerto USB de la Raspberry Pi.

Si desea que el puerto serie utilizado por Arduino se seleccione de forma predeterminada en la interfaz web, puede configurar un dispositivo de puerto serie preferido en el código. ir a la línea27 deapp.py y reemplace el texto "ARDUINO" con el nombre de su dispositivo. El nombre debe coincidir con el que aparece en el menú desplegable en la pestaña "Configuración" de la interfaz web.

Para hacer que la interfaz se conecte automáticamente al Arduino cuando se inicia, puede cambiar la línea31 aautoStartArduino = Verdadero

Prensa CTRL+O para ahorrar yCTRL + X para salir del editor nano.

[b] Uso del servidor web

Para determinar la dirección IP actual de Raspberry Pi en su red, escriba el comando: hostname -I

Para iniciar el servidor:python3 ~/walle-réplica/web_interface/app.py

Para acceder a la interfaz web, abra un navegador en cualquier computadora/dispositivo en la misma red y escriba la dirección IP de la Raspberry Pi, siga por:5000. Por ejemplo192.168.1.10:5000

Para detener el servidor presione:CTRL + C

Para comenzar a controlar el robot, primero debe iniciar la comunicación en serie con el Arduino. Para ello, acceda a laAjustes pestaña de la interfaz web, seleccione el puerto serie correcto de la lista desplegable y presione en elreconectar botón.

![image](https://user-images.githubusercontent.com/124558681/219349240-45c988bf-031b-4cdc-a12d-8d9cf39b97a3.png)

![image](https://user-images.githubusercontent.com/124558681/219346511-f95e8fbe-eb8b-4622-ab1d-397a0f2a055b.png)

![image](https://user-images.githubusercontent.com/124558681/219349122-5fb46807-cc09-47a3-9981-7172d94dde34.png)
