# MI0283QT-Adapter v1

There exists a [Linux Framebuffer driver (fbtft)](https://github.com/notro/fbtft) for the display adapter with MI0283QT2 + MI0283QT9A display panel and the ADS7846 touch controller has already a Linux device driver.

Here are some Links with further infos:
* [MI0283QT9A + RPi-ShieldBridge](http://lallafa.de/blog/2013/07/watterotts-new-rpi-shieldbridge/)
* [MI0283QT9A + RPi-ShieldBridge (German)](http://www.mdtweb.de/index.php/projekte/mikroprozessoren/raspberry-pi/rpi-lcd-ansteuerung)
* [MI0283QT9A driver (flexfb)](http://lallafa.de/blog/2013/06/watterott-mi0283qt-9-display-with-generic-flexfb-driver/)
* [MI0283QT9A driver](http://lallafa.de/blog/2013/03/watterott-mi0283qt-9a-display-for-the-rasbperry-pi/)
* [MI0283QT2 driver](http://lallafa.de/blog/2013/03/watterott-display-on-raspberry-pi/)
* [ADS7846 driver](http://lallafa.de/blog/2013/03/adding-touch-support-for-the-mi0283qt-displays/)

Display connection on the RPi-ShieldBridge:
```
Display  Raspberry Pi
---------------------
LCD-RST  GPIO23
LCD-LED  GPIO24
LCD-CS   CE0 / GPIO8
ADS-CS   CE1 / GPIO7
ADS-IRQ  GPIO25 (close Jumper JIRQ, open Jumper JSDA+JSCL)
MISO     MISO / GPIO9
MOSI     MOSI / GPIO10
SCK      SCK  / GPIO11

Note on RPi-ShieldBridge v1.0 the CS pins are swapped:
LCD-CS   CE1 / GPIO7
ADS-CS   CE0 / GPIO8
```


# MI0283QT-Adapter v2

Display connection on the RPi-ShieldBridge:
```
Display  Raspberry Pi
---------------------
LCD-RST  GPIO23
LCD-CS   CE0 / GPIO8 (on RPi-ShieldBridge v1.0: CE1 / GPIO7)
 --- SPI/SSI ---
MISO     MISO / GPIO9
MOSI     MOSI / GPIO10
SCK      SCK  / GPIO11
 --- I2C ---
LCD-SDA  SDA / GPIO2 (close Jumper JSDA)
LCD-SCL  SCL / GPIO3 (close Jumper JSCL, open Jumper JIRQ)

```
