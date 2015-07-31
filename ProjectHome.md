![http://www.zambetti.com/projects/seriality/images/diagram_new.png](http://www.zambetti.com/projects/seriality/images/diagram_new.png)

Seriality is a serial connectivity browser plug-in that enables web pages to communicate with microcontrollers via Javascript. Seriality is physicality for the Web.

```
<html>
<head>
  <script type="text/javascript">  
    function setup()
    {
      var serial = (document.getElementById("seriality")).Seriality();
      serial.begin(serial.ports[0], 9600);
      serial.write("Hello World");
    }
  </script>
</head>
<body onload="setup();">
  <object type="application/Seriality" id="seriality" width="0" height="0"></object>
</body>
</html>
```