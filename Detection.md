# Detecting Seriality #

Some sample code to detect the Seriality browser plug-in is included below. This has been tested on Safari, Firefox, and Chrome for Mac OS X. See the `apiVersion` mentioned in the API documentation for version checking.

```
<html>
<head>

  <script type="text/javascript">

    var serial;

    function setup()
    {
      if (hasSeriality()) {
        serial = (document.getElementById("seriality")).Seriality();
        alert("You have Seriality" + "\n" + serial.ports);
      } else {
        alert("You do not have Seriality");
      }
    }

    function hasSeriality()
    {
      if (navigator.plugins && navigator.plugins.length > 0) {
        for (var i = 0; i < navigator.plugins.length; ++i) {
          if ((navigator.plugins[i].name.indexOf("Seriality") >= 0) || (navigator.plugins[i].description.indexOf("Seriality") >= 0)) {
             return true;
          }
        }
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