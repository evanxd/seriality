# Scale Example #

This example demonstrates how to read from a device using Seriality. It uses a query technique, querying the device for the latest analog reading. This technique offers a noticeable performance advantage and is lightweight enough to allow the page to perform other Javascript intensive tasks in an interleaved/controllable fashion. This example is also available in the Seriality installation package and [online](http://zambetti.com/projects/seriality/examples/scale.html).

## HTML/JS/Seriality ##

```
<html>
<head>

  <script type="text/javascript">

    // make global reference
    var serial;

    function setup()
    {
      // instantiate seriality
      serial = (document.getElementById("seriality")).Seriality();

      // begin serial communications on first known port at 9600 baud
      serial.begin(serial.ports[0], 9600);

      // set infinite interval for 50ms
      setInterval(loop, 50);

      // send anything to query for the current potentiometer reading
      serial.write(".");
    }
    
    function loop()
    {
      var value = 0;

      // if there are bytes to read
      if (serial.available())
      {
        // read a line that represents an integer ranging 0-1023,
        // then scale to 0-255 (converting to number) and round to the whole pixel
        value = Math.round(serial.readLine() / 4);

        // update the css values
        document.getElementById("square").style.width = value + "px";
        document.getElementById("square").style.height = value + "px";

        // query again for next reading
        serial.write(".");
      }
    }
 
  </script>

</head>
<body onload="setup()">

  <object type="application/Seriality" id="seriality" width="0" height="0"> </object>

  <h3>Scale Example</h3>
  <p>Control div with a potentiometer</p>
  <div style="background-color:#ccc;width:256px;height:256px">
    <div id="square" style="background-color:#FF0;width:256px;height:256px"></div>
  </div>

</body>
</html>

```

## Wiring/Arduino ##

```
void setup()
{
  Serial.begin(9600);
}

void loop()
{
  byte in;
  if (Serial.available()) {
    in = Serial.read();
    Serial.println(analogRead(0), DEC);
  }
}
```