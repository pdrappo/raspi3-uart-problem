Solucionar problemas de escritura y lectura en UART de Raspberry Pi 3 
===================
Puede estar pasándote como a mí que perdí muchas horas intentando comunicar la **Raspberry Pi 3** con dispositivos serie y obteniendo escrituras o respuestas incorrectas con falsos valores o valores indeseados. En mi caso intentaba comunicarme mediante RS-485 Modbus con un controlador de temperatura de la marca IEA para obtener y setear algunos valores, utilizando un circuito parecido al que se muestra aquí.
Problema
Charlando con un compañero de trabajo encontró un video en YouTube en el cual se explica que en este último modelo ya no tiene una UART como sus antecesores sino que tiene una Mini-UART (UART1) que tiene ciertas limitaciones, entre ellas es que el Baudrate que le configuramos trabaja con el reloj del sistema operativo y no tiene uno independiente para dicho fin. Esto provoca que el valor que le seteamos no sea en realidad al que está trabajando físicamente el puerto serie de la Raspi, provocando transmisiones de datos erróneos causando múltiples problemas a la hora de realizar una comunicación serie.
Solución
La solución que propone este buen hombre es deshabilitar el bluetooth del dispositivo (Para liberar la UART0) y mapear los pines 14 y 15 a la UART0 convencional que tenían los modelos previos a este para que el reloj del Baudrate coincida con los valores seteados y se eviten problemas.
¿Cómo realizamos dicha operación?


## Actualizamos
1) Primero que nada actualizamos la raspi para ver si existe algun parche que ya solucione esto.

```sh
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
sudo rpi-update
```


## Deshabilitar Bluetooth en la Pi3
2) Agregamos la siguiente línea al final del archivo de configuración

```sh
sudo nano /boot/config.txt
```
>dtoverlay=pi3-disable-bt

Luego ejecutamos en el terminal:
```sh
sudo systemctl disable hciuart
```

3) Editamos el siguiente archivo y donde dice en mi caso "serial1" también puede decir AMA0 por ttyS0 y guardamos
```sh
sudo nano /lib/systemd/system/hciuart.server
```

4) Editamos el archivo cmdline.txt y lo dejamos de la siguiente manera
```sh
sudo nano /boot/cmdline.txt
```
>dwc_otg.lpm_enable=0 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait

####Versión de Raspberry del tutorial
Linux raspberrypi 4.4.21-v7+ #911 SMP Thu Sep 15 14:22:38 BST 2016 armv7l GNU/Linux

Fuente de información: https://www.youtube.com/watch?v=lmLsKzNBMlA
