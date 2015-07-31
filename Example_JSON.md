# JSON Example #

This example demonstrates how to read a JSON formatted string from a device using Seriality. It uses a query technique, querying the device for the latest reading. This technique offers a noticeable performance advantage and is lightweight enough to allow the page to perform other Javascript intensive tasks in an interleaved/controllable fashion. This example is also available in the Seriality installation package and [online](http://zambetti.com/projects/seriality/examples/json.html).

## HTML/JS/Seriality ##

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

      // begin serial communications on the first known port at 9600 baud
      serial.begin(serial.ports[0], 9600);

      // create periodic loop to process incoming data
      setInterval(loop, 125);

      // query for the first reading
      serial.write(".");
    }
    
    function loop()
    {
      var json;
      var data;

      // if there are bytes to read
      if (serial.available()) {

        // read a JSON formatted string
        json = serial.readJSON();

        // transform JSON formatted string into an object
        data = eval('(' + json + ')');

        // if transformation was successful, populate text fields with the data
        if (undefined != data) {
          document.getElementById("knob").value = data.knob;
          document.getElementById("button").value = data.button;
        }

        // query for next reading
        serial.write(".");
      }
    }
 
  </script>

</head>
<body onload="setup()">

  <object type="application/Seriality" id="seriality" width="0" height="0"> </object>

  <form>
    Knob <input id="knob" type="text" value=""><br/>
    Button <input id="button" type="text" value="">
  </form>

</body>
</html>
```

## Wiring/Arduino ##

```
int knob;
int button;

void setup()
{
  Serial.begin(9600);
  pinMode(7, INPUT);
}

void loop()
{
  button = digitalRead(7);
  knob = analogRead(0);
 
  byte in;
  if (Serial.available()) {
    in = Serial.read();
    sendJSON();
  }
}

void sendJSON()
{
  Serial.print("{");
  Serial.print("'knob':");
  Serial.print(knob);
  Serial.print(",");
  Serial.print("'button':");
  if (button) {
    Serial.print("true");
  } else {
    Serial.print("false");
  }
  Serial.print("}");
}
```