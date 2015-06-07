---
layout: post
title: Use ArUco in JS way
categories: [Augmented Reality]
tags: [javascript,aruco]
fullview: false
description: A simple introduction to js-aruco
---

Nowadays Augmented Reality is becoming the hottest topic all around the technology world. This semester I followed a AR course at my school. And it let me take a first glance of AR world.
During my AR final project, I used ArUco as the detecter to my real world and implement all the project by javascript which means it runs in the web browser and has a very strong portability.

###Introduction to ArUco
[ArUco](http://www.uco.es/investiga/grupos/ava/node/26) is a minimal library for Augmented Reality applications based on OpenCv. Its main features are:

- Detect markers with a single line of C- Detection of AR boards (markers composed by several markers)
- Requires only OpenCv (>=2.1)
- Up to to 1024 different markers
- Trivial integration with OpenGL and OGRE
- Fast, reliable and cross-platform because relies on OpenCv
- Examples that will help you to get running your AR application in less than 5 minutes
- BSD licence

It could detect the code in image or video and find its position in 3D coordinate system.

![aruco-code]({{ site.url }}/assets/media/aruco-code.png)

###js-aruco
[js-aruco](https://github.com/jcmellado/js-aruco) is a port to JavaScript of the ArUco library. It implements all the function of ArUco in javascript.
It is open source and every one could download it on github and use it. I use it as the detector in my project [arcg](https://github.com/pangboww/arcg). It detect the code and helps me to find where should I put my chessboard in my image. And later I use Three.JS and webGL to draw the chessboard to this position.

![chessboard]({{ site.url }}/assets/media/chessboard.png)

Want to take a look at how I implement this game? Please feel free to see it on [my github](https://github.com/pangboww/).
<br>
<br>

####MediaStream (aka getUserMedia)
Before we start to detect our aruco code, the first thing is getting our video stream in the browser. Thanks to html5, now we could just use input tag and a little js code to get our video stream. If you haven't used getUserMedia, take a look at the [HTML5 Rocks article](http://www.html5rocks.com/en/tutorials/getusermedia/intro/).

Here is a simple example how to use getUserMedia:


{% highlight js %}
navigator.getUserMedia({video:true},
        function (stream){
                video.src = stream;
        },
        function(error){
        }
);
{% endhighlight %}

<br>

####Create detector
After getting the video stream, we should create a detector to detector the code in the stream. Here we use call AR.Detector to create a new implement.

{% highlight js %}
var detector = new AR.Detector();
{% endhighlight %}

And later this detector could get the the image data(which is a image, not a stream) , and returns the markers which it detected in this image.

{% highlight js %}
var markers = detector.detect(imageData);
{% endhighlight %}

<br>

####Get image data
To get image data, we should make a loop to take snapshot of our video stram. *imageData* argument must be a valid *ImageData* canvas object.

{% highlight js %}
var canvas = document.getElementById("canvas");

var context = canvas.getContext("2d");

var imageData = context.getImageData(0, 0, width, height);
{% endhighlight %}

<br>

####3D Pose Estimation
After getting *markers* from *detector.detect()* function, we could start to calculate it 3D Pose.
Markers result is an array of *AR.Marker* objects with detected markers.
*AR.Marker* objects have two properties:

- *id*: the number maker contains
- *corners*: 2D marker corners 

We could create *POS.Posit* object:
{% highlight js %}
var posit = new POS.Posit(modelSize, canvas.width);
{% endhighlight %}
Here *modelSize* argument must be the real marker size (millimeters).

Then we could call it to calculate the 3D Pose of our markers;
{% highlight js %}
var pose = posit.pose(corners);
{% endhighlight %}

pose result will be a POS.Pose object with two estimated pose:

- bestError: Error of the best estimated pose.
- bestRotation: 3x3 rotation matrix of the best estimated pose.
- bestTranslation: Translation vector of the best estimated pose.
- alternativeError: Error of the alternative estimated pose.
- alternativeRotation: 3x3 rotation matrix of the alternative estimated pose.
- alternativeTranslation: Translation vector of the alternative estimated pose.






