# hyperion-usbasp

AVR program to turn an [USBASP programmer](http://www.fischl.de/usbasp/) into a WS2801/WS2812 led controller for use with [Hyperion](https://github.com/hyperion-project/hyperion) that is capabale of driving up to 256 leds.

As you will need to reflash the USBASP with this firmware, you will need an atmel AVR programmer. You can use:
* Another usbasp
* just about any Arduino board with the ICSP sketch
* Commercial options such as an Atmel AVR Dragon

## Compiling and flashing
Install prerequisites
```
apt-get install "stuff"
```

Download and compile
```
git clone https://github.com/hyperion-project/hyperion-usbasp/
mkdir build
cd build
cmake ..
make
```

Flashing
```
avrdude -C blah -P blah blah hyperion/hyperion-atmega8.hex
```

## Physical wiring
The WS2801 strip is connected using MOSI and SCK pins.

The WS2811 strip is connected using only the MOSI pin.

![Atmel ICSP pinout](http://www.evilmadscientist.com/images/articles/avrtargetboards_1.jpg)

NOTE! Some chinese USBASP devices dont strictly follow the pinout and you must use PIN 10 for GND.


## hyperion configuration
### WS2801

Use the following device configuration in Hyperion:
```
"device" :
{
  "name"       : "MyDevice",
  "type"       : "hyperion-usbasp-ws2801",
  "colorOrder" : "rgb"
},
```

### WS2812

Use the following device configuration in Hyperion:
```
"device" :
{
  "name"       : "MyDevice",
  "type"       : "hyperion-usbasp-ws2812",
  "colorOrder" : "rgb"
},
```
