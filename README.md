#Pixel Clock (RGBPongClock) app for use with RGB Pixel Clock Kit

**Adapted for Particle Core by Paul Kourany, April 2015**
- Updated for Particle Photon/Electron and v4 RGB Matrix Panel Shield, May 2016

Andrew Holmes @pongclock

Inspired by, and shamelessly derived from [Nick's LED Projects](https://123led.wordpress.com/about/)

Videos of the clock in action:

https://vine.co/v/hwML6OJrBPw

https://vine.co/v/hgKWh1KzEU0

https://vine.co/v/hgKz5V0jrFn


Particle devices found here:
https://www.particle.io/

Uses an Adafruit 16x32 or 32x32 RGB matrix panel

Can optionally use [this microphone](http://www.adafruit.com/products/1063)

RGBPongClock
------------
This is an adaptation of Andrew Holmes (mostly complete) RGBPongClock for Arduino
using the Particle Core, Photon or Electron.  

The adaptation uses a Cloud Webhook to request a 7-day forecast from api.openweathermap.org
and, using the webhook's JSON parsing feature, receives a temperature and a weather condition
ID for each of the 7 days.  Without parsing, the return payload is typically about 2KB but
with parsing done by the webhook, the return payload is less than 100 bytes!

Some functions such as birthday, Halloween and Christmas display were removed in order for the
code to fit in the original Core flash.  The clock setting code was also removed since Particle devices can sync time to
the Particle cloud.  The Spectrum clock was modified to use fix_fft(*) an 8-bit "lite" fft library with
less RAM and code requirements.  Finally, a Particle.function() was created to allow setting of
specific clock modes since the mode will rotate at 5 minute intervals (primarily for demo purposes)

(*) Google "fix_fft" to get more details on this library 

Webhook
-------
In order for RGBPongClock to get weather from api.openweathermap.org without doing all the
data parsing in code, a JSON parsing webhook is used:

```
{
"event": "uniqueHookName",
"url": "http://api.openweathermap.org/data/2.5/forecast/daily",
"requestType": "POST",
"headers": null,
"query": {
	"q": "{{mycity}}",
	"mode": "json",
	"units": "{{units}}",
	"cnt": 7,
	"appid": "{{apikey}}"
	},
"responseTemplate": "{{#list}}{{temp.day}}{{#weather}}~{{id}}~{{/weather}}{{/list}}",
"json": null,
"auth": null,
"noDefaults": true,
"devideid": null,
"mydevices": true
}
```

The webhook file is provided (weather.json)

CONFIGURING THE WEBHOOK
--------------
The RGBPongClock.ino file contains a some #defines to configure the webhook and other details necessary
for using openweathermap:

`#define HOOK_RESP` defines the "event" name of the webhook.  It must match the "event" field in the
webhook JSON file.

`#define HOOK_PUB` defines the webhook subscription details.  The name after the `hook-response/` part be
the same as the one defined in `#define HOOK_RESP`.

`#define DEFAULT_CITY` define the default city to get weather for.  The "Ottawa,ON" part may be changed
as required (city, state)

`#define API_KEY' defines your openweathermap.org API key

`#define UNITS`	define metric or imperial (US) units

Sample defines:
```
 #define HOOK_RESP		"hook-response/uniqueHookName"
 #define HOOK_PUB		"uniqueHookName"
 #define DEFAULT_CITY	"\"mycity\":\"Ottawa,ON\""	// Change to desired default city,state
 #define API_KEY		"\"apikey\":\"xxxxxxxxxxxxxxxxxxxxxxxxxxx\""  //your key instead of x's
 #define UNITS			"\"units\":\"metric\""		// Change to "imperial" for farenheit units
```
**NOTE**: openWeatherMap now requires an API KEY to be specified.  This key is available on
their site and is free depending on usage.

The JSON parsing template is defined by `responseTemplate` following the Mustache
stateless query format.


DON'T FORGET to create the webhook using Particle CLI or the Particle Dashboard webhook interface:

```  particle webhook create weather.json```  (or whatever you named your webhook file)

  
OTHER LIBRARIES
---------------

RGBPongClock makes use of the following libraries:
```
  RGBMatrixPanel (including SparkIntervalTimer)
  Adafruit_GFX library
  fix_fft
```
