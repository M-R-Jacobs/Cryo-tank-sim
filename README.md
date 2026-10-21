# Cryo-tank-sim
### Tank Boil-off Model

A thermodynamic model of heat leak and boil-off in cryogenic propellant tanks, built up in stages from a vented tank to self-pressurization and multiple cryogens.

## v0.1: vented boil-off

Impetus: How fast does a vented cryogenic tank lose its liquid when heat leaks in?

The tank vents at constant pressure, so the liquid stays at its saturation temperature and cannot warm up. Every watt of heat leak therefore goes into vaporizing liquid, and the boil-off rate is set by the latent heat alone:

$$\dot m = \frac{Q}{h_{fg}}, \qquad Q = q_{MLI}A + Q_{struts}$$

The heat leak has two parts: radiation through the multilayer insulation (MLI), scaled by tank surface area, and conduction through the supports and penetrations.

Saturated properties ($h_{fg}$, $\rho_l$, $T_{sat}$) come from the [CoolProp](http://www.coolprop.org/) C++ library. Grateful for their support.

**"Default case":** 0.5 m³ LN2 tank, 90% full, 1 atm, 5 W heat leak. In this case, the boil-off rate of 2.17 kg/day (0.60 %/day) would cause a time to empty of 167 days.

These match a hand calculation with $h_{fg} = 199.2$ kJ/kg and $\rho_l = 806$ kg/m³.

## v0.2: vacuum jacket quality

v0.1 assumed a perfect vacuum between the tank and its outer wall. Real jackets leak and outgas, so really how much does the residual gas matter?

Gas molecules in the jacket carry heat from the warm outer wall to the cold tank through conduction, which adds a third term to the heat leak:

$$Q = q_{MLI}A + Q_{struts} + q_{gas}(P)\A$$

How that term behaves depends on the Knudsen number, the ratio of the gas's mean free path to the jacket gap.

At good vacuum (Kn ≫ 1), molecules cross the gap without colliding with each other, so each one carries heat directly from wall to wall. More molecules means more heat, and the flux is linear in pressure:

$$q_{fm} = a_{eff}\,\frac{\gamma+1}{\gamma-1}\sqrt{\frac{R}{8\pi M T}}\;P\,(T_{hot}-T_{cold})$$

At poor vacuum (Kn ≪ 1), molecules collide constantly and the gas behaves like ordinary conducting air. Adding more gas no longer helps, and the flux saturates at Fourier conduction across the gap:

$$q_c = \frac{k\,(T_{hot}-T_{cold})}{\text{gap}}$$

The two regimes are bridged with a harmonic sum, so whichever is smaller controls:

$$q_{gas} = \left(\frac{1}{q_{fm}} + \frac{1}{q_c}\right)^{-1}$$

**Default case now:** same tank as v0.1, with a 2 cm air-filled jacket. At 10⁻⁵ Pa the gas adds only 0.01 W and the tank still lasts 167 days. At 10⁻³ Pa it adds 0.63 W, cutting that to 149 days. At 10⁻² Pa the gas leak (6.2 W) exceeds the MLI and struts combined, and boil-off doubles to 1.34 %/day. At 1 Pa it reaches 293 W and the tank empties in under 3 days.

Below about 10⁻⁴ Pa the gas term is negligible and v0.1's result holds. Above about 10⁻² Pa the gas takes over, and a full loss of vacuum raises the heat leak roughly 100×. In practice, jacket vacuum is as important to boil-off as the insulation itself.

**(Running list) Assumptions**
- Residual gas is air; a baked, getter-pumped jacket would be mostly H2/He with different constants
- Gas conduction acts in parallel with MLI over the full tank area; MLI's own pressure dependence is not modeled
- Single accommodation coefficient (0.9) on both walls, equal areas
