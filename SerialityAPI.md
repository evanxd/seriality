# Seriality API #

The Seriality API is modeled lightly off of the serial libraries included with [Processing](http://processing.org/reference/libraries/serial) and [Wiring](http://wiring.org.co/reference/libraries/Serial/index.html)/[Arduino](http://arduino.cc/en/Reference/Serial) with some alterations to make it friendlier for Javascript. This documentation covers the first version of the Seriality API (see the `apiVersion` property).


---


## Seriality() ##

The constructor must be called to create an instance of Seriality by which all other functions can be called. This is done by referencing the DOM element which contains the plug-in object. The constructor expects no parameters. <font color='#999999'>Available since API v1.0</font>

```
var serial = (document.getElementById("seriality")).Seriality();
...
<object type="application/Seriality" id="seriality" width="0" height="0"></object>
```

## begin(port, speed) ##

The `begin()` function attempts to begin serial communications on the specified port at the specified speed. Valid ports are held in the `ports` array. Valid speeds are `1200`, `1800`, `2400`, `4800`, `9600`, `19200`, `38400`, `57600`, `115200`, and `230400`. When any other number is provided, the speed will default to `9600`. A boolean `true` is returned when the port was opened successfully; a boolean `false` is returned otherwise. <font color='#999999'>As of API v1.1 the <code>speed</code> parameter is optional with a default value of <code>9600</code>. Available since API v1.0</font>

```
serial.begin("/dev/tty.usbserial-A9082r0E", 9600);
```

## end() ##

The `end()` function ends serial communications by closing the port that was opened by the `begin()` function. It expects nothing and returns nothing; `end()` doesn't mess around. <font color='#999999'>Available since API v1.0</font>

```
serial.end();
```

## read() ##

When successful, the `read()` function returns a string of length 1 created from a single char read from serial. When unsuccessful, it returns the empty string `""`. <font color='#999999'>Available since API v1.0</font>

```
if (serial.available()) {
  if ("x" == serial.read()) {...}
}
```

## readByte() ##

The `readByte()` function differs from the `read()` function in that it returns an integer number rather than a string. This is intended to obviate the need for type conversion when using hexadecimal notation. <font color='#999999'>Available since API v1.0</font>

```
if (serial.available()) {
  if (0xF3 == serial.readByte()) {...}
}
```

## readLine() ##

To complement the use of `Serial.println()` in Wiring/Arduino, the `readLine()` function returns a string containing all characters preceding a newline `\n`. Leading and trailing newlines and carriage returns are omitted from the return value. <font color='#999999'>As of API v1.2 <code>readLine()</code> only returns on <code>\n</code>, it also returned on <code>\r</code> in previous versions. Available since API v1.0</font>

```
var message;
if (serial.available()) {
  message = serial.readLine();
}
```

## readJSON() ##

To help with protocol creation and to ease difficulties related to type conversion, support for JSON is also supported. The `readJSON()` function expects a sequence of characters beginning with a `{`. It returns all characters received as a string when an equal number of opening and closing braces are received. Any further validation is expected to be handled in Javascript. This function returns an empty string `""` on error. <font color='#999999'>Available since API v1.0</font>

```
var json;
var data;
if (serial.available()) {
  json = serial.readJSON();
  data = eval('(' + json + ')');
  if (undefined != data) {...}
}
```

## readUntil(byte) ##

For specialized protocols and sensor interfacing, `readUntil()` returns a string containing all characters preceding the given byte. This function is intended for use with hexadecimal notation. <font color='#999999'>Available since API v1.2</font>

```
var data = '';
var debug = '';
if (serial.available()) {
  data = serial.readUntil(0x03);
  for (var i = 0; i < data.length; ++i) {
    debug += data.charCodeAt(i).toString(16) + ' ';
  }
  alert(debug);
}
```

## write(message) ##

The `write()` function is a versatile function for writing almost anything over the serial port. It can handle strings, integers, floating point numbers, and any other variable you supply via its built in string conversion function. It returns boolean `true` on success and boolean `false` on failure. <font color='#999999'>Available since API v1.0</font>

```
serial.write("Hello World");
serial.write(42);
serial.write(1.4142);
serial.write(Math.SQRT2);
```

## writeByte(byte) ##

The `writeByte()` function attempts to convert the supplied parameter to a single byte before writing. To complement `readByte()` it is also intended for use with hexadecimal notation. <font color='#999999'>Available since API v1.0</font>

```
serial.writeByte(0xA2);
```

## available() ##

The `available()` function returns an integer representing the number of bytes available for reading. This function should be used as a conditional around any and all read functions. <font color='#999999'>Available since API v1.0</font>

```
var message;
if (serial.available()) {
  message = serial.readLine();
}
```

## ports ##

The `ports` property is an array that contains all known serial ports of the client machine. The items in the `ports` array are suitable for use with the `begin()` function. Due to the popularity of FTDI and Prolific usb-serial chips, any ports containing the substring 'usbserial' will appear at the front of the array as a convenience on UNIX platforms.<font color='#999999'>Available since API v1.0</font>

```
serial.begin(serial.ports[0], 9600);
```

## refreshPorts() ##

To allow for pages that instruct the user to plug in their device after page load, the `refreshPorts()` function refreshes the `ports` array manually. If your device has a unique name, it is possible to call this function repeatedly in the background to auto-detect when it is plugged in. <font color='#999999'>Available since API v1.0</font>

```
alert(serial.ports);
alert("Plug in a device...");
serial.refreshPorts();
alert(serial.ports);
```

## port ##

The `port` property is a string that contains the path to the port that is currently in use. When no port is in use (eg. after a call to the end() function) `port` contains the empty string. <font color='#999999'>Available since API v1.1</font>

```
if (serial.port) {
  alert("Port in use: " + serial.port);
}
else {
  alert("No port in use");  
}
```

## speed ##

The `speed` property is a number that contains the speed of the port in use. When no port is in use (eg. after a call to the end() function) `speed` contains 0. <font color='#999999'>Available since API v1.1</font>

```
alert("Port " + serial.port + " running at " + serial.speed + "bps");
```

## apiVersion ##

The `apiVersion` property is supplied to mitigate the painful process of plug-in version detection. Since this property exists from the inception of the project, it is a reliable way to discern which API features are available for use on the client machine. Difficulties of plug-in detection are reduced to knowing simply whether or not the plug-in exists. Once you know that the plug-in is installed, you can rely on this property to accommodate API feature dependencies as the API matures. <font color='#999999'>Available since API v1.0</font>

```
alert("You have version " + serial.apiVersion + " of the Seriality API");
```

This is a listing of when each API version was incorporated in the downloadable release. This can help with the decision of whether or not to probe for the API version.

  * 1.0
    * Mac OS X - January 10th, 2010
    * Windows - n/a
  * 1.1
    * Mac OS X - January 11th, 2010
    * Windows - n/a
  * 1.2
    * Mac OS X - March 6th, 2010
    * Windows - n/a