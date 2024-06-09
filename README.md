# Water Rocket

Simple project solving some differtial equations for a water rocket using a Cash-Karp Runge-Kutta solver.  

This program comes with ABSOLUTELY NO WARRANTY and serves only as toy model.

## Theory

### A Simplistic Model of a Water Rocket

#### Geometry

In the following, we assume a cylindrical rocket with the cross-section area $A_{rocket}$, the nozzle opening area $A_{expel}$. The rocket has the length $L_{rocket}$. The rocket can be filled with water and the filling level is counted from the bottom of the rocket and is a function of time $h_{water}(t)$. Hence, the water and air volume in the rocket can simply be calculated as 
```math
V_{water}(t)=A_{rocket}h_{water}(t)
```
```math
V_{air}(t)=A_{rocket}(L_{rocket}-h_{water}(t))
```

#### Equation of Motion
In the following we assume all momenta, forces etc. in z-direction.
Newton's second law states that in the absence of external forces, the total momentum is conserved:
```math
\frac{dP}{dt} = 0
```
We can split the total momentum into two components, the momentum of the rocket system $P_{rocket}$ and the momentum of the water immediately after leaving the rocket $P_{water}$. Hence the equation above can be written as
```math
\frac{dP_{rocket}}{dt}+\frac{dP_{water}}{dt} = 0
```
In the labrotory frame, the time derivative of the momentum of the rocket system can be written as 
```math
\frac{dP_{rocket}}{dt} = \frac{dm_{rocket}}{dt}v_{rocket}+m_{rocket}\frac{dv_{rocket}}{dt}
```
Hence, the momentum of the rocket may increase over time with inceasing mass or inceasing velocity. 
The momentum of the water system inceases as the rocket is loosing its mass and expelling it with velocity $v_{expel}$ measured relative to the rockets velocity $v_{rocket}$:
```math
\frac{dP_{water}}{dt} = \frac{dm_{rocket}}{dt}(v_{expel}-v_{rocket})
```
Combining those two equations results in the well-known rocket equation
```math
m_{rocket}\frac{dv_{rocket}}{dt} + \frac{dm_{rocket}}{dt}v_{expel}=0
```
where the first term describes the acceleration of the rocket and the second term can be interpreted as the thrust force acting on the rocket.

Extending the equation above and considering also external forces results in the following balance of forces:
```math
F_{rocket} = F_{trust} + F_{gravitation} + F_{drag},
```
where 
- $F_{rocket}=m_{rocket}\frac{dv_{rocket}}{dt}$,
- $F_{thrust}=-v_{expel}\frac{dm_{rocket}}{dt}$,
- $F_{gravitation}=-m_{rocket}g$,
- $F_{drag}=-\frac{1}{2} \rho_{ambient} v_{rocket}^2 C_D A_{rocket}\frac{v_{rocket}}{|v_{rocket}|}$

Above, we assume quadratic drag for objects at high Reynolds numbers and, since for our simple setup, we do not expect the rocket to fly higher than a few meters, we can safely assume the gravitational acceleration to be constant. Inserting these relations obove, we obtain:
```math
m_{rocket}\frac{dv_{rocket}}{dt} = -v_{expel}\frac{dm_{rocket}}{dt} - m_{rocket}g - \frac{1}{2} \rho_{ambient} v_{rocket}^2 C_D A_{rocket}\frac{v_{rocket}}{|v_{rocket}|}.
```
The mass of the rocket consists of several contributions, $m_{rocket} = m_{hull}+m_{water}+m_{air}\approx m_{rocket} = m_{hull}+m_{water}$, where we neglect for simplicity the contribution from the pressurized air, which should be in the order of a few grams for a 1l bottle and pressures up to five bars. While the mass of the rocket hull is constant over time, the mass of the water is a function of time and can be expressed by the water density and the filling level:
```math
m_{water} = \rho_{water}V_{water}=\rho_{water}A_{rocket}h_{water}.
```

