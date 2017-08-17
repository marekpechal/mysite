---
layout: page
title: Geiger counter v3
use_math: true
tags:
- electronics
- physics
- under-construction
- future
---

Since I am in the US now and my Geiger counter v2 is in the Czech Republic, I thought I would try quickly building another one. It could be interesting to go and have a look at the thorium-containing black sands on California beaches. I could of course have my old counter shipped here but it is a bit bulky, so it would probably cost a lot to post. Bringing it with me last time I went home was not an option - I was afraid I could have problems having it in my carry-on luggage. If I decide to make a new one, I could try to use a different type of Geiger tube this time (one that detects alpha radiation). And it would be a nice exercise to see if I can design and build the whole thing faster this time. I may even experiment with the process of ordering custom-made PCBs from China.

At the moment, I would like the counter to have these features:
- more compact than the last version
- minimalistic and modular (no bells and whistles on the counter itself but additional functionality easy to add with modules)
- visual output via flashing LED
- audio output through earphones for discreteness (optionally switchable to built-in piezo ticker)
- possibly some quantitative readout but only if it can be made compact
- output for Raspberry Pi, powered from the USB port on the Pi or from a battery or powerbank

## Design process

# Geiger tube readout circuitry

The signal from a Geiger tube is often read out in a circuit configuration whose minimal version is shown here in Figure (a):

![Geiger counter readout circuit]({{site.url}}/assets/pic-geiger-readout.jpg)

The tube whose anode is connected to a large resistor $R_{\mathrm{A}}$ (on the order of megaohms) behaves very much like a simple capacitor with a small capacitance $C_{\mathrm{T}}$ (on the order of picofarads). In the absence of radiation, this capacitor is fully charged and the red circuit node is at the high voltage $V_{\mathrm{hv}}$. When an ionizing particle passes through the tube, it creates an avalanche of ions and the tube capacitance starts quickly discharging. That is, until the voltage across it drops below a threshold $V_{\mathrm{s}}$ (the _starting voltage_) where the discharge can no longer be sustained. We can assume this all happens very fast compared with the characteristic time-scale of the $R_{\mathrm{A}}C_{\mathrm{T}}$ circuit. After the discharge stops, the circuit starts (comparatively slowly) relaxing back to its steady state with the voltage across the tube back at $V_{\mathrm{hv}}$.

The resulting voltage dip could in principle be measured directly at the red node but one typically tries to isolate the mesuring circuitry from the high voltage part of the circuit. This is done by connecting a coupling capacitor $C_{\mathrm{A}}$ to the red node and dc biasing its other end to a lower voltage $V_0$ via a resistor $R_{\mathrm{B}}$, as shown in (b). The disadvantage of this configuration is that it significantly increases the effective capacitance of the tube's anode by connecting the coupling capacitor directly to it. This in turn slows down recharging of the tube after a detection event. 

This problem can be alleviated by modifying the circuit into the form shown in (c). Now the coupling capacitor is not connected directly to the anode but to the middle node of a resistive voltage divider. In this setting, $R_{\mathrm{A}2}$ is significantly larger than $R_{\mathrm{A}1}$. Not only does this mean that the relaxation time of the circuit after a discharge is much less affected but it also helps reduce the amplitude of the voltage spike presented to the detection circuitry behind the coupling capacitor.



The LND-712 tube has a recommended operating voltage of 500 volts, starting voltage of 325 volts or lower and a capacitance of about 3 picofarads. The suggested values for the readout circuit are $R_{\mathrm{A}1} = 1\,\mathrm{M\Omega}$, $R_{\mathrm{A}2} = 10\,\mathrm{M\Omega}$ and $C_{\mathrm{A}} = 50\,\mathrm{pF}$.

These numbers allow us to estimate the maximum expected current that the high voltage source needs to supply. In a single detection event, a charge of approximately $600\,\mathrm{pF}$ (a capacitance of $3\,\mathrm{pF}$ is discharged by about $200\,\mathrm{V}$) is drawn from the source. The minimum dead time of the tube is specified as $90\,\mathrm{\mu s}$, so the maximum counting rate is about $10^4\,\mathrm{s}^{-1}$, corresponding to an average current of $6\,\mathrm{\mu A}$. This should be negligible in comparison to the current drawn by the feedback resistors. I am planning to have a shunt feedback resistance of 10 megaohms, which would give a current draw of about 50 microamps.

# High voltage source

The high voltage source will be a boost converter with a [MC34063A]({{site.url}}/assets/datasheets/MC34063A.pdf) controller IC (because I have already built a few with this one and it seems to work quite reliably). The circuit looks roughly like this:

