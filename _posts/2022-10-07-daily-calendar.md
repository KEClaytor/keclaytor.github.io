---
layout: post
title: Daily E-Ink Calendar
subtitle:
author: K. E. Claytor
tags: laser cutting, e-ink, calendar, 
hero_image: /assets/images/einkcalendar/hero.png
---

This project was inspired by this [magic ink calendar](https://github.com/speedyg0nz/MagInkCal).
I had a need for an easy display to a shared calendar, but only needed the day's events and not a full month display.

1. Google Calendar Apps API
1. HTTPS Redirect
1. JSON Parsing
1. EInk Display
1. ESP8266 Deep Sleep

## Google Apps Calendar API

So Google has a decent API for interfacing with their calendar, but also they have a scripting language that takes out most of the hard work!

[This blog post](https://coertvonk.com/sw/embedded/esp8266-clock-import-events-from-google-calendar-15809) was incredibly helpful in setting up and accessing the Google Calendar integration.
Both on the google script side and on the ESP side.

### Creating the Script

Create a new script at [Google Apps Script](https://script.google.com/home).
My script is in [code.gs](code.gs).

Our script need to:
1. Query upcoming calendar events
1. Parse the events into a format for display
1. Compute the sleep time (so we don't have to do in-depth date and time manipulations on the ESP)
1. Provide the current date (and time)
1. Yield the following in an easy-to-parse format (JSON)

In order to query events, pull up the calendar settings and under "Integrate calendar" you have the `Calendar ID`.

This ID is passed into the Calendar API when querying for events.
For convenience I wanted the events ordered chronologically, which requires `singleEvents` to be `true`;

```js
  var events = Calendar.Events.list(calendarId, {
    timeMin: now.toISOString(),
    timeMax: stop.toISOString(),
    singleEvents: true,
    orderBy: 'startTime',
  });
```

I then loop over the returned events parsing them into a reduced structure for easier display (eg; directly printing strings);

```js
      response.push({
        name: event.summary,  // String
        allday: all_day,      // Bool
        time: time_string,    // String
        duration: duration,   // int, ms
        who: attendees,       // Array
      })
```

I wanted the ESP to deep sleep until the next event finished and then refresh the display.
Instead of figuring out the time on the ESP, I opted to compute it in the google script and return it as another field.

```js
function calcDeepSleep(upcoming) {
  // Compute deep sleep time in milliseconds
  // Conditions are;
  // 1. End time of last event
  // 2. 4 hours
  // 3. Midnight
  // Whichever is smaller
  ...
}
```

Finally, all of this is collected into a dictionary and converted to json;
```js
  var data = {
    "date": today.toDateString(),                       // String
    "time": today.toTimeString(),                       // String
    "items": (events.items ? events.items.length : 0),  // Number
    "events": parsed,                                   // Array
    "sleep": deepSleepTime,                             // int, ms
  };

  // Turn into json
  var response = JSON.stringify(data);

  // Log and return
  return ContentService.createTextOutput(response)
```

> NOTE: I only have room to display two events, so events is always a length-2 array.
But I'd like to know how many events are still on the calendar for the day, so the `items` field keeps track of that.

This is done in the `doGet()` function which is run when a GET request is made for our script.

### Deploying the Script

Now that we have the script, it's time to "Deploy" it.

In the upper right is a "Deploy" button, if this is the initial deployment, select "new" otherwise you can issue a new revision with "manage".
For the new deployment, provide a description, run as yourself, and make it available to anyone.
This establishes a unique URL that when requested will provide the JSON data we developed above.

> NOTE: **Anyone** can hit up this endpoint.
As a result, your calendar data will be exposed to the world, so make sure you're okay exposing this calendar.

If you make changes to your script, you can just "manage" your deployment.
After clicking manage, click the edit (pencil) icon, then click **version --> new version** to deploy your current code.
Provide a description and click deploy.

Test your script at the URL provided and ensure you get the JSON you expect.

## HTTPS Redirect

One issue with using the google script, is that it goes through a number of redirects until it returns the JSON, oh, and also it uses HTTPS.
We have to give the ESP8266 the ability to follow these redirects.
This is done with the [HTTPS Redirect Library](https://github.com/electronicsguy/HTTPSRedirect) allows us to make the https connections required on the ESP 8266.

This was fairly straightforward to use, the examples worked great for me.
I moved my constants into a config.cpp file (which is not tracked by version control) which looks like;

```c
// HTTPSRedirect Configuration file
// Fill in your Wifi network credentials
#ifndef HTTPSREDIRECT_CONFIG
#define HTTPSREDIRECT_CONFIG

const char* ssid = "<NETWORK NAME>";
const char* password = "<NETWORK PASSWORD>;

const char* host = "script.google.com";
const char *GScriptId = "<REALLY LONG GOOGLE SCRIPT DEPLOYMENT ID>";

#endif
```

## Arduino JSON

JSON parsing was made easy using the [ArduinoJson Library](https://arduinojson.org).
There were only a couple of conversion issues that it had problems with.

First, the strings are extracted as `const char*` and not just `char*`, so I had to recast them to manipulate them with `strtok`:
```c
  // Get the value as const char*
  const char* cdate = doc["date"];
  // Recast to a char* we can modify with strtok
  char* date = (char*) cdate;
```

And second, I couldn't pull the deep sleep time out directly as a `uint64_t` (likely because this is not a common arduino datatype) and had to recast from long before extending to microseconds (another reason I sent the time over in milliseconds vs. microseconds):
```c
 uint64_t deepSleepTime = (uint64_t) 10e6;
  ...
    long doctime = doc["sleep"];
    deepSleepTime = (uint64_t) doctime;
    // Convert from ms to us
    deepSleepTime = (deepSleepTime * 1000);
```

## EInk Display

The TriColor EInk display works really well for this project.

I initially had some difficulty because I was relying on the labels for the MOSI/MISO pins and was off by one.
Once I had that worked out the display shone through with really high contrast and good color on the red.

One of the drawbacks of the ESP8266 is the limited number of pins available.
We need 8 pins for almost full control of the eInk display (10 pins for full control).
There are pins 16 broken out on the feather.
Of these 3 are duplicate SPI lines if you're using SPI (we are), 2 are TX/RX for the WiFi module, one is a control pin, and one the deep sleep wake pin - that's 7 pins we can't use.
That leaves *just* enough to drive the display.
If we had an I2C peripheral that would lower the pin count by two, and we'd either have to take a performance hit (time, RAM), or use an I2C pin expander like hthe [MCP23008](https://www.adafruit.com/product/593).
The two SPI lines being tied together is really quite a bummer, as otherwise it would be quite easy to drive the display and have the I2C lines available.

Most of the trouble I had was figuring out these reserved pins.
Quite frequently, the code would crash on the initialization of the of the eInk display when using them and nothing would be sent to the serial monitor, so it was quite frustrating to debug that.

> HINT: if nothing in `setup()` is printing, the error is *before* `setup()`.

### Pin Routing Table

Eink    | Descriptor        | HUZZAH
--------|-------------------|--------
Vin     | Input Voltage     | 3V
3v3     | Regulated 3v out  | NC
GND     | Ground            | GND
SCK     | SPI Clock         | SCK / 14
MISO    | SPI MISO          | MOSI / 13
MOSI    | SPI MOSI          | MISO / 12
ECS     | EInk Chip Select  | 15
D/C     | EInk Data Command | 0
SRCS    | SRAM Chip Select  | 2
SDCS    | SD Chip Select    | 
RST     | EInk Reset        | 5
BUSY    | EInk Busy         | 5
ENA     | EInk Enable       |
N/A     | Deep Sleep        | RST <--> 16


## ESP8266 Deep Sleep

**Don't forget to tie pin 16 (the interrupt from the deep sleep RTC timer) to the reset pin.**
It seems you can do this with just a wire, but there are suggestions to use a Shottkey diode.

> If you want to use the on-board RESET or an external RST signal with the Huzzah, then you should use a Schottky diode for the connection GPIO16 > RST, with the cathode/stripe going to GPIO16 and the anode to RST. If you use a wire or jumper then you're potentially shorting GPIO16 to GND while it's in high output drive, which can eventually damage the high pin drive MOSFET on GPIO16. You *might* get away with a 300-470 ohm resistor in place of the Schottky, although some folks have had Deep Sleep problems as the RST pin might not go low enough to be recognized. 
>
> -- [tech-tx via reddit](https://www.reddit.com/r/esp8266/comments/odttwe/ways_to_increase_deep_sleep_reliability/h44vyqv/?utm_source=reddit&utm_medium=web2x&context=3)

I found the Shottkey diode worked better for me - allowing me to program the device without having to disconnect pin 16 from reset.

Looking into deep sleep time from [ESP8266 Arduino Core](https://arduino-esp8266.readthedocs.io/en/latest/libraries.html#esp-specific-apis)
One can sleep using `ESP.deepSleep(microseconds, mode)` with several modes (see table below).
You can also omit the mode and call `ESP.deepSleep(microseconds)`.

Mode                | Meaning
--------------------|---------
`WAKE_RF_DEFAULT`   | ??
`WAKE_RFCAL`        | ??
`WAKE_NO_RFCAL`     | ??
`WAKE_RF_DISABLED`  | ??

> NOTE: The sleep time is in microseconds (`10^-6s`) and not milliseconds (`10^-3s`) as a typical arduino `delay()` statement would be in, and what the javascript `Date()` differences we return in the googlescript.

The ESP8266 can sleep for at most `ESP.deepSleepMax()` microseconds.
This turns out to be a uint64, which Arduino can't print out natively.
But there's a [library for that](https://github.com/yoursunny/PriUint64/).
Which allowed me to find the max deep sleep on my esp (there seems to be some variability);
```
ESP.deepSleepMax() = 12751208442 = 3.5 hrs
                   = 12322865146 = 3.4 hrs
```

## Battery Monitoring

I'm using a 220kOhm and 1MOhm resistor dividor pair and code [pulled from this tutorial](https://learn.adafruit.com/using-ifttt-with-adafruit-io?view=all) to pull the battery voltage into the **0 - 1.0 V** measurement range of the ESP8266.

## References

- https://arduino-esp8266.readthedocs.io/en/latest/libraries.html#esp-specific-apis
- https://hackaday.io/project/91895-sinclair-scientific-calculator-emulator/log/144392-how-to-design-a-custom-pcb-shape-for-fritzing-no-more-sharp-corners
- https://maker.pro/pcb/tutorial/how-to-get-a-custom-pcb-made
- https://learn.adafruit.com/using-ifttt-with-adafruit-io?view=all
- https://www.seeedstudio.com/fusion_pcb.html

# References
- https://coertvonk.com/sw/embedded/esp8266-clock-import-events-from-google-calendar-15809
- https://www.benlcollins.com/apps-script/google-apps-script-beginner-guide/
- https://github.com/StorageB/Google-Sheets-Logging
- https://github.com/electronicsguy/HTTPSRedirect
- https://maakbaas.com/esp8266-iot-framework/logs/https-requests/
  - https://github.com/maakbaas/esp8266-iot-framework/blob/master/docs/installation-guide.md
  - https://maakbaas.com/esp8266-iot-framework/logs/configuration-manager/
  - https://github.com/maakbaas/esp8266-iot-framework/blob/master/docs/fetch.md
- https://maakbaas.com/linear-calendar-clock/logs/display-modes/
