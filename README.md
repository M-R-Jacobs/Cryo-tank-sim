# Cryo-tank-sim
### Tank Boil-off Model

A thermodynamic model of heat leak and boil-off in cryogenic propellant tanks, built up in stages from a vented tank to self-pressurization and multiple cryogens.

## v0.1: vented boil-off

**Facts**
The tank vents at constant pressure, so every watt of heat leak goes into vaporizing liquid. This means:

$$\dot m = \frac{Q}{h_{fg}}, \qquad Q = q_{MLI}A + Q_{struts}$$

Saturated properties ($h_{fg}$, $\rho_l$, $T_{sat}$) come from [CoolProp](http://www.coolprop.org/) C++ library. Grateful for their support.

**Default case:** 0.5 m³ LN2 tank, 90% full, 1 atm, 5 W heat leak. In this case, the boil-off rate at 2.17 kg/day (0.60 %/day) would cause a time to empty of 167 days.

These match a hand calculation with $h_{fg} = 199.2$ kJ/kg and $\rho_l = 806$ kg/m³.
