## HiSoRange Browser Detect package for Laravel 4.
***
This package is able to identify the visitors browser almost perfectly by useing multiple well tested package and service together. The theory behind it is to use user-agent parser libraries together to get the most accurate result. The packages sets up a standard data schema and fills in the informations generated by plugins, if one of the plugin cannot determine an information like operating system version then there is a good chance the others could.

## Features
***
+ Easily extend or personalize the analization flow.
+ Identify the **browser's family** and it's version.
+ Identify the **operating system**'s software and it's version.
+ Idenfity the **browsing device**'s family and model. 
+ Categorise devices such as **mobile**, **tablet** and **desktop**.
+ Determine the browser's **CSS protokol** support as CSS1, CSS2, CSS3.
+ All **semantic version** is preparsed to major, minor and patch keys.
+ Out of the box **caching**, useing your app cache so you don't need to set up anything.
+ **Plugin oriented** parsers, simple add, remove or manage plugins from the plugin config file.
+ Identify the the **crawlers** aka bots.
+ **Import & export** result into very compact strings which can be stored even in varchar fields.
+ NEW in 1.0.0 check support for JavaScript.

## Installation
First add the package to your composer:
```json
{
    "require": {
        "hisorange/browser-detect": "1.*"
    }
}
```
After the composer update/install add the service provider to your app.php:
```php
'providers' => array(
    // ...
    'hisorange\BrowserDetect\Provider\BrowserDetectService',
    // ...
)
```
Add the alias to the aliases in your app.php:
```php
'aliases' => array(
    // ...
    'BrowserDetect' => 'hisorange\BrowserDetect\Facade\Parser',
)
```
You can use personal configurations just publish the package's configuration files.
```cli
php artisan config:publish hisorange/browser-detect
```
Finaly, enjoy :3

## Built in plugins
***
The package ships with multiple built in plugin and one native api connector to gather the most accurate informations, but you can simple add your own solution by adding a simple line to the plugin config file. Here is the built in plugins list.

