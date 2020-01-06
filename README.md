# Music with bling bling Light

> This is an Internet of Thing project.
> Let us play music and visualize.
> Used technique: raspberry pi, Socket.gpio, PWM control, JS Audio Visualier.

[![Build Status](http://img.shields.io/travis/badges/badgerbadgerbadger.svg?style=flat-square)](https://travis-ci.org/badges/badgerbadgerbadger)


***Project Architecture***




**ttystudio**



---
## Overview of this Project

### Music Visualization with the Fourier Transform

- There are a ton of extravagant math conditions and diagrams that surface when you look for a Fourier change, however, for the reasons for this instructional exercise, I'm going to give a fundamental review.

    ![](https://i.imgur.com/kOWxBlQ.jpg)

- For the most part, we see a standard waveform. What this shows is the manner by which uproarious the melody is after some time. So around the center of the tune, we see these enormous spikes, and afterward these spikes get fundamentally littler. So we can see that the melody gets uproarious, and afterward a lot calmer just after. This is indicating plentifulness (uproar) after some time. This portrayal is known as the time area.

    This doesn't give us a great deal of data about the melody, other than how boisterous it is at sure focuses. To get familiar with it, we can utilize a Fourier change. 

    Rather than speaking to sound in the time-space, a Fourier change lets us speak to it in the recurrence area. This implies as opposed to giving us plentifulness and time, we'll be taking a gander at adequacy and recurrence.
     
    ![](https://media.giphy.com/media/kTLZ4yQvnlLyw/giphy.gif)

- And beautify that...

    ![](https://im5.ezgif.com/tmp/ezgif-5-07d0be7f872b.gif)
---

## Table of Contents (Optional)

> If you're `README` has a lot of info, section headers might be nice.

- [Installation](#installation)
- [Features](#features)
- [Contributing](#contributing)
- [Team](#team)
- [FAQ](#faq)
- [Support](#support)
- [License](#license)

---

## Installation



### Enviroment Setup
*excute the following instructions on your raspberry command line.*

#### Install Node.js on Raspberry Pi
 - [The Raspberry Pi has a row of GPIO (General Purpose input/output) pins, and these can be used to interact in amazing ways with the real world. This tutorial will focus on how to use these with Node.js. --- reference from w3cshool](https://www.w3schools.com/nodejs/nodejs_raspberrypi.asp)

> Update your system package list:

```shell
$ sudo apt-get update
```

> Upgrade all your installed packages to their latest version:

```shell
$ sudo apt-get dist-upgrade
```

> To download and install newest version of Node.js, use the following command:
```shell
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
```

> Now install it by running:
```shell
$ sudo apt-get install -y nodejs
```

> Check that the installation was successful, and the version number of Node.js with:
```shell
$ node -v
```

#### Install Pigpio library
- In our project, we use GPIO to control LED follow the music rhythm. Pigpio manages the PWM duty cycle in software, also can help us achieve our goal.

- You can take a quick look in this projct to understand what does Pigpio working on. - [Fading LEDs with PWM on all pins with Pi Zero & Node.js](https://seblee.me/2016/02/fading-leds-with-pwm-on-a-raspberry-pi-zero-with-node-js/)

    ![](https://seblee.me/wp-content/uploads/2016/02/ezgif.com-optimize.gif)


> The pigpio C library is a prerequisite for the pigpio Node.js module.

> Run the following command to determine which version of the pigpio C library is installed:


#### Install socket.io for Node.js

> With the webserver set up, update your Raspberry Pi system packages to their latest versions.

> Update your system package list:
```shell=
$ sudo apt-get update
```
> Upgrade all your installed packages to their latest version:
```shell=
$ sudo apt-get dist-upgrade
```

> To download and install newest version of socket.io, use the following command:
```shell=
$ npm install socket.io --save
```
### Hardware Setup

#### Hardware Compoments


| Name | Quantity 
| -------- | -------- 
| DFRobot Raspberry Pi 3     | 1
| Assembled Pi T-Cobbler Plus - GPIO Breakout    | 1
| LED     | 15*3


#### Circuit Diagram

![](https://i.imgur.com/CUkPKhe.png)

- Look at the above illustration of the circuit.

    1. On the Raspberry Pi, connect the female leg of a jumper wire to a GND pin. In our example we used Physical Pin 6 (GND, row 3, right column)
    2. On the Breadboard, connect the male leg of the jumper wire connected to the GND power, to the Ground Bus on the right side. That entire column of your breadboard is connected, so it doesn't matter which row. In our example we attached it to row 1.
    3. For each LED: Connect the LED so that it connects to 2 Tie-Point rows. In our example we connected:
        - LED1 to GPIO18
        - LED2 to GPIO24
        - LED3 to GPIO25
        - LED4 to GPIO4
        - LED5 to GPIO17
        - LED6 to GPIO27
        - LED7 to GPIO22
        - LED8 to GPIO5
        - LED9 to GPIO6
        - LED10 to GPIO13
        - LED11 to GPIO19
        - LED12 to GPIO26
        - LED13 to GPIO16
        - LED14 to GPIO20
        - LED15 to GPIO21
        

- Now it is time to boot up the Raspberry Pi, and write the Node.js script to interact with it.



```shell
$ pigpiod -v
```

> For the Raspberry Pi Zero, 1, 2 and 3 V41 or higher of the pigpio C library is required. For the Raspberry Pi 4 V69 or higher is required.

> If the pigpio C library is not installed or if the installed version is too old, the latest version can be installed with the following commands:
```shell
$ sudo apt-get update
$ sudo apt-get install pigpio
```

> Now install it by running:
```shell
$ sudo apt-get install -y nodejs
```

> Install the pigpio Node.js package
```shell
$ npm install pigpio
```

---

## Usage

### Webserver for Raspberry Pi and Node.js
> In our "nodetest" directory create a new directory we can use for static html files:
```shell
$ mkdir {your project name}
``` 
> Now lets set up a webserver. Create a Node.js file that opens the requested file and returns the content to the client.

```shell
$ nano webserver.js
``` 
- webserver.js
```javascript=
var Gpio = require('pigpio').Gpio; //require onoff to control GPIO

var pins = []
var ledPins = [18,25,24,4,17,27,22,5,6,13,19,26,16,20,21]; 
// initialise all the pins
for (var i = 0; i<ledPins.length; i++) { 
	var led = new Gpio(ledPins[i], {mode: Gpio.OUTPUT});    
	pins.push(led);
}
pins.forEach(function(currentValue) { //for each LED
    currentValue.pwmWrite(0); //turn off LED
    
});
// express framework ref:
var express = require('express'),
     app = express(),
     server = require('http').createServer(app),
     io = require('socket.io').listen(server);

// listening the port 8081
server.listen(8081);

// set the static directory to load static files in Express
app.use(express.static('public'))
// now, you can load all files under the public directory

app.get('/', function(req, res){
     res.sendfile(__dirname + '/public/index.html');
});

// socket.io listener used to control the GPIO
io.sockets.on('connection' , function(socket){
	

  	socket.on('state', function (data) { //get button state from client
    	console.log(data);
		pins[data.pin].pwmWrite(data.data); //turn LED on or off
    	
  });
});

```

> Then create public folder to put html file.
```shell
$ mkdir public
$ cd public
```
> Noe lets set up a html view, download the javascript file and css file and the same root.
> Those file reference from the following project : https://github.com/gg-1414/music-visualizer. 
```shell
$ nano index.html
```
- index.html
```htmlmixed=
<!DOCTYPE html>
<html>
<title>Music with LED</title>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Music Visualizer</title>
    <link rel="stylesheet" href="style.css">
    <script src="script.js"></script>
</head>
<body>
    <input type="file" id="file-input" accept="audio/*,video/*,image/*" />
    <canvas id="canvas"></canvas>
    <h3 id="name"></h3>
    <audio id="audio" controls></audio>
    <div id="background"></div>

</html>
</body>

</html>

```
> create script file
```shell
$ nano scripu.js
```
```javascript=
window.onload = function() {

  const file = document.getElementById("file-input");
  const canvas = document.getElementById("canvas");
  const h3 = document.getElementById('name')
  const audio = document.getElementById("audio");

  file.onchange = function() {

    const files = this.files; // FileList containing File objects selected by the user (DOM File API)
    console.log('FILES[0]: ', files[0])
    audio.src = URL.createObjectURL(files[0]); // Creates a DOMString containing the specified File object

    const name = files[0].name
    h3.innerText = `${name}` // Sets <h3> to the name of the file

    ///////// <CANVAS> INITIALIZATION //////////
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    const ctx = canvas.getContext("2d");
    ///////////////////////////////////////////


    const context = new AudioContext(); // (Interface) Audio-processing graph
    let src = context.createMediaElementSource(audio); // Give the audio context an audio source,
    // to which can then be played and manipulated
    const analyser = context.createAnalyser(); // Create an analyser for the audio context

    src.connect(analyser); // Connects the audio context source to the analyser
    analyser.connect(context.destination); // End destination of an audio graph in a given context
    // Sends sound to the speakers or headphones


    /////////////// ANALYSER FFTSIZE ////////////////////////
    analyser.fftSize = 32768;

    // (FFT) is an algorithm that samples a signal over a period of time
    // and divides it into its frequency components (single sinusoidal oscillations).
    // It separates the mixed signals and shows what frequency is a violent vibration.

    // (FFTSize) represents the window size in samples that is used when performing a FFT

    // Lower the size, the less bars (but wider in size)
    ///////////////////////////////////////////////////////////


    const bufferLength = analyser.frequencyBinCount; // (read-only property)
    // Unsigned integer, half of fftSize (so in this case, bufferLength = 8192)
    // Equates to number of data values you have to play with for the visualization

    // The FFT size defines the number of bins used for dividing the window into equal strips, or bins.
    // Hence, a bin is a spectrum sample, and defines the frequency resolution of the window.

    const dataArray = new Uint8Array(bufferLength); // Converts to 8-bit unsigned integer array
    // At this point dataArray is an array with length of bufferLength but no values
    console.log('DATA-ARRAY: ', dataArray) // Check out this array of frequency values!

    const WIDTH = canvas.width;
    const HEIGHT = canvas.height;
    console.log('WIDTH: ', WIDTH, 'HEIGHT: ', HEIGHT)

    const barWidth = (WIDTH / 15);
    console.log('BARWIDTH: ', barWidth)

    console.log('TOTAL WIDTH: ', (117*10)+(118*barWidth)) // (total space between bars)+(total width of all bars)
    
    let barHeight;
    let x = 0;
    let socket = io.connect(); //load socket.io-client and connect to the host
    
    function renderFrame() {
      requestAnimationFrame(renderFrame); // Takes callback function to invoke before rendering

      x = 0;

      analyser.getByteFrequencyData(dataArray); // Copies the frequency data into dataArray
      // Results in a normalized array of values between 0 and 255
      // Before this step, dataArray's values are all zeros (but with length of 8192)

      ctx.fillStyle = "rgba(0,0,0,0.2)"; // Clears canvas before rendering bars (black with opacity 0.2)
      ctx.fillRect(0, 0, WIDTH, HEIGHT); // Fade effect, set opacity to 1 for sharper rendering of bars

      let r, g, b;
      let bars = 15 // Set total number of bars you want per frame
      
      for (let i = 0; i < bars; i++) {
        barHeight = (dataArray[i] * 2.5);

    
        r = 0
        g = 199
        b = 255

        ctx.fillStyle = `rgb(${r},${g},${b})`;
        ctx.fillRect(x, (HEIGHT - barHeight), barWidth, barHeight);
        
        socket.emit("state", {"pin":i,"data":dataArray[i]}); //send bars state to server
        
        x += barWidth + 10 // Gives 10px space between each bar
      }
    }

    audio.play();
    renderFrame();
  };
};

```
> Create css file
```shell
$ nano style.css
```
```css=

body {
  background-color: black;
}

#file-input {
  position: fixed;
  top: 10px;
  left: 10px;
  z-index: 3;
}

#canvas {
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
}

audio {
  position: fixed;
  left: 10px;
  bottom: 10px;
  width: calc(100% - 25px);
  z-index: 3;
}

#name {
  position: absolute;
  top: 0;
  right: 20px;
  z-index: 3;
  color: #eeeeee;
  font-family: monospace;
}

#background {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  background: linear-gradient(transparent,#00035f,transparent);
  background-size: 100% 7px;
  animation: bg 1s infinite linear;
  z-index: 2;
  opacity: 0.3;
}

@keyframes bg {
  0%{ background-position: 0 0; }
  100%{ background-position: 8px 8px; }
}
```

---

## License



- Copyright 2019 © LAB706.