![Geiger counter hv source]({{site.url}}/assets/pic-geiger-hvsource.jpg)

The MC34063A generates the switching pulses, has a built-in comparator to suspend switching when the desired output voltage has been reached and even a built in BJT switch such that in some cases one does not need to use an external one. Timing is maintained by charging and discharging a small capacitor $C_t$ with a constant current. One can also connect a small current sensing resistor $R_{sc}$ in series with the inductor to implement current limiting. When the voltage drop across this resistor exceeds approximately 300 mV, the charging current of the timing capacitor is increased, making it quickly reach the threshold voltage and thus turning the switch off.

There are essentially two options I would consider for the switch: The first is to use a high voltage mosfet (shown in yellow). The advantage is I could get the desired voltage around 500 V in a single step (in blue). The disadvantage is I would probably want to use a mosfet gate driver in this case, so one extra IC. The second option is a BJT. These are harder to find for high voltages, so I'd use a voltage doubler (in green). The advantage is I could connect the transistor direcly to the controller IC (as shown in red), without a special driver, and I could use components rated for half the voltage. The disadvantage is that I would need three capacitors and three diodes in the high voltage part. Apart from these reasons, if I want to run the whole thing from 5V, it could be easier to use a BJT since mosfets usually need a bit higher voltage on their gate to fully open.

Assuming I would use the BJT with the doubler and aim for an output voltage between 400 and 600 volts generated from an input between 5 and 9 volts and I would like to use the 220 microhenry inductors I got from ebay, the current through the inductor should increase at a rate between 23 and 41 milliamps per microsecond in the on state and decrease at a rate between 0.9 and 1.3 amps per microsecond in the off state. Since the typical on and off times of the controller are 26 and 4 microseconds, respectively, the converter will always be operating in the dicontinuous mode.

During the on time, the current through the inductor, assuming zero resistance, would ramp up to between 0.6 and 1.0 amp.

Neglecting losses, this means the energy stored in the inductor in a single cycle would be in the range of 40 to 110 microjoules and at an output voltage of 500 volts, the charge delivered to the load between 0.08 and 0.22 microcoulombs. If I expect to draw about 50 microamps from the output capacitors, that is about 230 to 630 pulses per second. So the converter should be running only about 0.7 to 2.0% of the time.

## Shopping list

- high voltage capacitors (50pF for signal coupling, larger for voltage multiplier)
- counter?, op-amps?
- usb and/or barrel connectors for power
- 10k trimmers for hv adjustment

## Ordered

## Available parts

- Geiger tube [LND-712]({{site.url}}/assets/datasheets/LND712.pdf)
- high voltage capacitors (22nF 600V, 100nF 400V) 
- [BYV26E, BYV26C]({{site.url}}/assets/datasheets/BYV26.pdf) high voltage fast diodes
- 10k, 1k trimmers for hv adjustment
- 220uH inductors
- [MC34063A]({{site.url}}/assets/datasheets/MC34063A.pdf) boost controller
- [TC4420]({{site.url}}/assets/datasheets/TC4420.pdf) mosfet drivers
- [BUX85G]({{site.url}}/assets/datasheets/BUX85.pdf) High voltage NPN



## Lessons learned

- Beware BJTs in fast switching high current applications. Namely, the storage time (the delay between the base current stopping and the transistor actually closing) which can be on the order of microseconds if the transistor is driven into saturation. This can be mitigated using a Baker clamp to prevent saturation. But it just seems easier to use a MOSFET instead of a BJT.

- When testing if a MOSFET opens and closes properly, simply connecting its drain to a pull-up resistor and measuring the voltage drop over it can be misleading. The MOSFET can have a capacitance on the order of hundreds of picofarads. This together with the sensing resistor forms an RC circuit that low-pass filters the measured signal, making it seem as if the transistor does not fully open/close.

- Sometimes meticulous testing of each part of the circuit can be counterproductive. Connecting oscilloscope leads to different parts of the boost converter circuit can make it go mad with the extra capacitance, showing very confusing behaviour which is not actually there when the scope is not connected.

- Keep connections in the feedback loop _short_, especially if the voltage divider consists of very large resistors. Presumably, the inductance of the wire, when connected between the high-impedance voltage divider and the feedback input of the controller makes it behave improperly (I saw the output of the converter shoot up to over 900 volts when it was supposed to be below 100 - this suggests the feedback simply doesn't turn of the switching). If a long connection is needed, put it between the voltage divider and the output of the converter (effectively on the low-impedance side where the inductance won't matter as much).