+ Browscap based on [GaretJax/phpbrowscap](https://github.com/GaretJax/phpbrowscap) package.
+ UAParser based on [yzalis/UAParser](https://github.com/yzalis/UAParser) package.
+ MobileDetect2 based on [serbanghita/Mobile-Detect](https://github.com/serbanghita/Mobile-Detect) v2.* package.
+ UserAgentStringApi as a native parser for the [useragentstring.com](http://www.useragentstring.com/pages/api.php)'s api.

*As default the UserAgentStringApi is turned off since it requires a remote call and that is barelly supported on shared hosts, also a bit slower. But if you are able to use remote fopen then use it bravely they provide a really accurate informations.*

## Usage
***
The codeing standard trying to be the simpliest as possible. The following examples assume you gona use the *BrowserDetect* class name as alias, but the packages is namespaced so you can name the facade as you wish.
```php
// Detect the current visitor's informations.
$result = BrowserDetect::detect();

// Every parsing will product a standard data schema.
array(
	// Device's kind.
	'isMobile'				=> true, // bool
	'isTablet'				=> false, // bool
	'isDesktop'				=> false, // bool

	// Visitor's purpose.
	'isBot'					=> false, // bool

	// Browsing software.
	'browserFamily'			=> 'Safari', // string
	'browserVersionMajor' 	=> 6,  // integer
	'browserVersionMinor' 	=> 1,  // integer
	'browserVersionPatch' 	=> 2,  // integer

	// Operating software.
	'osFamily' 				=> 'Apple iOS', // string
	'osVersionMajor'		=> 5,  // integer
	'osVersionMinor'		=> 1,  // integer
	'osVersionPatch'		=> 4,  // integer

	// Device's hardware.
	'deviceFamily' 			=> 'Apple', // string
	'deviceModel'  			=> 'iPad', // string
	'mobileGrade'  			=> A, // string

	// Browser's capability.
	'cssVersion'   			=> 3,  // integer

	// Javascript support. (The default value is true because most of the browser supporting
	// the js but only the phpbrowscap plugin can analyze it.)
	'javaScriptSupport'		=> true, // bool
);

// Detect browser informations from user-agent string.
BrowserDetect::detect('Opera/9.80 (Windows NT 6.0) Presto/2.12.388 Version/12.14');

// Import stored informations from compact string.
BrowserDetect::importFromString('1||||Chrome|18||1025|AndroidOS|4|1|2||NexusTablet|A|3');

// Export to compact string.
BrowserDetect::detect()->toString();

// The result also implements the ArrayIterator so can use like this.
foreach(BrowserDetect::detect() as $key => $value) {
	echo 'Your '.$key.' is '.var_export($value, true).'.<br>';
}
```

## Examples
***
You can get result informations by simply call on the facade. @@@magic happens@@@
```php
// You can always get the result object from the facade if you wish to operate with it.
BrowserDetect::detect(); // Will resolve and return with the 'browser.result' container.

// Calls are mirrored to the result object for easier use.
BrowserDetect::browserVersion(); // return '3.6' string.

// Supporting human readable formats.
BrowserDetect::browserName(); // return 'Firefox 3.6' string.

// Or can be objective.
BrowserDetect::browserFamily(); // return 'Firefox' string.
```

### Parse from stored user-agent header
***
As base the Browser will parse the currently visiting browser's user-agent but you can parse stored agents too.
```php
// Current visitor's user-agent.
BrowserDetect::detect();

// Parse from string.
BrowserDetect::detect('Mozilla/5.0 (X11; U; Linux x86_64; en-ca) AppleWebKit/531.2+ (KHTML, like Gecko) Version/5.0 Safari/531.2+');
```
### Categoriesed devices
***
The script categorise the result into 3 device category such as *Desktop*, *Tablet* and *Phone* also there is a fourth category but it's rather about the purpose of the visit and that is the *isBot*, this is for crawlers like the Googlebot.

```php
// Is the device a mobile?
BrowserDetect::isMobile();

// Is the device a tablet?
BrowserDetect::isTablet();

// Is the device a desktop computer?
BrowserDetect::isDesktop();

// Is this a bot visit?
BrowserDetect::isBot();
``` 

### Browser software informations
***
```php
// Get the browser family.
BrowserDetect::browserFamily(); // return 'Internet Explore', 'Firefox', 'Googlebot', 'Chrome'...

// Get the browser version.
BrowserDetect::browserVersionMajor(); // return '5' integer.
BrowserDetect::browserVersionMinor(); // return '2' integer.
BrowserDetect::browserVersionPatch(); // return '0' integer.

// Get the human friendly version.
BrowserDetect::browserVersion(); // return '5.2' string, cuts the unecessary .0 or .0.0 from the end of the version.

// Even more love for humans..
BrowserDetect::browserName(); // return 'Internet Explorer 10' string, merges the family and it's version.
```

### Operating system informations
***
```php
// Get the os family.
BrowserDetect::osFamily(); // return 'Windows' string.

// Get the browser version.
BrowserDetect::osVersionMajor(); // return 'XP' string or integer if the os uses semantic versioning like Android OS.
BrowserDetect::osVersionMinor(); // return '0' integer.
BrowserDetect::osVersionPatch(); // return '0' integer.

// Get the human friendly version.
BrowserDetect::osVersion(); // return '2.3.6' string, for Android OS 2.3.6
BrowserDetect::osVersion(); // return '8' integer, for Windows 8
BrowserDetect::osVersion(); // return 'Vista' string, for Windows Vista
BrowserDetect::osVersion(); // return 'XP' string, for Windows XP

// Even more love for humans..
BrowserDetect::osName(); // return 'Windows 7' string, merges the family and it's version.
```

### Device informations
***
The script can determine the device family e.g: 'Apple' and the device model e.g: 'iPhone'. This function only works with tablet and mobile devices yet, since the request to the server do not containing informations about the desktop computers.

```php
// Get device family.
BrowserDetect::deviceFamily(); // return 'Apple' string. Nokia, Samsung, BlackBerry, etc...

// Get device model.
BrowserDetect::deviceModel(); // return 'iPad' string. iPhone, Nexus, etc..
```

### CSS version support
***
The Browscap plugin can determine many browser's css version support.

```php
// Get the supported css version.
BrowserDetect::cssVersion(); // return '3' integer. Possible values null = unknown, 1, 2, 3
```

### Mobile grades
***
The MobileDetect* plugin can define a device category. For desktop computers this value will be null.
```php
// Get a tablet's or phone's mobile grade in scale of A,B,C.
BrowserDetect::mobileGrade(); // returns 'A'. Values are always capitalized.
```

### Internet Explorer versions
***
The package contains some helper functions to determine if the browser is an IE and it's version is equal or lower to the setted, those functions are defined on the result object.

```php
// Determine if the browser is an Internet Explorer?
BrowserDetect::isIE(); // return 'true'.

// Send out alert for old IE browsers.
if (BrowserDetect::isIEVersion(6)) {
    echo 'Your browser is a shit, watch the Jersey Shore instead of this page...';
}

// true for IE 6 only.
BrowserDetect::isIEVersion(6); 

// true for IE 9 or lower.
BrowserDetect::isIEVersion(9, true);
```
### Export & Import results
***
You can export & import the result object informations into a simple array or a compact string. This function useful when you wish to store a user agent info in a database field, the compact string format will only contain the result values so it can be between 30-200 chr which fits perfectly in your database char field.

```php
// Export result object to a compact string.
BrowserDetect::toString(); // Will produce '1|0|0|0|Safari|5|0|2|iOS|4|3|2|Apple|iPhone|3|A'
// Or...
echo BrowserDetect::detect();

// Import result from a compact string.
BrowserDetect::importFromString('1|0|0|0|Safari|5|0|2|iOS|4|3|2|Apple|iPhone|3|A'); // Will generate a normal result object.

// Export result object to an array.
$infoArray = BrowserDetect::toArray(); // Will produce a simple array with the result object data values.

// Import result from an array.
// if you pass a numeric keyed array to the function that will 
// sniff it out and combine the schema keys and the imported data values to the object.
BrowserDetect::importFromArray($infoArray); // Will revert every informations.

// Also there is a base function which can sniff the
// passed argument's type an call the right function for it.
BrowserDetect::import('Can be string or an array here.');
```

### Results
***
The package uses the Laravel framework's awesome IoC with this you can inject your own solutions.
```php
// Make the parser from it's container.
App::make('browser-detect.parser')->detect();

// Make an empty result object from the container.
App::make('browser-detect.result')
	->import('|1|||Safari|7|2|1|BlackBerryOS||||BlackBerry|BlackBerryTablet|A|');

// Inject your own result object or parser.
App::bind('browser-detect.result', 'My\Custom\Result');
App::bind('browser-detect.parser', 'My\Custom\Parser\Extended\To\The\Original\Or\Not');
```

### Performance and cacheing
***
On my test server without cacheing the first analization took 40ms and 3-4ms each different agent after that. With cache the first analization took 3ms and 0,1-1ms each different agent after that.

*Those result are made without the UserAgentStringApi plugin, since that requires a remote call.*

The package useing your running app's cache so you don't have to set up anything ;) Also it uses a really really small footprint with the compact string solution.

Cacheing in theory: the results are converted into an unindexed array in the standard schema's order, then remove the empty values like 0, false, null to save the unecessary zeros and that string will be imported & exported from / to cache. When the script imports a result from a string the result object uses the standard schema and converts the value types back.

### Configurations, plugin managment.
***
To access the configurations use the 'browser' hint. You can costumize everything! Howsdat? just open the config.php :P But for real:

+ Generic values for cases where the user agent string invalid or not provided.
	+ Set your custom OS name for cases where it's not recognizable.
	+ Set your custom Browser family for cases where the user agent is invalid.
	+ Set fallback user agent for calls like CLI or CURL.
+ Cacheing
	+ Set cacheing expiration.
	+ Set key prefix to avoid conflict.
+ Plugins are stupidly simple, it works as PluginClassName => PluginConfig.

Worth to mention you can costumize the plugins which allows you to interact with the original packages such as Browscap without hardcoding anything.

### Precached browscap.
***
The PHP Browscap requires a browscap.ini and it's cached format but it does not refresh too often so the package already containing a downloaded and parsed browscap.ini in the *cache/Browscap* directory and the autoupdate is turned off by default, but you can easly update your ini by turning the *doAutoUpdate* value to *true*.

# Changelog

### Changes in 1.0.0pre
***
The code has been almost totaly rewrited except like 30 line of code from v0.9.*, this breaks the compability with older versions so the major version has been increased to v1.0.0pre.

The version 1.0.0 is promised when the Mobile Detect 3 comes out but since they passed the due date for the release the support for their new detector will be intruduced in a plugin so the package dev can move on.

- Most prior change is the PHP requirement increased to 5.4~ this allows the usage of traits.
- Class loading now uses PSR-4 instead of PSR-0 structure. This will be handled by the composer automaticaly.
- Package now requires the hisorange/traits package to share resources between packages.
- PHP namespace are moved from **hisorange\browserdetect** to **hisorange\BrowserDetect** to avoid collusions.
- Package now uses the 'browser-detect.parser', 'browser-detect.result' component names in the L4 Di.
- Service provider is more extendable with splitted parser and result component keys.
- Manager class has been renamed to Parser.
- Instead of useing the basic Cache and Config class from the Laravel app now useing the app's Di to forge the needed component.
- Most of the Manager class' functions has been renamed and reoriented in the Parser.
- Before hardcoded generic values now stored in the config file.
- Default cache prefix has been changed to 'hbd1'.
- Cacheing now requires less memory the results are stored in a compact string format instead of an array.
- Parser now determine the browser's javascript support.
- Parsing are now plugin oriented instead of hardcodeing.
- Plugins are costumizeable from the config/plugins.php file.
- Package ships with 4 built in plugin.
- UserAgentStringApi plugin is default turned off, because it requires greater amount of time to process.

### v0.9.2
- Fix the case where importing datas and query the current agent in the same request.
- Perform self analization before importing data.

### v0.9.1
- New import and export function on the info object.

### Initial release v0.9.0