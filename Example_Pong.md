# Pong Example #

This example demonstrates how to use Seriality with [Processing.js](http://processingjs.org/). You can try [this example](http://zambetti.com/projects/seriality/examples/pong.html) online.

## HTML/JS/Processing.js/Seriality ##

```
<html>
<head>
  <script type="text/javascript" src="processing.js"></script>
  <script type="text/javascript" src="init.js"></script>
</head>
<body>

<object type="application/seriality" id="seriality" width="0" height="0"></object>

<script type="application/processing">

  var serial;
  
  float x = 100;
  float y = 150;
  float speedX = 0.95;
  float speedY = 0.65;
  float paddleY = 150;

  void setup()
  {
    // set up stage and drawing modes
    size(500, 300);
    fill(255);
    noStroke();
    rectMode(CENTER);

    // begin serial
    serial = (document.getElementById("seriality")).Seriality();
    serial.begin(serial.ports[0], 9600);
    serial.write(".");
  }
  
  void draw()
  {
    // update paddle position
    if (serial.available()) {
      paddleY = (serial.readLine() / 1024) * height;
      serial.write(".");
    }

    // clear screen
    background(200);

    // move ball
    x += speedX;
    y += speedY;

    // bounce off walls
    if ((x < 0) || (width < x)) {
      speedX *= -1;
    }
    if ((y < 0) || (height < y)) {
      speedY *= -1;
    }

    // bounce off paddle
    if (x < 40) {
      if (((paddleY - 50) < y) && (y < (paddleY + 50))) {
        speedX *= -1;
      }
    }

    // draw ball and paddle
    rect(x, y, 20, 20);
    rect(25, paddleY, 20, 100);
  }

  </script>

  <canvas width="500" height="300"></canvas>

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