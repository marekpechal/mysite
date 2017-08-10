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

The signal from a Geiger tube is often read out in this circuit configuration:

![Geiger counter readout circuit]({{site.url}}/assets/pic-geiger-readout.jpg)

The tube behaves very much as a simple capacitor. In the absence of radiation, it is fully charged and the red circuit node is at the high voltage $V_0$. When an ionizing particle passes through the tube, it creates an avalanche of ions and the tube capacitance starts very quickly discharging. That is, until the voltage across it drops below a threshold $V_s$ (the _starting voltage_) where the discharge can no longer be sustained. We can assume this all happens very fast compared with the characteristic time-scale of the $RC$ circuit. After the discharge stops, the circuit starts (comparatively slowly) relaxing back to its steady state with the voltage across the tube back at $V_0$.

The capacitor $C$ does not play any role in this simplified description of the process except to dc isolate the blue node where the pulse is detected by further circuitry from the high voltage (this by the way means it needs to be accordingly rated for high enough voltage). This lack of influence by the coupling capacitor is a justified assumption if it together with the resistance of the connected pulse detector forms an $RC$ circuit with a characteristic time significantly longer than the time-scale given by $R_1$ and the tube capacitance.

Typically, the voltage drop $V_0-V_s$ during the avalanche is rather high (on the order of 100 volts), so it makes sense to reduce it a bit before detection. This can be done by splitting the resistor $R_1$ into a series combination of a smaller $R_1$ (at the high voltage supply) and a larger $R_2$ (at the tube) and connecting the coupling capacitor to the node between them. The voltage drop is then reduced by a factor $R_1/(R_1+R_2)$. 

This is exactly the recommended readout circuit for the LND-712 tube:


The LND-712 tube has a recommended operating voltage of 500 volts, starting voltage of 325 volts or lower and a capacitance of about 3 picofarads. The suggested values for the readout circuit are $R_1 = 1\,\mathrm{M\Omega}$, $R_2 = 10\,\mathrm{M\Omega}$ and $C = 50\,\mathrm{pF}$.

These numbers allow us to estimate the maximum expected current that the high voltage source needs to supply. In a single detection event, a charge of approximately $600\,\mathrm{pF}$ (a capacitance of $3\,\mathrm{pF}$ is discharged by about $200\,\mathrm{V}$) is drawn from the source. The minimum dead time of the tube is specified as $90\,\mathrm{\mu s}$, so the maximum counting rate is about $10^4\,\mathrm{s}^{-1}$, corresponding to an average current of $6\,\mathrm{\mu A}$. At a voltage of $500\,\mathrm{V}$, the maximum power drawn from the high voltage source is about $3\,\mathrm{mW}$.

## Shopping list

1. Geiger tube LND-712 (ordered)
2. MC34063 boost controller (ordered)
3. 170uH inductor
4. BYV26E high voltage fast diode (ordered)
5. high voltage capacitors (one for source, one 50pF for signal coupling)
6. trimmer for hv adjustment (ordered)
7. counter?, op-amps?
8. usb and/or barrel connectors for power
