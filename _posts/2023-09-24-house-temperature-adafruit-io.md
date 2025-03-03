---
layout: post
title: House Temperature Logging
subtitle:
author: K. E. Claytor
tags: whippersnapper, microcontroller, esp32, sensing
hero_image:
---

I was interested in temperature, humidity monitoring around the house to see if there were any trends, or if the basement was getting too humid.
In particular, looking for a low-effort way to get data into a dashboard, I was eager to try out the [AdafruitIO](https://io.adafruit.com/) framework.
They limit you to two managed whippersnapper devices (but you can have more using your IO key), and 10 feeds (this is the real limiter, with each stream of data being one feed).

## Wippersnapper (~1 hr from Zero)

Spent a few minutes installing the drivers.

After installing the drivers, query for devices, erase the flash, and install the wippersnapper flash:
```bash
ls /dev/cu.* 
esptool.py --port /dev/cu.SLAB_USBtoUART erase_flash
esptool.py --port /dev/cu.SLAB_USBtoUART write_flash 0x0 wippersnapper.feather-esp8266.1.0.0-beta.72.bin
```

It appears in their UI right away, adding the MCP9808 was trivially easy, and it started reporting data right away.

One big downside is the lack of components.
For instance, I have the TSL2561 Lux sensor, but only the TSL25**9**1 is supported.
Similarly, I have the VL53L1X, but only the VL53L**0**X is supported.
This includes the development devices (currently).

## Arduino (~2hr from Zero)

Took about about an hour configuring the ardunio boards manager (finding the ESP32 was the hardest).

`config.h`
```c
#define IO_USERNAME   "<your adafruit io username"
#define IO_KEY        "<your adafruit io key"
#define IO_FEED       "esp8266.downstairs"
#define IO_SLEEP_MS   5*60*1000

#define WIFI_SSID   "<ssid>"
#define WIFI_PASS   "<password>"

#include "AdafruitIO_WiFi.h"
AdafruitIO_WiFi io(IO_USERNAME, IO_KEY, WIFI_SSID, WIFI_PASS);
```

A note on the `IO_FEED` name, this points to the "key" in the feed, which is generated when you generate the feed name, it is typically an "API-safe" version of the group.name that you configured when creating the feed.

`main.c`
```c
/**************************************************************************/
/* Log MCP9808 data to Adafruit IO */
/**************************************************************************/

#include "config.h"
#include <Wire.h>
#include "Adafruit_MCP9808.h"

// Create the MCP9808 temperature sensor object
Adafruit_MCP9808 tempsensor = Adafruit_MCP9808();

// set up the 'temperature' and 'humidity' feeds
AdafruitIO_Feed *temperature = io.feed(IO_FEED);

void setup() {
  
  // start the serial connection
  Serial.begin(115200);
  
  // wait for serial monitor to open
  while(! Serial);
  
  // Connect to the MCP9808
  if (!tempsensor.begin(0x18)) {
    Serial.println("Couldn't find MCP9808!");
    while (1);
  }
   Serial.println("Found MCP9808!");
  tempsensor.setResolution(3);
  
  // connect to io.adafruit.com
  Serial.print("Connecting to Adafruit IO");
  io.connect();
  
  // wait for a connection
  while(io.status() < AIO_CONNECTED) {
    Serial.print(".");
    delay(500);
  }
  
  // we are connected
  Serial.println();
  Serial.println(io.statusText());

}

void loop() {
  // io.run(); is required for all sketches.
  // it should always be present at the top of your loop
  // function. it keeps the client connected to
  // io.adafruit.com, and processes any incoming data.
  io.run();
  // wake up, and take a measurement
  tempsensor.wake();
  float fahrenheit = tempsensor.readTempF();
  // Log to the serial
  Serial.print("Temp: "); 
  Serial.print(fahrenheit, 4);
  Serial.println("*F.");
  // save fahrenheit (or celsius) to Adafruit IO
  temperature->save(fahrenheit);
  // shutdown MSP9808 - power consumption ~0.1 mikro Ampere
  tempsensor.shutdown_wake(1);
  // Delay until the next cycle
  delay(IO_SLEEP_MS);
}
```

## ESP32

I have the ESP32-WROOM-32 board.
Unfortunately, I was not able to find the Ardunio library for this board.
Similarly, I only found the `-v2` circuit python implementation, which I was able to flash to the board;
```bash
esptool.py --chip esp32 --port /dev/cu.SLAB_USBtoUART erase_flash
esptool.py --port /dev/cu.SLAB_USBtoUART write_flash -z 0x0 adafruit-circuitpython-adafruit_feather_esp32s2-en_US-8.2.6.bin
```
I was able to connect to the REPL via [Mu](https://codewith.mu/), but was just getting a stream of errors.

Eventually I just went back to the wippersnapper implementation, which was easy to flash with the `--chip esp32` option.
```bash
esptool.py --chip esp32 --port /dev/cu.SLAB_USBtoUART erase_flash
esptool.py --chip esp32 --port /dev/cu.SLAB_USBtoUART write_flash 0x0 wippersnapper.feather-esp32.1.0.0-beta.72.bin
```

I think it will be easier to use a circuitpython board that appears as a drive, and then air-lift it into wifi.


## MagTag

Setup the MagTag light sensor with the rest of the house dashboard.
Setup the buttons to also pull in data.
Setup the neopixel to display colors.

Linked the buttons to the neopixel by creating an action from the button (any new data) to publish to the neopxiel feed a value.
HINT: Bump down the limit to every 10s for a more reactive button pressing activity.
