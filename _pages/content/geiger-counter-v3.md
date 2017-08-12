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

The signal from a Geiger tube is often read out in a circuit configuration whose minimal version is shown here in Figure (a):

![Geiger counter readout circuit]({{site.url}}/assets/pic-geiger-readout.jpg)

The tube whose anode is connected to a large resistor $R_{\mathrm{A}}$ (on the order of megaohms) behaves very much like a simple capacitor with a small capacitance $C_{\mathrm{T}}$ (on the order of picofarads). In the absence of radiation, this capacitor is fully charged and the red circuit node is at the high voltage $V_{\mathrm{hv}}$. When an ionizing particle passes through the tube, it creates an avalanche of ions and the tube capacitance starts quickly discharging. That is, until the voltage across it drops below a threshold $V_{\mathrm{s}}$ (the _starting voltage_) where the discharge can no longer be sustained. We can assume this all happens very fast compared with the characteristic time-scale of the $R_{\mathrm{A}}C_{\mathrm{T}}$ circuit. After the discharge stops, the circuit starts (comparatively slowly) relaxing back to its steady state with the voltage across the tube back at $V_{\mathrm{hv}}$.

The resulting voltage dip could in principle be measured directly at the red node but one typically tries to isolate the mesuring circuitry from the high voltage part of the circuit. This is done by connecting a coupling capacitor $C_{\mathrm{A}}$ to the red node and dc biasing its other end to a lower voltage $V_0$ via a resistor $R_{\mathrm{B}}$, as shown in (b). The disadvantage of this configuration is that it significantly increases the effective capacitance of the tube's anode by connecting the coupling capacitor directly to it. This in turn slows down recharging of the tube after a detection event. 

This problem can be alleviated by modifying the circuit into the form shown in (c). Now the coupling capacitor is not connected directly to the anode but to the middle node of a resistive voltage divider. In this setting, $R_{\mathrm{A}2}$ is significantly larger than $R_{\mathrm{A}1}$. Not only does this mean that the relaxation time of the circuit after a discharge is much less affected but it also helps reduce the amplitude of the voltage spike presented to the detection circuitry behind the coupling capacitor.



The LND-712 tube has a recommended operating voltage of 500 volts, starting voltage of 325 volts or lower and a capacitance of about 3 picofarads. The suggested values for the readout circuit are $R_{\mathrm{A}1} = 1\,\mathrm{M\Omega}$, $R_{\mathrm{A}2} = 10\,\mathrm{M\Omega}$ and $C_{\mathrm{A}} = 50\,\mathrm{pF}$.

These numbers allow us to estimate the maximum expected current that the high voltage source needs to supply. In a single detection event, a charge of approximately $600\,\mathrm{pF}$ (a capacitance of $3\,\mathrm{pF}$ is discharged by about $200\,\mathrm{V}$) is drawn from the source. The minimum dead time of the tube is specified as $90\,\mathrm{\mu s}$, so the maximum counting rate is about $10^4\,\mathrm{s}^{-1}$, corresponding to an average current of $6\,\mathrm{\mu A}$. At a voltage of $500\,\mathrm{V}$, the maximum power drawn from the high voltage source is about $3\,\mathrm{mW}$.

## Shopping list

- high voltage capacitors (50pF for signal coupling, larger for voltage multiplier)
- counter?, op-amps?
- usb and/or barrel connectors for power

## Ordered

- Geiger tube LND-712
- MC34063 boost controller
- 220uH inductor
- BYV26E, BYV26C high voltage fast diodes
- trimmer for hv adjustment

## Available parts

- high voltage capacitors (22nF, 600V) 
