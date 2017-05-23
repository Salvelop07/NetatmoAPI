<img align="right" src="/readmeAssets/CameraAPI.jpg" width="150">

# php Netatmo Camera API

## This API allow full control of your Netatmo Presence/Welcome cameras

The following operations are currently supported:

**Presence Cameras:**

- Get and Set Camera Monitoring (on, off).
- Get and Set Camera floodlight intensity.
- Get and Set Camera floodlight mode (auto, on, off).
- Get and Set Camera floodlight auto mode (always, people, cars, animals, other).
- Get and Set Camera Smart-Zones.
- Get and Set alerts and recording (ignore, record, record and notify) for humans, vehicle, animal, other.
- Get and Set alerts hours (from, to).
- Get outdoor events (filtered by type or not).
- Get camera(s) status.

**Welcome Cameras:**

- Get and Set Camera Monitoring (on, off).
- Get and Set persons at home / empty home.
- Get person status.
- Get and Set alerts and recording for persons, unknown faces, motion.
- Get and Set alerts for alarm.
- Set known person gone after time.
- Get indoor events (filtered by type or not).
- Get camera(s) status.

[Warning](#warning)<br />
[How to](#how-to)<br />
[Get settings](#get-settings)<br />
[Change settings](#change-settings)<br />
[IFTTT](#ifttt)<br />
[Version history](#version-history)<br />

*This isn't an official API | USE AT YOUR OWN RISK!
This API is reverse-engineered, provided for research and development for interoperability.*

Feel free to submit an issue or pull request to add more.

[&#8657;](#php-netatmo-camera-api)
<img align="right" src="/readmeAssets/requirements.jpg" width="48">

## Warning

This API will connect directly to your Netatmo account. Regarding your account option, each connection (script execution) may send you an email alert telling that someone connected to your account!

To avoid this:

- On the web interface, go to user settings (top right).
- Go to e-mail settings.
- Turn off e-mail on new connection.

Or:

- Create a new account on Netatmo website.
- Disable connection notification for this new account.
- From your original account, invite the new account.
- Use this second account for the API!

[&#8657;](#php-netatmo-camera-api)
<img align="right" src="/readmeAssets/howto.jpg" width="48">

## How-to

- Download the class/NetatmoCameraAPI.php on your server.
- Include it in your script.
- Start it with your Netatmo account login and password.

#### Connection

```php
require($_SERVER['DOCUMENT_ROOT']."/path/to/NetatmoCameraAPI.php");
$_NAcams = new NetatmoCameraAPI($Netatmo_user, $Netatmo_pass);
if (isset($_NAcams->error)) die($_NAcams->error);
```

If you have several houses on your Netatmo account, you can specify home name:
```php
require($_SERVER['DOCUMENT_ROOT']."/path/to/NetatmoCameraAPI.php");
$_NAcams = new NetatmoCameraAPI($Netatmo_user, $Netatmo_pass, 'myTinyHouse');
if (isset($_NAcams->error)) die($_NAcams->error);
```

[&#8657;](#php-netatmo-camera-api)
<img align="right" src="/readmeAssets/read.jpg" width="48">

#### Get settings:
*Change camera name by yours!*

```php
//get home settings, with alert/recording settings:
$home = $_NAcams->getHome();
echo "<pre>home:<br>".json_encode($home, JSON_PRETTY_PRINT)."</pre><br>";

//list your cameras (name, status, etc.):
$cameras = $_NAcams->getCameras();
echo "<pre>cameras:<br>".json_encode($cameras, JSON_PRETTY_PRINT)."</pre><br>";

//get camera settings:
$settings = $_NAcams->getCameraSettings('myCamera');
echo "<pre>settings:<br>".json_encode($settings, JSON_PRETTY_PRINT)."</pre><br>";
//for Presence you can check:
echo $settings ['light_mode_status'].'<br>';
echo $settings ['light']['intensity'].'<br>';

//get Presence smart zones:
$smartZones = $_NAcams->getSmartZones('myCamera');
echo "<pre>smartZones:<br>".json_encode($smartZones, JSON_PRETTY_PRINT)."</pre><br>";

//get last 10 outdoors events (from Presence) of all type.
//You can request All, or only human, animal, vehicle, movement
$answer = $_NAcams->getOutdoorEvents('All', $num=10);
echo "<pre>answer:<br>".json_encode($answer, JSON_PRETTY_PRINT)."</pre><br>";

//get Presence camera timelapse. It will return filename of the mp4 timelapse, located in API folder:
//for this function to work, the API folder needs write access
$timelapse = $_NAcams->getTimeLapse();
echo $timelapse;

//get last 10 indoors events (from Welcome) of all type.
$answer = $_NAcams->getIndoorEvents($num=10);
echo "<pre>answer:<br>".json_encode($answer, JSON_PRETTY_PRINT)."</pre><br>";

//get John settings:
$john = $_NAcams->getPerson('John');
echo "<pre>john :<br>".json_encode($john, JSON_PRETTY_PRINT)."</pre><br>";

//who is at home:
$athome = $_NAcams->getPersonsAtHome('John');
echo "<pre>athome :<br>".json_encode($athome , JSON_PRETTY_PRINT)."</pre><br>";

//is home empty:
$empty = $_NAcams->isHomeEmpty()
```

[&#8657;](#php-netatmo-camera-api)
<img align="right" src="/readmeAssets/set.jpg" width="48">

#### Change settings:
*Change camera name by yours!*

```php
//SET Presence monitoring on/off
$monitoring = $_NAcams->setMonitoring("myCamera", "on");
echo "<pre>monitoring:<br>".json_encode($monitoring, JSON_PRETTY_PRINT)."</pre><br>";

//SET Presence floodlight mode (auto, on, off):
$floodlight = $_NAcams->setLightMode("myCamera", "auto");
echo "<pre>floodlight:<br>".json_encode($floodlight, JSON_PRETTY_PRINT)."</pre><br>";

//SET Presence floodlight intensity:
$_NAcams->setLightIntensity("myCamera", 100);

//SET when Presence floodlight should turn on in auto mode:
//in order: always, person, vehicle, animal, movement
$lightAutoMode= $_NAcams->setLightAutoMode("myCamera", false, true, false, false, true);
echo "<pre>lightAutoMode:<br>".json_encode($lightAutoMode, JSON_PRETTY_PRINT)."</pre><br>";

//SET Presence alert level for human detection:
//0: ignore, 1: record, 2: record and notify
$alert = $_NAcams->setHumanOutAlert(1);
echo "<pre>alert:<br>".json_encode($alert, JSON_PRETTY_PRINT)."</pre><br>";
//and:
$alert = $_NAcams->setAnimalOutAlert(1);
$alert = $_NAcams->setVehicleOutAlert(1);
$alert = $_NAcams->setOtherOutAlert(1);

//SET Presence alert time from 10h15 (always use hh:mm)
$alert = $_NAcams->setOutAlertFrom('10:15');
echo "<pre>alert:<br>".json_encode($alert, JSON_PRETTY_PRINT)."</pre><br>";

//SET Presence alert time to 22h00
$alert = $_NAcams->setOutAlertTo('22:00');

/*SET Presence SmartZones:
- Define zones as array(x, y, width, height)
- Send at least one zone
- The API check for overlapping zones and don't send them to camera if so.
*/
$zone1 = array(0, 140, 455, 938);
$zone2 = array(457, 99, 880, 979);
$zone3 = array(1339, 569, 545, 509);
$zone4 = array(1339, 158, 580, 409);
$smartZones = $_NAcams->setSmartZones('myCamera', $zone1, $zone2, $zone3, $zone4);
echo "<pre>smartZones:<br>".json_encode($smartZones, JSON_PRETTY_PRINT)."</pre><br>";

//___Welcome:

//set John away from home:
$_NAcams->setPersonAway('John');

//set someone away from home after 120mins:
$_NAcams->setAwayHomeAfter(120);

//set home empty:
$_NAcams->setHomeEmpty();

//set Welcome alerts for unknown faces ('nobody' or 'always'):
$_NAcams->setUnknownFacesInAlert('nobody');

//set Welcome alerts for motion detection ('never', 'nobody', 'always'):
$_NAcams->setMotionInAlert('nobody');

//set Welcome recording for motion detection ('never', 'nobody', 'always'):
$_NAcams->setMotionInRecord('nobody');

//set Welcome alert when someone known is seen ('never', 'arrive'):
$_NAcams->setPersonInRecord('John', 'arrive');

//set Welcome alerts when someone known is seen (0 for disable, 1 for enable):
$_NAcams->setPersonArriveAlert('John', 0);

//set time for John alerts (always use 'hh:mm'):
$_NAcams->setPersonInAlertFromTo('John', '00:00', '23:59');

//set Welcome alarm detection ('never', 'nobody', 'always'):
$_NAcams->setAlarmInDetected('nobody');
```

[&#8657;](#php-netatmo-camera-api)
<img align="right" src="/readmeAssets/IF.jpg" width="48">

## IFTTT

You can create an endpoint url for triggering changes from IFTTT.

Basically, you create a php script that will get url parameters and trigger actions regarding these parameters. So in IFTTT, you can trigger same script with different parameters.

See IFTTTactions.php as an example.

[&#8657;](#php-netatmo-camera-api)
<img align="right" src="/readmeAssets/changes.jpg" width="48">

## Version history

#### v1.0 (2017-05-25)
- First public version.

[&#8657;](#php-netatmo-camera-api)
<img align="right" src="/readmeAssets/mit.jpg" width="48">

## License

The MIT License (MIT)

Copyright (c) 2017 KiboOst

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

