---
layout: page
title: Phone scanner
tags:
- coding
- python
- under-construction
---

This is a bit of a meta topic. When writing up some of the pages here, I thought it would be nice to be able to quickly sketch something (a diagram, circuit, etc.) and put it on the page. I don't have a tablet-like device to draw on and drawing with a touchpad in Gimp is a bit annoying. Drawing on paper and then taking a picture would be nice but I'm too lazy to move the sd card all the time or copy files via a usb cable.

I found this Android app called 'IP Camera' which runs a simple web server on the phone that allows you to control the camera and take pics from a browser. Fortunately, taking a picture with this turns out to be very easy to automate. The picture is grabbed when the browser accesses `[phone-ip]:8080/photoaf.jpg`. This can be also done from Python with `urllib`, then the raw data converted to a `PIL` image. One can also turn the phone's flashlight on and off with `[phone-ip]:8080/enabletorch` and `[phone-ip]:8080/disabletorch` and set the resolution of the pictures with `[phone-ip]:8080/settings/photo_size?set=1920x1080`.

The next step would be some image processing to isolate the drawing and generate a nice image. The idea is to ultimately have a simple program where I point the camera at a piece of paper with a diagram, press a button in the gui and out comes an image ready to be included on a webpage.

## Isolating drawings from a scan

I wrote a quick and dirty script to process the 'scanned' image. It consists of the following steps:

1. Convert to greyscale.
2. Apply a median filter to isolate the relatively homogeneous background of the paper. Divide the original picture by this to suppress variations in paper brightness.
3. Apply edge detection filter, then max filter, normalize and threshold into a binary image. This gives a hard mask which selects only areas around the text.
4. Get a level histogram of the original image (after division by the background) in the masked areas. Find the one big peak. This corresponds to the white value. Find the width of this peak. Apply a pointwise map to the image which maps the value of the peak center minus three times the width (to have some margin) onto full white. Choose something nonlinear which stretches the values close to white but leaves the darker shades untouched.

A bit surprisingly, this algorithm gives pretty decent results such as this (colors added in Gimp):

![Example image]({{site.url}}/assets/pic-geiger-readout.jpg)
