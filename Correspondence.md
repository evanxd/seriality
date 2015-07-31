# Correspondence #

Knowing that a device is connected to your web page is not enough; when you design a physical interface for public distribution, you have to know that the device connected to your web page is the device you intended.

The [correspondence](http://en.wikipedia.org/wiki/Correspondence_(theology)) of a physical interface to a web page can be verified by asking the microcontroller which page it is intended to be connected to and comparing that information with what the web page knows about itself.

This is a method of verifying that a program resident on a microcontroller corresponds to a particular web page.

## Example ##

This Wiring/Arduino program corresponds to this [test page](http://zambetti.com/projects/seriality/examples/correspondence.html). Visit the page while your board is connected and running the program provided below.

### Wiring/Arduino ###

```
void setup()
{
  Serial.begin(9600);
}

void loop()
{
  char in;
  if (Serial.available()) {
    in = Serial.read();
    // process correspondence request
    if (in == '@') {
      // reply with correspondence identifier
      Serial.print("{'domain':'zambetti.com','path':'/projects/seriality/examples/correspondence.html'}");
    }
  }
}
```

# How it works #

The `verifyPhysicalInterface()` javascript function (in the [test page](http://zambetti.com/projects/seriality/examples/correspondence.html) and at the bottom of this page) issues a correspondence request and processes the resulting correspondence identifier received via the serial port.

## Correspondence Request ##

The '@' character is used to request the correspondence identifier from an attached serial device. A JSON-formatted correspondence identifier is expected in return.

## Correspondence Identifiers ##

There are two types of correspondence
  * **Path correspondence** for path-specific physical interfaces
  * **Domain correspondence** for domain-wide physical interfaces

For a physical interface to be verified as corresponding to a web page, the correspondence identifier must match what is expected by the page.

### domain ###

The `domain` property states which web domain the physical interface corresponds to. It can also contain subdomains (eg: `www.example.com` vs. `example.com`) to be targeted more specifically. The `domain` property is the only required property of a correspondence identifier.

Domain correspondence identifier (Wiring/Arduino)
```
Serial.print("{'domain':'example.com'}");
```

How to verify a physical interface for domain correspondence (Javascript)
```
if (verifyPhysicalInterface() ) {
  alert("Correct physical interface for this domain");
}
```

### path ###

The `path` property states which path the physical interface corresponds to in the given domain. It can contain a path to a directory or a specific page (eg: `/path/to/` vs. `/path/to/page.html`). For path correspondence, `verifyPhysicalInterface()` must be called with parameter `true` to enable strict mode (eg: `verifyPhysicalInterface(true)`),

Path correspondence identifier (Wiring/Arduino)
```
Serial.print("{'domain':'example.com','path':'/path/to/page.html'}");
```

How to verify a physical interface for path correspondence (Javascript)
```
if (verifyPhysicalInterface(true) ) {
  alert("Correct physical interface for this path");
}
```

### version ###

Additionally, a `version` property is provided to allow for various releases of firmware that correspond to the same domain or path. This can be used to prompt users to update their firmware as needed.

To enforce versioning, supply either an integer or floating-point number as the second parameter of `verifyPhysicalInterface()`. It represents the _minimum_ version that will pass verification; for example, a correspondence identifier containing version 3.2 will pass verification when a web page specifies version 2.4.

Domain correspondence identifier with version property (Wiring/Arduino)
```
Serial.print("{'domain':'example.com','version':1}");
```

Path correspondence identifier with version property (Wiring/Arduino)
```
Serial.print("{'domain':'example.com','path':'/path/to/page.html','version':1}");
```

How to verify a physical interface with versioning (Javascript)
```
if (verifyPhysicalInterface(false, 1) ) {
  alert("Correct physical interface for this domain");
}

if (verifyPhysicalInterface(true, 1) ) {
  alert("Correct physical interface for this path");
}
```

# Usage #

This is a simplified example of how to verify that a physical interface corresponds to a web page. To use this example, you will need to upload the web page below to your server and modify the Wiring/Arduino program at the top of this page to provide a matching correspondence identifier. For an even more robust deployment, consider adding [plug-in detection](Detection.md) to this example.

### HTML/JS/Seriality ###

```
<html>
<head>

  <script type="text/javascript">

    // create global reference
    var serial;
  
    function setup()
    {
      // instantiate seriality
      serial = (document.getElementById("seriality")).Seriality();

      // begin serial communications on first known serial port at 9600 baud, handle failure
      if (!serial.begin(serial.ports[0], 9600)) {
        alert("No physical interface");
        return;
      }

      // verify that the physical interface corresponds to this web page, handle failure
      if (!verifyPhysicalInterface(true)) {
        alert("Incorrect physical interface");
        serial.end();
        return;
      }

      // physical interface corresponds to this web page
      alert("Correct physical interface");
    }
    
    // utility function to request and parse the correspondence identifier
    function verifyPhysicalInterface(strict, version)
    {
      // request the correspondence identifier
      serial.write("@");

      // allow time for the response (100ms)
      var now = new Date();
      var then = new Date();
      while (now - then < 100) {
        now = new Date();
      }

      // optional parameter for physical interface versioning
      if (undefined === version) {
        version = 0;
      }

      // optional parameter to allow for path correspondence
      if (undefined === strict) {
        strict = false;
      }

      // parse correspondence identifier
      if (serial.available()) {
        var identifier = eval('(' + serial.readJSON() + ')');
        if (undefined === identifier) {
          return false;
        }
        if (version > 0) {
          if (undefined === identifier.version) {
            return false;
          }
          if (identifier.version < version) {
            return false;
          }
        }
        if (-1 == location.host.indexOf(identifier.domain)) {
          return false;
        }
        if (strict) {
          if (0 != location.pathname.indexOf(identifier.path)) {
            return false;
          }
        }
        return true;
      }
      return false;
    }

  </script>

</head>
<body onload="setup();">

  <object type="application/Seriality" id="seriality" width="0" height="0"></object>

</body>
</html>
```