---
layout: page
title: Calculation of the dipole moment sensor
tags:
- electronics
- math
- under-construction
---

The dipole moment sensor I am currently building is essentially a cylindrical container with a curved plate inside which is a section of a smaller cylinder. The container and the plate are coaxial and both are electrically conductive (plastic, covered in copper tape). The sample whose dipole moment is to be measured sits on a little turntable on the axis of the assembly and rotates at a fixed speed. This induces an ac voltage between the outer cylindrical shield and the plate whose amplitude is proportional to the dipole moment. The relative phase between the induced voltage and the rotation of the sample indicates the direction of the dipole moment in the rotation plane.

In the absence of the dipole, the device behaves simply as a capacitor. As a consequence of the superposition principle, the effect of the dipole can be equivalently described as an effective voltage source added in series with the capacitor (or a current source added in parallel with it). Therefore, to fully characterize the sensor, at least in first approximation, we just need to know the capacitance and the proportionality constant between the dipole moment and the induced voltage. This in turn means solving the Laplace equation for the potential with a Dirichlet boundary condition on the outer shield and the plate.



