---
layout: page
title: Measuring maximum counting rate on the RPi
tags:
- python
- coding
- raspberry-pi
- home-lab
- nscope
- under-construction
---

Partially out of general interest but also because I am thinking about building a new Geiger counter, I decided to measure how fast can the Raspberry Pi reliably count pulses on its GPIOs. This could be quite easy to do with the nScope as the pulse generator.

## Setting up the nScope on the Raspberry Pi (try #1)

The link to the package on the [nScope site](http://www.nscope.org/nscope-on-raspberry-pi/) is broken, so the instructions there are useless. Let's try to get the compiled library directly.

{% highlight shell %}
wget https://raw.github.com/nLabs-nScope/nScopeAPI/master/arm/libnscope.so
wget https://raw.github.com/nLabs-nScope/nScopeAPI/master/python/nScopePy.py
{% endhighlight %}

The relative path to the `libnscope.so` library is hard-coded in `nScopePy.py` according to the github folder structure, so let's change the loading section to

{% highlight python %}
nScopeAPI = CDLL(os.path.join(os.path.dirname(__file__),"libnscope.so"))
{% endhighlight %}

After this, I try to import `nScopePy.py` in python but get the error `libnscope.so: undefined symbol: libusb_cancel_transfer`. I try to fix this by `apt-get install libus-dev` (`libusb` cannot be found by `apt-get`) but it does not help. A bit of googling later, I try to build libusb myself (following instruction on the Raspberry Pi forum):

{% highlight shell %}
wget http://sourceforge.net/projects/libusb/files/libusb-1.0/libusb-1.0.9/libusb-1.0.9.tar.bz2
tar xjf libusb-1.0.9.tar.bz2
cd libusb-1.0.9
./configure
make
sudo make install
{% endhighlight %}

The installation works fine but the error persists. So I give up for now and try to connect the nScope to my laptop instead. I will do it under Ubuntu (I know it works under Windows) and so I might hopefully learn a bit about what's going wrong on the Pi. 


## Setting up the nScope under Ubuntu

I follow basically the same steps as on the Pi, except downloading from the `linux64` folder of the github repo instead of `arm`. When I try importing `nScopePy.py`, it complains about missing `GLIBC_2.17`. It looks like I have version 2.15. `apt-get` cannot automatically find a newer version but again, random people on the internets are of tremendous help:

{% highlight shell %}
wget launchpadlibrarian.net/130794928/libc6_2.17-0ubuntu4_amd64.deb
dpkg -i ipts libc6_2.17-0ubuntu4_amd64.deb
{% endhighlight %}

Importing `nScopePy.py` now works - yay. But the nScope fails to connect. I even tried to download the official gui but that can't find the nScope either.

### Update

Just as a warning, playing with `libc6` probably wasn't the smartest idea. It kind of broke my system. I was not able to install any new packages, instead getting errors about wrong versions of `libc6`. I had to get the `libc6_2.15` package from the `launchpadlibrarian` website and after downgrading `libc6` with it, run `apt-get -f install` to fix stuff. Luckily, this seems to have helped. 

## Measuring the damned thing under Windows

Yes, I do give up easily but I am really interested in the results of this measurement. So I went back to Windows, checked that the nScope works and connected its pulse output to RPi's GPIO 21 (via a 3.9k resistor and a pulldown diode to drop the pulse voltage closer to the 3.3V which the RPi can handle).

I then set up a simple counting script on the Pi:

{% highlight python %}
mypath = os.path.dirname(os.path.realpath(__file__))
dt = 1.0
if len(sys.argv)>1: dt = float(sys.argv[1]) # couting interval optionally set from cmd line

# initialize GPIOs
GPIO.setmode(GPIO.BCM)
GPIO.setup(21,GPIO.IN)

t0 = time.time()
t1 = t0+dt
lastState = 0
c = 0
while time.time()<t1:
  newState = GPIO.input(21)
  if newState and not lastState: c += 1
  lastState = newState

cps = float(c)/dt

# write measured cps into a file
f = open(os.path.join(mypath,'res.txt'),'w')
f.write(str(cps))
f.close()
{% endhighlight %}

On the laptop, I call this script via `plink` and measure the count rate as a function of the input signal's frequency and duty cycle like this:

{% highlight python %}
# set nScope output
scope.setP1frequencyInHz(freq)
scope.setP1dutyPercentage(duty)
scope.setP1on(True)

# give it a bit of time, then count pulses
time.sleep(0.1)
os.system('plink -pw raspberry pi@192.168.1.146 python /home/pi/python/testCounting/testCounting.py '+str(dt))

# copy file with result, read it and clean up
time.sleep(0.1)
os.system('pscp -pw raspberry pi@192.168.1.146:/home/pi/python/testCounting/res.txt'+os.path.join(mypath,'res.txt'))
os.system('plink -pw raspberry pi@192.168.1.146 rm /home/pi/python/testCounting/res.txt')
f = open('res.txt','r')
cps = float(f.read())
f.close()
os.remove('res.txt')
{% endhighlight %}

I found that with a 50% duty cycle, the count rate was basically equal to the frequency over the whole range that the nScope can output (up to 10 kHz). I had to decrease the duty cycle all the way to 1% to see any drop in the count rate. It started happening in a pretty irregular way around 5 kHz which suggests the timing resolution in my polling python script is on the order of a few microseconds.

I also tried to count the pulses using interrupts instead of continuous polling but ran into problems with setting it up. I first needed to install the `RPIO` module but that doesn't seem to work on a Raspberry Pi 3 by default (it does not recognize it as a Raspberry Pi). To fix this, I had to install git (which had some problems of its own) and then
{% highlight shell %}
git clone https://github.com/metachris/RPIO.git --branch v2 --single-branch
cd RPIO
sudo python setup.py install
{% endhighlight %}

After a few unsuccessful tries when I was getting an error complaining about invalid GPIO id, I figured only a subset of the GPIOs supports interrupt. When I chose one of them, the script ran (one needs to call `RPIO.wait_for_interrupts(threaded=True)` and then `RPIO.stop_waiting_for_interrupts()`) but I was getting strange counts. They varied wildly from zero to about seven when the frequency was 2.5 Hz and I was counting for 5 seconds. I then gave up. For now, it seems counting in a loop should be more than fast enough for a Geiger counter. I might revisit this problem in the future.

## Other people's results

There is a very nice [post](http://codeandlife.com/2012/07/03/benchmarking-raspberry-pi-gpio-speed/) by a guy who looked at the speed of GPIO _outputs_. He got around 70kHz with python and around 20MHz (!) with some low-level c coding.
