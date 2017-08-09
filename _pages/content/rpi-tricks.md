---
layout: page
title: Setting up a Raspberry Pi
tags:
- coding
- raspberry-pi
- home-lab
- under-construction
---
This is a list of little tricks I played with on the Raspberry Pi in attempts to make my life easier.

## Finding its IP without access to router
Having to connect the Pi to the laptop by an ethernet cable all the time is a bit cumbersome. The wifi on the Pi is now set up, so I could in principle connect to it through our wifi router. The only catch is that the IP address is not guaranteed to be the same every time and I do not have access to the router's admin account (the person who was in charge of it left the house without telling anyone the password). So I came up with a little work-around. Have a python script run on the Pi when it boots up and send me the IP address by email. I usually have to wait a few minutes after turning the Pi on but that's not really a problem.

## Installing jupyter notebook server 
