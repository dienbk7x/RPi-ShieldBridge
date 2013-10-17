# Firmata

[Firmata](http://firmata.org) is a protocol for communicating with microcontrollers from software on a host computer.
So you can control an Arduino with a Raspberry Pi using Firmata.

There is a tutorial for Firmata and Raspberry Pi in [The MagPi Issue 7](http://www.themagpi.com/en/issue/7) and here is also a small guide.


## Prepare Arduino / RPi-ShieldBridge

* **Sketch upload with FTDI adapter**
  * Download and install the [Arduino IDE](http://arduino.cc/en/Main/Software) on your computer or [Raspberry Pi](https://github.com/watterott/RPi-ShieldBridge/blob/master/docu/Arduino.md).
  * Close the **JVCC Jumper** next to the FTDI connector to power the RPi-ShieldBridge from the FTDI adapter or put it on a Raspberry Pi for powering. 
  * Connect the RPi-ShieldBridge with a FTDI adapter (5V) to the computer or Raspberry Pi.
  * Run the Arduino IDE.
  * Select **Arduino Uno** under **Tools->Board**.
  * Select the serial port from the FTDI adapter under **Tools->Serial Port**.
  * Open the Firmata Sketch under **File->Examples->Firmata->StandardFirmata**.
  * Upload the Sketch.
    If there are upload problems then add a pull-down resistor (10k-47k Ohm) between **GPIO18** or **Jumper JBL** and GND.
  * Now exit the Arduino IDE.

* **Sketch upload via hardware serial port of the Raspberry Pi**
  * Close the Jumpers: **JTX**, **JRX** and **JBL** on the RPi-ShieldBridge.
  * [Install the Arduino IDE on the Raspberry Pi](https://github.com/watterott/RPi-ShieldBridge/blob/master/docu/Arduino.md).
  * Run the Arduino IDE.
  * Select **Arduino Uno** under **Tools->Board**.
  * Select the hardware serial port **/dev/ttyAMA0** under **Tools->Serial Port**.
  * Open the Firmata Sketch under **File->Examples->Firmata->StandardFirmata**.
  * Upload the Sketch.
  * Now exit the Arduino IDE.


## Prepare Raspberry Pi

The RPi-ShieldBridge can be connected via USB or UART:

* **USB connection (FTDI adapter)**
  * Put the RPi-ShieldBridge on the Raspberry Pi.
  * Connect a FTDI adapter (5V) to the USB port of the Raspberry Pi and FTDI connector on the RPi-ShieldBridge.
  * Device name: */dev/ttyUSB0* or */dev/ttyACM0*

* **UART connection (hardware serial port)**
  * Close the **Jumpers JTX and JRX** on the RPi-ShieldBridge.
  * Put the RPi-ShieldBridge on the Raspberry Pi.
  * [Disable Linux serial console](http://elinux.org/RPi_Serial_Connection#Preventing_Linux_using_the_serial_port).
  * Device name: */dev/ttyAMA0*


## Controlling with Python

* Install [pySerial](http://pyserial.sourceforge.net) and [pyFirmata](https://github.com/tino/pyFirmata) using [pip](http://www.pip-installer.org/en/latest/installing.html):

    ```
    $ sudo apt-get update
    $ sudo apt-get install python-serial
    $ sudo pip install pyfirmata
    ```

* Create a test script named *firmatatest.py*:

    ```
    $ nano firmatatest.py

    import time
    import RPi.GPIO as GPIO
    import pyfirmata
    GPIO.setmode(GPIO.BOARD)
    GPIO.setup(12, GPIO.OUT)   # GPIO18 output
    GPIO.output(12, GPIO.LOW)  # GPIO18 low -> Arduino reset off
    # start connection to Arduino
    #  USB: /dev/ttyUSB0 or /dev/ttyACM0
    #  UART: /dev/ttyAMA0
    board = pyfirmata.Arduino('/dev/ttyUSB0')
    board.digital[13].write(1) # switch on LED
    time.sleep(3)              # 3s delay
    board.digital[13].write(0) # switch off LED
    time.sleep(3)              # 3s delay
    board.exit()
    ```

* Run the script:

    ```
    $ python firmatatest.py
    ```


## Controlling with Node.js (JavaScript)

* Install [Node](http://nodejs.org), [Firmata library](https://npmjs.org/package/firmata) and  [rpio library](https://npmjs.org/package/rpio):

    ```
    $ sudo mkdir /opt/node
    $ wget http://nodejs.org/dist/v0.10.12/node-v0.10.12-linux-arm-pi.tar.gz
    $ tar zxf node-v0.10.12-linux-arm-pi.tar.gz
    $ sudo cp -r node-v0.10.12-linux-arm-pi/* /opt/node
    $ sudo nano /etc/profile

    # add these lines before *export PATH*
    PATH="$PATH:/opt/node/bin"

    $ sudo /opt/node/bin/npm install -g firmata

    $ sudo /opt/node/bin/npm install -g rpio
    ```

* Create a test script named *firmatatest.js*:

    ```
    $ nano firmatatest.js

    var rpio = require('rpio');
    var firmata = require('firmata');

    // GPIO18 low -> Arduino reset off
    rpio.setOutput(12);
    rpio.write(12, rpio.LOW);

    // start connection to Arduino
    //  USB: /dev/ttyUSB0 or /dev/ttyACM0
    //  UART: /dev/ttyAMA0
    var board = new firmata.Board('/dev/ttyUSB0', function(err)
    {
      if(err)
      {
        console.log(err);
        return;
      }
      console.log('connected');
      board.pinMode(13, board.MODES.OUTPUT);
      // switch on LED
      board.digitalWrite(13, board.HIGH);
      // switch off LED after 3s
      setTimeout(function()
      {
        board.digitalWrite(13, board.LOW);
        process.exit(0);
      }, 3000);
    });
    ```

* Run the script:

    ```
    $ sudo node firmatatest.js
    ```
