# Water Rocket
Simple project solving the differtial equations for a water rocket using a Cash-Karp Runge-Kutta solver  

## Theory

### A Simplistic Model of a Water Rocket

#### Geometry

In the following, we assume a cylindrical rocket with the cross-section area $A_{rocket}$, the nozzle opening area $A_{expel}$. The rocket has the length $l_{rocket}$. The rocket can be filled with water and the filling level is counted from the bottom of the rocket and is a function of time $h_{water}(t)$. Hence, the water and air volume in the rocket can simply be calculated as 
```math
V_{water}(t)=A_{rocket}h_{water}(t)
```
```math
V_{air}(t)=A_{rocket}(l_{rocket}-h_{water}(t))
```

#### Equation of Motion

The equation of motion for the rocket, assuming all forces are directed along the z-axis:
```math
F_{rocket} = F_{trust} + F_{gravitation} + F_{drag},
```
where 
- $F_{rocket}=-m_{rocket}\frac{dv_{rocket}}{dt}$,
- $F_{thrust}=v_{expell}\frac{dm_{rocket}}{dt}$,
- $F_{gravitation}=-m_{rocket}g$,
- $F_{drag}=-\frac{1}{2} \rho_{ambient} v_{rocket}^2 C_D A_{rocket}\frac{v_{rocket}}{|v_{rocket}|}$

Above, we assume quadratic drag for objects at high Reynolds numbers and since for our simple setup, we do not expect the rocket to fly higher than a few meters, we can safely assume the gravitational acceleration to be constant. Inserting these relations obove, we obtain:
```math
m_{rocket}\frac{dv_{rocket}}{dt} = -v_{expell}\frac{dm_{rocket}}{dt} - m_{rocket}g - \frac{1}{2} \rho_{ambient} v_{rocket}^2 C_D A_{rocket}\frac{v_{rocket}}{|v_{rocket}|}.
```
The mass of the rocket consists of several contributions, $m_{rocket} = m_{hull}+m_{water}+m_{air}\approx m_{rocket} = m_{hull}+m_{water}$, where we neglect for simplicity the contribution from the pressurized air, which should be in the order of a few grams for a 1l bottle and pressures up to five bars. While the mass of the rocket hull is constant over time, the mass of the water is a function of time and can be expressed by the water density and the filling level:
```math
m_{water} = \rho_{water}V_{water}=\rho_{water}A_{rocket}h_{water}.
```

Further, also the expell velocity is a function of time, since the pressure in the bottle will decrease as more water or air is expelled from the bottle. 

In summary, we have the the unknown variables $m_{water}$, $v_{rocket}$, $v_{expell}$, and several known variables such as the area of the bottle $A_{rocket}$, the mass of the bottle $m_{hull}$, or the ambient air density $\rho_{ambient}$.

#### Water Thrust

During the starting phase of the water rocket, the thrust is generated by the expelling water due to the high pressure in the bottle. However, the pressure will decrease over time, as more and more water is expelled from the bottle.

To relate the water pressure in the bottle to the pressure at the nozzle, the Bernoulli equation is applied:
```math
p_{water}+\frac{1}{2}\rho_{water} v_{sink}^2=p_{ambient} + \frac{1}{2}\rho_{water} v_{expell}^2
```
where $v_{sink}$ is the sinking velocity of the water in the bottle and $v_{expell}$ the expell velocity of the water at the nozzle. Here, we assume water to be incompressible ($\rho_{water} = const$) and neglect the hydrostatic pressure of the system. Further, we assume that the pressue at the nozze is equal to the ambient air pressue. Above, we introduced two further unknown, $v_{sink}$ and $p_{water}$. To find a closing relation for $v_{sink}$, the mass conservation is applied:
```math
v_{sink}A_{rocket}=v_{expell}A_{expell}.
```
To determine the water pressure $p_{water}$, we assume that the water pressure is in thermodynamic equilibrium with the pressurized air, so that $p_{water}=p_{air}$ holds.

However, with sinking water level in the bottle, also the air pressure is lowering. To relate the air pressure at time $t$ to the initial air pressure at time $t=0$, we assume a polytropic process with the polytropic index $\gamma$:
```math
p_{air}(t)V_{air}^{\gamma}(t) = p_{air}(0)V_{air}^{\gamma}(0)
```

Combining all these relations, we obtain a set of equations for the rocket during the time when the expelling water is contributing to the thrust. However, two conditions must be met for the water thrust:
- $p_{water}(t) > p_{ambient}$
- $h_{water}(t) > 0$

If these two conditions are not fullfilled anymore, the water-thrust phase can be considered over.
In the case those two conditions are fulfilled, the Bernoulli equation together with the mass conservation leads to an expression for the expelling water velocity:
```math
v_{expell}(t) = \sqrt{\frac{2}{\rho_{water}}\frac{p_{water}(t)-p_{ambient}}{1 - \left(\frac{A_{expell}}{A_{rocket}}\right)^2} }
```
where
```math
p_{water}(t) = p_{air}(0)\left(\frac{l_{rocket}-h_{water}(0)}{l_{rocket}-h_{water}(t)}\right)^\gamma
```

```math
\frac{dv_{rocket}}{dt} = A_{expell} \rho_{water} \frac{v_{expell}^2(t)}{m_{rocket}(t)} - g - \frac{1}{2} \frac{\rho_{ambient}}{m_{rocket}(t)} v_{rocket}^2(t) C_D A_{rocket}\frac{v_{rocket}(t)}{|v_{rocket}(t)|}.
```
where 
```math
m_{rocket}(t) = m_{hull}+ \rho_{water}A_{rocket}h_{water}(t)
```
```math
\frac{dh_{rocket}}{dt} = v_{rocket}(t)
```
```math
\frac{dh_{water}}{dt} = -v_{sink} = -\frac{A_{expell}}{A_{rocket}} v_{expell}
```
Hence, the calculation scheme is the following:
1. Calculate the rocket's mass $m_{rocket}(t)$
2. Calculate the pressure in the rocket $p_{water}(t)$
3. Calculate the expelling velocity $v_{expell}(t)$
4. Calculate the acceleration of the rocket $\frac{dv_{rocket}}{dt}$
5. Calculate the velocity of the rocket $\frac{dh_{rocket}}{dt}$
6. Calculate the sinking velocity of the water in the rocket $\frac{dh_{water}}{dt}$








