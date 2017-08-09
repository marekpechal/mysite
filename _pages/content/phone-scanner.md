---
layout: page
title: Phone scanner
tags:
- coding
- python
- under-construction
---

This is a bit of a meta topic. When writing up some of the pages here, I thought it would be nice to be able to quickly sketch something (a diagram, circuit, etc.) and put it on the page. I don't have a tablet-like device to draw on and drawing with a touchpad in Gimp is a bit annoying. Drawing on paper and then taking a picture would be nice but I'm too lazy to move the sd card all the time or copy files via a usb cable.

I found this Android app called 'IP Camera' which runs a simple web server on the phone that allows you to control the camera and take pics from a browser. Fortunately, taking a picture with this turns out to be very easy to automate. The picture is grabbed when the browser accesses `[phone-ip-address]:8080/photoaf.jpg`. This can be also done from Python with `urllib`, then the raw data converted to a `PIL` image.

The next step would be some image processing to isolate the drawing and generatea nice image. The idea is to ultimately have a simple program where I point the camera at a piece of paper with a diagram, press a button in the gui and out comes an image ready to be included on a webpage.
