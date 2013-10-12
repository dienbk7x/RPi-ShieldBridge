# I2C Test

A small guide to show the communication via I2C between the Arduino (RPi-ShieldBridge) and Raspberry Pi.

The I2C pins of the Raspberry Pi are connected to the AVR and the Arduino Shield connectors (SDA+SCL) on the RPi-ShieldBridge.


## Prepare Arduino / RPi-ShieldBridge

Upload the following Sktech:

```
#include <Wire.h> 

#define ADDRESS 0x30

void setup() 
{
  pinMode(13, OUTPUT);     // set pin 13 as output (LED connected)
  digitalWrite(13, LOW);   // set pin 13 to low (0V)

  Wire.begin(ADDRESS);     // join I2C bus with respective address
  Wire.onReceive(receive); // receive data
  Wire.onRequest(request); // send data
} 

void loop() 
{
  // do nothing
} 

void receive(int numBytes) // function that runs when data is received
{
  unsigned char c;

  while(Wire.available())
  { 
    c = Wire.read();
    if(c == 0x00)      // LED off, if byte equals 0
    {
      digitalWrite(13, LOW);
    }
    else if(c == 0x01) // LED on, if byte equals 1
    {
      digitalWrite(13, HIGH);
    }
  }
}

void request() // function that runs when data is requested
{
  Wire.write(0xAA); // send 0xAA
}
```


## Prepare Raspberry Pi

* Activate I2C:

    ```
    $ sudo modprobe i2c_bcm2708 baudrate=100000
    $ sudo modprobe i2c-dev
    ```

* Install i2c-tools and python-smbus:

    ```
    $ sudo apt-get update
    $ sudo apt-get install i2c-tools
    $ sudo apt-get install python-smbus
    ```

* Test I2C bus:

    ```
    $ sudo i2cdetect -y 1
    ```

* Note: As default the reset pin of the Arduino is connected to GPIO18 (Pin 12) of the Raspberry Pi (GPIO18 high = reset on, GPIO18 low = reset off). To disable the reset:

    ```
    $ sudo -i
    $ echo "18" > /sys/class/gpio/export
    $ echo "out" > /sys/class/gpio/gpio18/direction
    $ echo "0" > /sys/class/gpio/gpio18/value
    $ exit
    ```

    Or if you have installed [WiringPi](http://wiringpi.com/):

    ```
    gpio -g mode 18 out
    gpio -g write 18 1
    ```

### Shell

* Switch on LED:

    ```
    $ sudo i2cset -y 1 0x30 0x01
    ```

* Switch off LED:

    ```
    $ sudo i2cset -y 1 0x30 0x00
    ```

### Python

* Create a test script named *i2ctest.py*:

    ```
    $ nano i2ctest.py

    import time
    import smbus
    bus = smbus.SMBus(1)
    addr = 0x30
    
    while True:
        bus.write_byte(addr, 0x01)
        print "on"
        time.sleep(1)
        bus.write_byte(addr, 0x00)
        print "off"
        time.sleep(1)
    ```

* Run the script:

    ```
    $ sudo python i2ctest.py
    ```
