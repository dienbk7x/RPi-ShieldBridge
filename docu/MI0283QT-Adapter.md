# MI0283QT-Adapter v1

There is a [Linux Framebuffer driver (FBTFT)](https://github.com/notro/fbtft/wiki) available for the display adapter with MI0283QT-2 + MI0283QT-9A display panel and the ADS7846 touch controller has also a Linux device driver.

## Installation

* [Install FBTFT](https://github.com/notro/fbtft/wiki#install) Framebuffer.

* Install Touchscreen tools:

    ```
    $ sudo apt-get install libts-bin evtest xinput
    ```

* [Activate Framebuffer](https://github.com/notro/fbtft/wiki#enable-driver):

    ```
    $ sudo modprobe fbtft_device name=mi0283qt-9a cs=0 gpios=reset:23,led:24 rotate=90 speed=16000000
    ```
    or
    ```
    $ sudo modprobe fbtft_device name=mi0283qt-2 cs=0 gpios=cs:8,reset:23,led:24 rotate=90 speed=16000000
    ```

* [Activate Touchpanel](https://github.com/notro/fbtft/wiki/Touchpanel#watterott-mi0283qt-9a):

    ```
    $ sudo modprobe ads7846_device model=7846 cs=1 gpio_pendown=25 speed=2000000 keep_vref_on=1 swap_xy=1 pressure_max=255 x_plate_ohms=60 x_min=250 x_max=3780 y_min=160 y_max=3930
    ```
    *Note: The Jumper JIRQ has to be closed.*

* Enable for X-Windows:

    ```
    $ FRAMEBUFFER=/dev/fb1 startx & 
    $ DISPLAY=:0 xinput --set-prop 'ADS7846 Touchscreen' 'Evdev Axis Inversion' 1 0
    ```

* Enable for Console:

    ```
    $ con2fbmap 1 1
    ```

### Further Infos/Tutorials

* [MI0283QT9A + RPi-ShieldBridge](http://lallafa.de/blog/2013/07/watterotts-new-rpi-shieldbridge/)
* [MI0283QT9A + RPi-ShieldBridge (German)](http://www.mdtweb.de/index.php/projekte/mikroprozessoren/raspberry-pi/rpi-lcd-ansteuerung)
* [MI0283QT9A driver (flexfb)](http://lallafa.de/blog/2013/06/watterott-mi0283qt-9-display-with-generic-flexfb-driver/)
* [MI0283QT9A driver](http://lallafa.de/blog/2013/03/watterott-mi0283qt-9a-display-for-the-rasbperry-pi/)
* [MI0283QT2 driver](http://lallafa.de/blog/2013/03/watterott-display-on-raspberry-pi/)
* [ADS7846 touch controller driver](http://lallafa.de/blog/2013/03/adding-touch-support-for-the-mi0283qt-displays/)

### Display Connection

```
Display  Raspberry Pi
---------------------
LCD-RST  GPIO23
LCD-LED  GPIO24
LCD-CS   CE0 / GPIO8 (on v1.0: CE1 / GPIO7)
ADS-CS   CE1 / GPIO7 (on v1.0: CE0 / GPIO8)
ADS-IRQ  GPIO25 (close Jumper JIRQ, open Jumper JSDA+JSCL)
MISO     MISO / GPIO9
MOSI     MOSI / GPIO10
SCK      SCK  / GPIO11
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
    $ FRAMEBUFFER=/dev/fb1 startx
    ```

* Enable for Console:

    ```
    $ con2fbmap 1 1
    ```

### Display Connection

```
Display  Raspberry Pi
---------------------
LCD-RST  GPIO23
LCD-CS   CE0 / GPIO8 (on v1.0: CE1 / GPIO7)
 --- SPI/SSI ---
MISO     MISO / GPIO9
MOSI     MOSI / GPIO10
SCK      SCK  / GPIO11
 --- I2C ---
LCD-SDA  SDA / GPIO2 (close Jumper JSDA)
LCD-SCL  SCL / GPIO3 (close Jumper JSCL, open Jumper JIRQ)
```

Note: The Raspberry Pi hardware revision 1 boards connect I2C bus 0 (GPIO 0 + 1) and revision 2 boards connect I2C bus 1 (GPIO 2 + 3) to the GPIO connector.
