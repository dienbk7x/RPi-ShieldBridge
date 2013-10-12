# Arduino IDE on Raspberry Pi with RPi-ShieldBridge

* Close the Jumpers: **JTX**, **JRX** and **JBL** on the RPi-ShieldBridge.

* Install the Arduino IDE:

    ```
    $ sudo apt-get update
    $ sudo apt-get install arduino
    ```

* Install [Auto-Reset for AVRdude](https://github.com/CisecoPlc/avrdude-rpi) on GPIO18 (Pin 12):

    ```
    $ wget https://raw.github.com/watterott/RPi-ShieldBridge/master/docu/autoreset
    $ wget https://raw.github.com/watterott/RPi-ShieldBridge/master/docu/avrdude-autoreset
    $ cp autoreset /usr/bin
    $ cp avrdude-autoreset /usr/bin
    $ mv /usr/bin/avrdude /usr/bin/avrdude-original
    $ ln -s /usr/bin/avrdude-autoreset /usr/bin/avrdude
    ```

* [Disable Linux serial console](http://elinux.org/RPi_Serial_Connection#Preventing_Linux_using_the_serial_port):

    ```
    $ sudo nano /boot/cmdline.txt
    ```

    Remove references to ```console=ttyAMA0,115200``` and ```kgdboc=ttyAMA0,115200```.

    ```
    $ sudo nano /etc/inittab
    ```

    Remove or comment the line ```T0:23:respawn:/sbin/getty -L ttyAMA0 115200 vt100```.
    
    Reboot the system.

* Run the Arduino IDE.

* Select **Arduino Uno** under **Tools->Board**.

* Select the hardware serial port */dev/ttyAMA0* under **Tools->Serial Port**.

* Open the Blink Sketch under **File->Examples->01.Basics->Blink**.

* Upload the Sketch.

* The LED on the RPi-ShieldBridge will start to blink after the upload is finished.