Further, also the expel velocity is a function of time, since the pressure in the bottle will decrease as more water or air is expelled from the bottle. 

In summary, we have the unknown variables $m_{water}$, $v_{rocket}$, $v_{expel}$, and several known variables such as the area of the bottle $A_{rocket}$, the mass of the bottle $m_{hull}$, or the ambient air density $\rho_{ambient}$.

#### Water Thrust ($p_{air} > p_{ambient}$, $h_{water} > 0$)
During the starting phase of the water rocket, the thrust is generated by the expelling water due to the high pressure in the bottle. However, the pressure will decrease over time, as more and more water is expeled from the bottle.

To relate the water pressure in the bottle to the pressure at the nozzle, the Bernoulli equation is applied:
```math
p_{water}+\frac{1}{2}\rho_{water} v_{sink}^2=p_{ambient} + \frac{1}{2}\rho_{water} v_{expel}^2
```
where $v_{sink}$ is the sinking velocity of the water in the bottle and $v_{expel}$ the expel velocity of the water at the nozzle. Here, we assume water to be incompressible ($\rho_{water} = const$) and neglect the hydrostatic pressure of the system. Further, we assume that the pressue at the nozzle is equal to the ambient air pressue. Above, we introduced two further unknown, $v_{sink}$ and $p_{water}$. To find a closing relation for $v_{sink}$, the mass conservation is applied:
```math
v_{sink}A_{rocket}=v_{expel}A_{expel}.
```
To determine the water pressure $p_{water}$, we assume that the water pressure is in thermodynamic equilibrium with the pressurized air, so that $p_{water}=p_{air}$ holds.

However, with sinking water level in the bottle, also the air pressure is lowering. To relate the air pressure at time $t$ to the initial air pressure at time $t=0$, we assume a polytropic process with the polytropic index $\gamma$:
```math
p_{air}(t)V_{air}^{\gamma}(t) = p_{air}(0)V_{air}^{\gamma}(0)
```

Combining all these relations, we obtain a set of equations for the rocket during the time when the expelling water is contributing to the thrust. However, two conditions must be met for the water thrust:
- $p_{air}(t) > p_{ambient}$
- $h_{water}(t) > 0$

