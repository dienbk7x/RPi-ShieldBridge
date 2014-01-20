# MI0283QT-Adapter v1

There is a [Linux Framebuffer driver (FBTFT)](https://github.com/notro/fbtft/wiki) available for the display adapter with MI0283QT-2 + MI0283QT-9A display panel and the ADS7846 touch controller has also a Linux device driver.

## Installation

* [Install FBTFT](https://github.com/notro/fbtft/wiki#install) Framebuffer.

* Install Touchscreen Tools:

    ```
    $ sudo apt-get install xinput evtest
    ```

* [Activate Framebuffer](https://github.com/notro/fbtft/wiki#enable-driver):

    ```
    $ sudo modprobe fbtft_device name=mi0283qt-9a cs=0 gpios=reset:23,led:24 rotate=90 speed=16000000
    ```
    or
    ```
    $ sudo modprobe fbtft_device name=mi0283qt-2 cs=0 gpios=reset:23,led:24 rotate=90 speed=16000000
    ```

    To make it permanent (on Debian) add to the file ```/etc/modules``` the following line:
    ```fbtft_device name=mi0283qt-9a cs=0 gpios=reset:23,led:24 rotate=90 speed=16000000```

    *Note: For a higher speed than 16MHz the display has to be connected directly to the RPi-ShieldBridge or with wires not longer than 5cm.*

* [Activate Touchpanel](https://github.com/notro/fbtft/wiki/Touchpanel#watterott-mi0283qt-9a):

    ```
    $ sudo modprobe ads7846_device model=7846 cs=1 gpio_pendown=25 speed=2000000 keep_vref_on=1 swap_xy=1 pressure_max=255 x_plate_ohms=60 x_min=250 x_max=3780 y_min=160 y_max=3930
    ```
    *Note: The Jumper JIRQ has to be closed.*

    To make it permanent (on Debian) add to the file ```/etc/modules``` the following line:
    ```ads7846_device model=7846 cs=1 gpio_pendown=25 speed=2000000 keep_vref_on=1 swap_xy=1 pressure_max=255 x_plate_ohms=60 x_min=250 x_max=3780 y_min=160 y_max=3930```


* Enable for X-Window-System:

    ```
    $ FRAMEBUFFER=/dev/fb1 startx &
    ```

    *...wait till X-Window-System starts up...*

    ```
    $ DISPLAY=:0 xinput --set-prop 'ADS7846 Touchscreen' 'Evdev Axis Inversion' 1 0
    ```

   *...to stop X-Window-System run:*

    ```
   $ sudo pkill x
    ```

* Enable for Console:

    ```
    $ con2fbmap 1 1
    ```

    To make it permanent (on Debian) add to the file ```/boot/cmdline.txt``` the following kernel argument:
    ```fbcon=map:10```


### Further Infos/Tutorials

* [MI0283QT9A + RPi-ShieldBridge](http://lallafa.de/blog/2013/07/watterotts-new-rpi-shieldbridge/)
* [MI0283QT9A + RPi-ShieldBridge (German)](http://www.mdtweb.de/index.php/projekte/mikroprozessoren/raspberry-pi/rpi-lcd-ansteuerung)
* [MI0283QT9A driver installation](http://www.raspberrypi.org/phpBB3/viewtopic.php?f=44&t=61992&p=460809)
* [MI0283QT9A driver (flexfb) installation](http://lallafa.de/blog/2013/06/watterott-mi0283qt-9-display-with-generic-flexfb-driver/)
* [MI0283QT9A driver installation](http://lallafa.de/blog/2013/03/watterott-mi0283qt-9a-display-for-the-rasbperry-pi/)
* [MI0283QT2 driver installation](http://lallafa.de/blog/2013/03/watterott-display-on-raspberry-pi/)
* [ADS7846 touch controller driver installation](http://lallafa.de/blog/2013/03/adding-touch-support-for-the-mi0283qt-displays/)


### Display Connection

```
Display  Raspberry Pi
---------------------
LCD-LED  GPIO24
LCD-RST  GPIO23
LCD-CS   GPIO8  (CE0) [on RPi-ShieldBridge v1.0: CE1]
ADS-CS   GPIO7  (CE1) [on RPi-ShieldBridge v1.0: CE0]
ADS-IRQ  GPIO25 (close Jumper JIRQ, open Jumper JSDA+JSCL)
MISO     GPIO9  (MISO)
MOSI     GPIO10 (MOSI)
SCK      GPIO11 (SCK)
```


# MI0283QT-Adapter v2

There is an experimental [Linux Framebuffer driver (fbtft)](https://github.com/notro/fbtft/wiki) available for the display adapter v2 with ARM controller.

## Installation

* [Install FBTFT](https://github.com/notro/fbtft/wiki#install).

* Activate Framebuffer:

    ```
    $ sudo modprobe fbtft_device name=mi0283qt-v2 cs=0 rotate=1
    ```

* Enable for X Windows:

    ```
    $ FRAMEBUFFER=/dev/fb1 startx &
    ```

   *...to stop X-Window-System run:*

    ```
   $ sudo pkill x
    ```

* Enable for Console:

    ```
    $ con2fbmap 1 1
    ```

    To make it permanent (on Debian) add to the file ```/boot/cmdline.txt``` the following kernel argument:
    ```fbcon=map:10```


### Display Connection

```
Display  Raspberry Pi
---------------------
LCD-RST  GPIO23
LCD-CS   GPIO8  (CE0) [on RPi-ShieldBridge v1.0: CE1]
 --- SPI/SSI ---
MISO     GPIO9  (MISO)
MOSI     GPIO10 (MOSI)
SCK      GPIO11 (SCK)
 --- I2C ---
LCD-SDA  GPIO2  (SDA, close Jumper JSDA)
LCD-SCL  GPIO3  (SCL, close Jumper JSCL, open Jumper JIRQ)
```

Note: The Raspberry Pi hardware revision 1 boards connect I2C bus 0 (GPIO 0 + 1) and revision 2 boards connect I2C bus 1 (GPIO 2 + 3) to the GPIO connector.
