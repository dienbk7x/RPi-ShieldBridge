# MI0283QT-Adapter v1

There is a [Linux Framebuffer driver (FBTFT)](http://fbtft.org) available for the display adapter with MI0283QT-2 (HX8347D) + MI0283QT-9 / -11 (ILI9341) display panel and the ADS7846 / TSC2046 touch controller has also a Linux device driver.

## SD-Card Image

It is possible to use the ready to run SD-Card image for the RPi-Display with modified kernel parameters (/boot/cmdline.txt).
* [RPi-Display SD-Card Image](https://github.com/watterott/RPi-Display#sd-card-image)
* [cmdline.txt for RPi-ShieldBridge+MI0283QT-Adapter](https://github.com/watterott/RPi-Display/raw/master/docu/cmdline_9bit_sb.txt)

## Linux Driver Installation

* [Install FBTFT](https://github.com/notro/fbtft/wiki#install) Framebuffer:

    Install **rpi-update** for the kernel update:
    ```
    $ sudo wget https://raw.github.com/Hexxeh/rpi-update/master/rpi-update -O /usr/bin/rpi-update
    $ sudo chmod +x /usr/bin/rpi-update
    ```
    *If you get certificate errors, then run ```wget``` with ```--no-check-certificate```.*

    Remove or comment out the SPI blacklist line (**spi-bcm2708**):
    ```
    $ sudo nano /etc/modprobe.d/raspi-blacklist.conf
    ```

    Start the kernel update:
    ```
    $ sudo REPO_URI=https://github.com/notro/rpi-firmware rpi-update
    ```
    *On compatibility errors run rpi-update as follows: ```sudo -E RPI_UPDATE_UNSUPPORTED=0 REPO_URI=https://github.com/notro/rpi-firmware rpi-update```*

    *If FBTFT is not working correctly, then try the kernel without built-in FBTFT drivers: ```sudo REPO_URI=https://github.com/notro/rpi-firmware BRANCH=builtin rpi-update```*

    Reboot the system:
    ```
    $ sudo shutdown -r now
    ```

* Install Touchscreen Tools:

    ```
    $ sudo apt-get install xinput
    ```

* [Activate Framebuffer](https://github.com/notro/fbtft/wiki#enable-driver):

    *Note: For a higher speed than 16MHz the display has to be connected directly to the Raspberry Pi or with wires not longer than 5cm.*

    * FBTFT modules

        ```
        $ sudo modprobe fbtft_device name=mi0283qt-9a cs=0 gpios=reset:23,led:24 speed=16000000 rotate=270
        ```
        or for the older display adapters
        ```
        $ sudo modprobe fbtft_device name=mi0283qt-2 cs=0 gpios=reset:23,led:24 speed=16000000 rotate=270
        ```
        To make it permanent (on Debian) add to the file ```/etc/modules``` the following line:
        ```
        fbtft_device name=mi0283qt-9a cs=0 gpios=reset:23,led:24 speed=16000000 rotate=270
        ```

    * FBTFT compiled into kernel ```BRANCH=builtin```

        Kernel argument ([/boot/cmdline.txt](https://github.com/watterott/RPi-Display/raw/master/docu/cmdline_9bit_sb.txt)):
        ```fbtft_device.name=mi0283qt-9a fbtft_device.speed=16000000 fbtft_device.gpios=reset:23,led:24 fbtft_device.rotate=270```
        *(Replace existing fbtft parameters with the new ones and make sure everything is in one line.)*

* [Activate Touchpanel](https://github.com/notro/fbtft/wiki/Touchpanel#watterott-mi0283qt-9a):

    ```
    $ sudo modprobe ads7846_device model=7846 cs=1 gpio_pendown=25 speed=2000000 keep_vref_on=1 swap_xy=1 pressure_max=255 x_plate_ohms=60 x_min=200 x_max=3900 y_min=200 y_max=3900
    ```
    *Note: The Jumper JIRQ has to be closed.*

    To make it permanent (on Debian) add to the file ```/etc/modules``` the following line:
    ```
    ads7846_device model=7846 cs=1 gpio_pendown=25 speed=2000000 keep_vref_on=1 swap_xy=1 pressure_max=255 x_plate_ohms=60 x_min=200 x_max=3900 y_min=200 y_max=3900
    ```

* Calibrate Touchpanel (optional):

    For better accuracy a touchpanel calibration can be done with:

    **[xinput_calibrator](https://github.com/tias/xinput_calibrator)**
    ```
    $ sudo FRAMEBUFFER=/dev/fb1 xinput_calibrator &
    $ DISPLAY=:0 xinput --set-prop 'ADS7846 Touchscreen' 'Evdev Axis Inversion' 1 0
    ```

    **ts_calibrate**
    ```
    $ sudo apt-get install libts-bin
    $ sudo TSLIB_FBDEVICE=/dev/fb1 TSLIB_TSDEVICE=/dev/input/event0 ts_calibrate
    ```

* Enable for Console:

    ```
    $ con2fbmap 1 1
    ```
    To make it permanent (on Debian) add to the file ```/boot/cmdline.txt``` at the end of the line the following kernel argument: ```fbcon=map:10 fbcon=font:VGA8x8```

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
    To make it permanent (on Debian) see [autostart x](https://github.com/notro/fbtft/wiki#make-it-permanent-debian) and [xinput axis inversion](https://github.com/notro/fbtft/wiki/Touchpanel#-xinput---make-it-permanent).
    If the X-Window-System is on the wrong monitor, have a look at [this issue](https://github.com/notro/fbtft/issues/63).

* Video Test:

    *Note: The video file is about 60MB big.*
    ```
    $ wget http://download.blender.org/peach/bigbuckbunny_movies/BigBuckBunny_320x180.mp4
    ```

    Play with **mplayer**:
    ```
    $ sudo apt-get install mplayer
    $ mplayer -vo fbdev2:/dev/fb1 -vf scale=320:-3 BigBuckBunny_320x180.mp4
    ```

    Play with **omxplayer** (*fbcp* for framebuffer mirroring required):
    ```
    $ fbcp &
    $ omxplayer BigBuckBunny_320x180.mp4
    $ killall fbcp
    ```

### Further Infos/Tutorials

* [MI0283QT9 + RPi-ShieldBridge](http://lallafa.de/blog/2013/07/watterotts-new-rpi-shieldbridge/)
* [MI0283QT9 + RPi-ShieldBridge (German)](http://www.mdtweb.de/index.php/projekte/mikroprozessoren/raspberry-pi/rpi-lcd-ansteuerung)
* [MI0283QT9 + RPi-ShieldBridge (German)](http://www.forum-raspberrypi.de/Thread-tutorial-watterott-mi0283qt-9a-am-pi-betreiben)
* [MI0283QT9 driver installation](http://lallafa.de/blog/2013/03/watterott-mi0283qt-9a-display-for-the-rasbperry-pi/)
* [MI0283QT2 driver installation](http://lallafa.de/blog/2013/03/watterott-display-on-raspberry-pi/)
* [ADS7846 driver installation](http://lallafa.de/blog/2013/03/adding-touch-support-for-the-mi0283qt-displays/)
* [MI0283QT9 and BeagleBone Black](https://github.com/notro/fbtft/wiki/BeagleBone-Black)

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

## Linux Driver Installation

* [Install FBTFT](https://github.com/notro/fbtft/wiki#install) Framebuffer:

    Install **rpi-update** for the kernel update:
    ```
    $ sudo wget https://raw.github.com/Hexxeh/rpi-update/master/rpi-update -O /usr/bin/rpi-update
    $ sudo chmod +x /usr/bin/rpi-update
    ```
    *If you get certificate errors, then run ```wget``` with ```--no-check-certificate```.*

    Remove or comment out the SPI blacklist line (**spi-bcm2708**):
    ```
    $ sudo nano /etc/modprobe.d/raspi-blacklist.conf
    ```

    Start the kernel update:
    ```
    $ sudo REPO_URI=https://github.com/notro/rpi-firmware rpi-update
    ```
    *On compatibility errors run rpi-update as follows: ```sudo -E RPI_UPDATE_UNSUPPORTED=0 REPO_URI=https://github.com/notro/rpi-firmware rpi-update```*

    *If FBTFT is not working correctly, then try the kernel without built-in FBTFT drivers: ```sudo REPO_URI=https://github.com/notro/rpi-firmware BRANCH=builtin rpi-update```*

    Reboot the system:
    ```
    $ sudo shutdown -r now
    ```

* Activate Framebuffer:

    * FBTFT modules

        ```
        $ sudo modprobe fbtft_device name=mi0283qt-v2 cs=0 rotate=270
        ```

        To make it permanent (on Debian) add to the file ```/etc/modules``` the following line:
        ```
        fbtft_device name=mi0283qt-v2 cs=0 rotate=270
        ```

    * FBTFT compiled into kernel ```BRANCH=builtin```

        Kernel argument (/boot/cmdline.txt):
        ```fbtft_device.name=mi0283qt-v2 fbtft_device.rotate=270```
        *(Replace existing fbtft parameters with the new ones and make sure everything is in one line.)*

* Enable for Console:

    ```
    $ con2fbmap 1 1
    ```

    To make it permanent (on Debian) add to the file ```/boot/cmdline.txt``` at the end of the line the following kernel argument: ```fbcon=map:10 fbcon=font:VGA8x8```

* Enable for X Windows:

    ```
    $ FRAMEBUFFER=/dev/fb1 startx &
    ```
    *...to stop X-Window-System run:*
    ```
    $ sudo pkill x
    ```
    To make it permanent (on Debian) see [autostart x](https://github.com/notro/fbtft/wiki#make-it-permanent-debian).
    If the X-Window-System is on the wrong monitor, have a look at [this issue](https://github.com/notro/fbtft/issues/63).

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

*Note: The Raspberry Pi hardware revision 1 boards connect I2C bus 0 (GPIO 0 + 1) and revision 2 boards connect I2C bus 1 (GPIO 2 + 3) to the GPIO connector.*