If these two conditions are not fullfilled anymore, the water-thrust phase can be considered over.
In the case those two conditions are fulfilled, the Bernoulli equation together with the mass conservation leads to an expression for the expelling water velocity:
```math
v_{expel}(t) = \sqrt{\frac{2}{\rho_{water}}\frac{p_{air}(t)-p_{ambient}}{1 - \left(\frac{A_{expel}}{A_{rocket}}\right)^2} }
```
where
```math
p_{air}(t) = p_{air}(0)\left(\frac{L_{rocket}-h_{water}(0)}{L_{rocket}-h_{water}(t)}\right)^\gamma
```
Having this expression we can write the equations of motions:
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
\frac{dh_{water}}{dt} = -v_{sink} = -\frac{A_{expel}}{A_{rocket}} v_{expel}
```
Hence, the calculation scheme is the following:
1. Calculate the rocket's mass, $m_{rocket}(t)$
2. Calculate the pressure in the rocket, $p_{air}(t)$
3. Calculate the expelling velocity, $v_{expel}(t)$
4. Calculate the acceleration of the rocket, $\frac{dv_{rocket}}{dt}$
5. Calculate the velocity of the rocket, $\frac{dh_{rocket}}{dt}$
6. Calculate the sinking velocity of the water in the rocket, $\frac{dh_{water}}{dt}$

The calculations above are repeated until the conditions $p_{air}(t) > p_{ambient}$ or $h_{water}(t) > 0$ are violated.

#### Air Thrust ($p_{air} > p_{ambient}$, $h_{water} = 0$)

If the water level is zero either because all water is already expelled or because simply no water was filled into the rocket, we can still have a thrust force due to the higher air pressure inside the rocket than outside. Hence the equation of motion is the same as in the case above, only the equations for the medium in the rocket are somewhat different.

Recalling the thrust force, $F_{thrust}=-v_{expel}\frac{dm_{rocket}}{dt}$, we need an equation for the mass change of the rocket.

The mass of the rocket containing no water is given as $m_{rocket} = m_{hull}+m_{air}$. The only time dependant mass is the $m_{air}$ which can be written as 
```math
\frac{dm_{air}}{dt} = \frac{d}{dt} (V_{air}\rho_{air})=V_{rocket}\frac{d\rho_{air}}{dt} 
```
Recalling the integral version of the continuity equation, $\int_V \frac{\partial\rho}{\partial t} dV = \int_S \rho \mathbf{v} \mathbf{n} dS$, we find an expression for the derivative of the air density:
```math
V_{rocket}\frac{d\rho_{air}}{dt}= \rho_{air}v_{expel}A_{expel}
```
Similar as before, we use the energy consevation (Bernoulli) to relate the pressure to the expelling velocity
```math
p_{air}=p_{ambient}+\frac{1}{2}\rho_{air}v_{expel}^2
```
leading to 
```math
v_{expel}(t) = \sqrt{\frac{2}{\rho_{air}(t)}(p_{air}(t)-p_{ambient}) }
```
To find an equation relating the air pressure and the air density we assume in this case the air to be an ideal gas. The ideal gas equation of state can be written as:
```math
p_{air}=\rho_{air}R_{spec,air}T_{air},
```
where $R_{spec,air}$ is the specific gas constant of air and $T_{air}$ the air temperature, which we assume to be equal the ambient temperature $T_{air}=T_{ambient}$.

In sumary, we can again obtain an expression for the expelling velocity:
```math
v_{expel}(t) = \sqrt{\frac{2}{\rho_{air}(t)}(\rho_{air}(t)R_{spec,air}T_{air}-p_{ambient}) }
```
a differential equation for the air density
```math
\frac{d\rho_{air}}{dt}= \rho_{air}v_{expel}\frac{A_{expel}}{A_{rocket}L_{rocket}}
```
and the equation of motion of the rocket is a bit different
```math
\frac{dv_{rocket}}{dt} = A_{expell} \rho_{air}(t) \frac{v_{expell}^2(t)}{m_{rocket}(t)} - g - \frac{1}{2} \frac{\rho_{ambient}}{m_{rocket}(t)} v_{rocket}^2(t) C_D A_{rocket}\frac{v_{rocket}(t)}{|v_{rocket}(t)|}.
```

Hence, the calculation scheme is the following:

1. (Only once, when the condition $h_{water} < 0$ is met first, calculate $\rho_{air}(t_0)$ at that point in time $t_0$ using the ideal gas equation.)
2. Calculate the rocket's mass, $m_{rocket}(t)=m_{hull}+\rho_{air}(t)V_{rocket}$
3. Calculate the expelling velocity, $v_{expel}(t)$
4. Calculate the acceleration of the rocket, $\frac{dv_{rocket}}{dt}$
5. Calculate the velocity of the rocket, $\frac{dh_{rocket}}{dt}$
6. Calculate the air density change, $\frac{d\rho_{air}}{dt}$

The calculations above are repeated until the condition $p_{air}(t) > p_{ambient}$ is violated.

#### Ballistic Motion ($p_{air} = p_{ambient}$)

When the presure in the rocket equals the ambient pressure, the rocket undergoes a simple ballistic motion
```math
m_{rocket}\frac{dv_{rocket}}{dt} = - m_{rocket}g - \frac{1}{2} \rho_{ambient} v_{rocket}^2 C_D A_{rocket}\frac{v_{rocket}}{|v_{rocket}|}.
```

## Limitations

Many!

- rocket is basically reduced to a point model
- no tumbling
- "cylindical" shape of rocket
- laminar flow
- air = ideal gas
- viscosity and friction of water neglected
- ...

## Plots

![Trajectory of simple 1L water rocket.](trajectory.png)

![Maximum height reached for different air pressures and filling levels of water.](max_heights.png)
