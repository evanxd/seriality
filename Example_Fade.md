# Fade Example #

This example demonstrates how to use Seriality and JQuery together. This example is also available in the Seriality installation package and [online](http://zambetti.com/projects/seriality/examples/fade.html).

## HTML/JS/JQuery/Seriality ##

```
<html>
<head>

  <link type="text/css" href="http://jquery-ui.googlecode.com/svn/tags/1.7.2/themes/base/ui.all.css" rel="stylesheet" />

  <script type="text/javascript" src="http://jquery-ui.googlecode.com/svn/tags/1.7.2/jquery-1.3.2.js"></script>
  <script type="text/javascript" src="http://jquery-ui.googlecode.com/svn/tags/1.7.2/ui/ui.core.js"></script>
  <script type="text/javascript" src="http://jquery-ui.googlecode.com/svn/tags/1.7.2/ui/ui.slider.js"></script>

  <script type="text/javascript">

    // create global reference
    var serial;

    // execute setup when document is ready
    $(document).ready(setup);
  
    function setup()
    {
      // create a slider that ranges from 0-255
      // tell slider to call updateLED whenever it slides
      $("#slider").slider({min:0, max:255, slide:updateLED});

      // instantiate seriality
      serial = (document.getElementById("seriality")).Seriality();

      // begin serial communications on first known serial port at 9600 baud
      serial.begin(serial.ports[0], 9600);

      // initialize the LED to off
      serial.writeByte(0);
    }
    
    // whenever the slider slides
    function updateLED(event, ui)
    {
      // write a byte to the device with the current slider value
      serial.writeByte($("#slider").slider('option', 'value'));
    }
 
  </script>

</head>
<body>

  <object type="application/Seriality" id="seriality" width="0" height="0"> </object>

  <div id="slider" style="margin:10px; width:400px"></div>

</body>
</html>
```

## Wiring/Arduino ##

```
byte value = 0;

void setup()
{
  Serial.begin(9600);
  pinMode(13, OUTPUT);
}

void loop()
{
  if (Serial.available()) {
    value = Serial.read();
  }
  fadeLED();
}

void fadeLED()
{
  for (int i = 0; i < 256; ++i) {
    if (i < value) {
      digitalWrite(13, HIGH);
    }
    else {
      digitalWrite(13, LOW);
    }
  }
}
```