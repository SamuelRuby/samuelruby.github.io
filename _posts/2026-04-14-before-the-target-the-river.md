---
title: Before the Target, the River 
date: 2026-04-14
layout: post
---



Before getting into fluid Physics, I’d like to revisit a [thread of thought](https://samuelruby.github.io/2026/04/10/the-very-first-sin.html) I opened earlier, where I said ‘We can’t just keep getting better at fighting fires - they keep coming. We also have to build a smoke detector’. Medicine, for all its brilliance, developed extraordinary treatment infrastructure but they looked over equally developing monitoring infrastructure, the ability to know what is happening inside the body before it becomes a full-blown crisis.

In Data & ML, deployment is never the end of the pipeline. It’s the beginning of monitoring. We don’t just ship a model and pray, hoping for the best. Monitoring is built around it — data drift detection, performance degradation alerts, anomaly flags, distribution shift tracking,....... the whole shebangs. This is cuz we know that once you stop seeing a system, you’ve already lost it. Intervention is impossible without visibility.

Biology is a deployed system. The most complex one we know of. And for most of human history, we've been trying to patch and fix without being able to see inside it in real time, at the scale where things actually go wrong.

That's the gap. And that's what this whole project is trying to address. Starting, necessarily, with understanding the environment the nanobots has to navigate. Because before one can monitor anything, we have to learn to survive a host of barriers, principal of which is fluid dynamics, or as I like to call it ‘The River’.
                                
          “Water is the driving force of all nature”
                  —Leonardo da Vinci




Researchers — most of them with deep biological training, years in wet and dry labs, careers built around understanding living systems from the inside — have been working on micro and nanoscale machines for decades. They have navigated nanomotors through biological fluids, engineered DNA origami structures at gigadalton scales, built enzyme-powered systems that move autonomously, and sent things through blood that we would not have thought possible fifteen years ago. One group even described their device as a "nanovoyager in human blood",  which, if you ask me, is a befitting title.

Because of that foundation, we’re not starting from scratch. We’re building on a lineage, so to speak, but also stepping sideways from it. Now we can approach the same terrain from a different angle. Where earlier generations focused on biological ingenuity alone, we can now pair biological media with computational intelligence. The result is something hybrid by design: systems that don’t just move through the body, but interpret it.
                   
     "If we stand tall, it is because we stand on the 
                 shoulders of many ancestors."   
                                         - African proverb



## The Architecture of Flow
What we’ve worked on, up to this point, is nanobots in blood flow through the cardiovascular system. So, we have to talk about it

Fluid transport is a biological imperative, it being one of the most conserved functions in complex life. Across plants, fungi, and animals, evolution repeatedly converges on the same solution: pumping, circulating, transporting, draining.

The human vascular system is not a single environment. It is a hierarchy of environments, each with its own physics, its own rules, its own demands on anything trying to move through it. We have the closed loop cardiovascular system: arteries, arterioles, capillaries, venules, veins. Running alongside it is the lymphatic system, which acts as a complementary, one-way network of vessels that returns excess interstitial fluid, macromolecules, and immune cargo and returns them to the bloodstream. If the bloodstream is the highway, the lymphatics are the network of backroads, checkpoints, and cul‑de‑sacs.  Together, they  create a layered landscape of shear forces, pressures, viscosities, and biological checkpoints that anything moving through the body – engineered or otherwise– has to contend with.

The aorta sits at the top of the cardiovascular system, roughly about 25 millimeters (mm) in diameter, carrying blood directly from the heart. Below it, large arteries around 4mm. Then small arteries at 1mm. Arterioles at 30 (μm). Then capillaries, about 5 to 10μm — the smallest vessels in the body, so narrow that red blood cells, which are 7 to 8μm in diameter, pass through one at a time, physically deforming to squeeze through. Blood flow velocity across this hierarchy ranges from 100 to 600 mm/s in arteries, dropping significantly as vessels narrow. A nanobot, sized between 100 and 1000 nanometres (nm), sits comfortably below capillary diameter. Perfectly small enough to move freely without deforming. 

For a nanobot, right from the point of entry, the physics around it keeps changing. Turbulent in the aorta, shear‑dominated in arterioles, viscosity‑ruled in capillaries. Also at that scale, Brownian motion — the random thermal jitter of fluid molecules constantly colliding with everything around them — becomes one of the dominant forces on the system. Why that size, you might wonder. Well, DNA origami nanorobots in literature operate between size ranges of 50 to 200nm, also liposomes used in drug delivery have been validated to work between 50 to 500nm.

The therapeutic effect of the nanobot can only manifest when it gets to its target. Introduced via IV injection into a peripheral vein, it travels to the heart, gets pushed into the aorta, and from there is distributed throughout the body. Which means the first thing it encounters is not going to be a quiet capillary somewhere (which might probably be its site of action), but the aorta. And this is where the physics gets complicated– not merely because the aorta is turbulent, which we’ll get to, but because of the nature of blood itself. 

You see, blood is not a simple fluid, It is a dense suspension of cells (red blood cells, along with platelets and white blood cells) suspended in plasma. In large vessels, where the vessel diameter is much greater than the size of individual cells, you can model blood as a uniform newtonian fluid and get away with it. But as vessels narrow and diameter approaches the size of the cells themselves, that approximation breaks down. Their motion, their deformation, their interaction with vessel walls — all of that becomes part of the physics. Setting the cells composition aside, blood is a non-Newtonian fluid. In a Newtonian fluid like water, viscosity is constant (meaning it does not change with how fast you stir it). However, blood’s viscosity changes with shear rate. In fast flow, blood thins. In slow flow, it thickens. Near vessel walls, where the velocity gradient is steepest, the viscosity a nanobot experiences is different from what it experiences at the centreline.  Which is exactly why, before building anything, one has to understand the fluid mechanics first, and then actually model it, verify it, and see what the physics does.


### Laminar vs Turbulent
All this talk about fluid, perfect time to introduce the 2 regimes of fluid flow: 
- Laminar flow: smooth and orderly flow. Think honey pouring slowly. The fluid moves in parallel layers, each sliding past the next without mixing. You can describe it with clean equations. You can predict exactly where a particle will be 
- Turbulent flow: chaotic, swirling, full of eddies and vortices —unpredictable structures that form and collapse and reform. Think of rapids in a river, or the wake behind a fast-moving boat. The fluid mixes violently with itself. Particles get thrown in directions you cannot simply calculate.

So, how do we know which regime we're in? The Reynolds number
<!-- ![Reynolds Number](/assets/images/What-Is-Reynolds-Number.png) -->
 
<figure>
  <img src="/assets/images/What-Is-Reynolds-Number.png" alt="Laminar vs turbulent flow">
  <figcaption>Fig 1: Laminar vs turbulent flow. </figcaption>
</figure>


<figure>
  <img src="/assets/images/reynolds number.png" alt="Reynolds number formula"> 
  <figcaption>Fig 2: The Reynolds formula.</figcaption>
</figure>
                        
  *For blood, fluid density  ~1060 kg/m³, and dynamic viscosity ~0.003 Pa·s*

> **Note:**
* ** Re** < 2300 → Laminar
* ** Re** > 4000 → Turbulent
* 2300–4000 → Transitional

In blood vessels specifically, the heart's pumping action means the Reynolds number oscillates throughout each cardiac cycle. During systole, when the heart contracts and blood surges, *Re* spikes, sometimes into **transitional** or **turbulent** territory. During diastole, when the heart relaxes, it drops back towards **laminar**. 
Transitional flow tends to appear in the places where the geometry forces the blood to accelerate, decelerate, or change direction, for example, <u>the aortic root, the ascending aorta, the carotid bifurcation, the branching points of major arteries, and regions downstream of stenoses</u>. These are the zones where Re is high enough (local Reynolds numbers exceed ~2000 during systole and secondary flows --Dean vortices, helical structures-- begin to form)  and the vessel shape is so complex that the flow cannot stay purely laminar, but not chaotic enough to be fully turbulent.

So, if we run the numbers, we get:
* **Capillaries:** Re ≈ 0.01. Deeply, serenely laminar.
* **Small arteries:** Re ≈ 100. Still laminar.
* **Aorta:** Re ≈ 4000–5000. Turbulent, especially during systole.

A great mental model I've been using is :
* **Aorta (Highway):** Turbulent, high-speed, chaotic.
* **Small arteries (Neighbourhood streets):** Laminar, predictable.
* **Capillaries (Alleyways):** Ultra-laminar, almost still.
    

The nanobot's critical work of targeting, sensing, treatment, all happens in the neighbourhood streets and alleyways. Which means my simulation starts where the physics becomes tractable right after the chaos of the aorta, once the nanobot has been distributed into the smaller vessels network. 
**Turbulent flow, for the record, requires solving the full Navier-Stokes equations, a computationally expensive operation that I will talk about in coming notes.**


### The shape of flow — Poiseuille's equation

Inside a small blood vessel (helps to visualize this as a roughly cylindrical tube), flow driven by pressure takes a very specific shape. It is not uniform. The fluid at the very centre of the vessel moves fastest, while the fluid at the wall moves slowest, technically reaching zero velocity right at the wall surface. The transition between those two extremes follows like a parabola.
<figure>
  <img src="/assets/images/Poiseuille Flow Velcity Profile.png" alt="Poiseuille Flow Velcity Profile"> 
  <figcaption> Fig 3: The parabolic velocity profile across a small blood vessel (radius 0.5mm). Velocity peaks at the centreline (~360 mm/s) and falls to exactly zero at the vessel walls.</figcaption>
</figure>

 Jean Léonard Marie Poiseuille and Gotthilf Heinrich Ludwig Hagen solved this and gave us the Hagen-Poiseuille equation.
 
<figure>
  <img src="/assets/images/Poiseuille Flow.png" alt="Hagen-Poiseuille equation"> 
  <figcaption> The Hagen-Poiseuille equation.</figcaption>
</figure>

Where:

* **r:** radial position (distance from centre)
* **R:** vessel radius
* **v_max:** maximum velocity at centreline = ΔP·R² / 4μL
* **ΔP:** pressure difference driving flow
* **L:** vessel length
* **μ:** dynamic viscosity


* At r = 0 (centre): v = v_max
* At r = R (wall): v = 0

**The wall velocity being zero is called the *no-slip condition*, that is the fluid layer immediately in contact with the vessel wall that sticks to it**.


Now, in an ideal world where there is steady, laminar, pressure-driven flow through a perfect tube, blood mechanics will be calculated with Poiseuille flow, and everything will be well and good. However, Blood rarely behaves this neatly. It is pulsatile, non‑Newtonian, and shaped by branching, curvature, and compliance. So Poiseuille flow is the baseline that shows us just how far real hemodynamics diverges from the ideal. Why does this matter? Because where the nanobot sits in the vessel determines how fast it moves. A nanobot near the centre gets carried quickly -- useful if you want rapid distribution. A nanobot near the wall barely moves under flow alone. And near the wall, other physics are at play.

Recall earlier, we said blood in non-newtonian and poiseuille flow is used to model newtonian fluids, so why then do we use it for biological fluids? 
3 reasons:
* Large arteries behave approx. newtonian: In big vessels the shear rates are high, and viscosity becomes nearly constant. So blood flow here is acting nearly newtonian enough that Poiseuille assumptions aren’t catastrophic.
* Poiseuille flow gives a baseline that tells us what the flow should look like, what the velocity profile should look like, how pressure drop should scale, etc.. We compare all that to real blood, and then we see how it deviates (which can then be added as corrections (Fåhræus–Lindqvist effect, etc.)
* Full hemodynamic modeling requires Navier–Stokes, non‑Newtonian constitutive laws, plasma–cell interactions, vessel wall elasticity, pulsatile boundary conditions, etc… all CFD supercomputer territory



### Stokes drag
As mentioned earlier, at nanoscale, viscosity dominates. At human scale, if you jump into a swimming pool, inertia carries you. You coast. At nanobot scale, there is no coasting. The moment you stop applying force, you stop. The fluid's viscosity — its resistance to flow, its internal friction — overwhelms inertia completely. The relevant force here is **Stokes drag**. We can define it as the resistive force a small sphere feels as it moves through a viscous fluid.

<figure>
  <img src="/assets/images/stokes law.jpg" alt="Stokes' Law"> 
  <figcaption>Fig 4: Stokes' Law.</figcaption>
</figure>

so:
* If nanobot moves faster than fluid --> drag will slow down, and
* if it moves slower than fluid, possibly a viscous fluid  --> drag will speed up


**SO** it goes without saying that Nanobot has to match velocity of fluid
               ** Relaxation time τ (time it takes for the nanobot to match the velocity of the surrounding fluid after being placed in it)
                     > τ = m/6πμr
* For a nanobot with a radius r of 100nm, and with a fluid the density of water, τ ≈ 2.2 nanoseconds. 
* But the same nanobot in blood, τ ≈ 0.6 – 0.8 nanoseconds

So compared to water (~2 ns), blood pulls the nanobot into the local fluid velocity even faster, and as it is sucked into this velocity, becomes part of the flow. 



What this means physically: a nanobot placed in flowing blood almost immediately reaches the local fluid velocity at its position. And as it becomes part of the flow, all the nanobot have to do is survive the journey and know when to switch modes.

Simulation confirms this. A single nanobot placed slightly off-centre, given zero initial velocity, accelerated to the local Poiseuille velocity within the first timestep.

<figure>
  <img src="/assets/images/plot_single_nanobot_trajectory.png" alt="Single Nanobot Trajectory + Velocity vs Time">
  <figcaption>
    <strong>Fig 5: Single Nanobot Trajectory + Velocity vs Time.</strong><br>
    <em>Top:</em> nanobot path through the vessel — straight line at constant radial position, carried by the flow. 
    <em>Bottom:</em> nanobot velocity snaps to the local blood velocity almost instantly, then holds steady. 
    That spike at t=0 and immediate plateau is the Stokes drag doing exactly what the physics predicts. 
    τ is so small it's barely visible on this timescale.
  </figcaption>
</figure>

### BROWNIAN MOTION
Fluid molecules are never still. At any temperature above absolute zero, they are constantly jiggling — vibrating, colliding, bouncing off each other and off anything else in their path. A nanobot in blood will certainly be surrounded by billions of these collisions every microsecond, with most of them canceling out. The net result is then a random, persistent displacement — a jitter superimposed on whatever the flow is doing. At nanoscale, brownian motion isn't negligible and is actually one of the dominant forces

<figure>
  <img src="/assets/images/brownian motion image.png" alt="brownian motion"> 
  <figcaption>Fig 6.1: Brownian motion.</figcaption>
</figure>



The 1D probability of a particle to make a displacement x in a certain drection, during a time t, in a medium whose diffusion coefficient D, is given by

<figure>
  <img src="/assets/images/brownian .jpg" alt="Brownian Motion Formula"> 
  <figcaption>Fig 6.2: Brownian Motion Formula.</figcaption>
</figure>


### Brownian Motion — Einstein-Smoluchowski

The diffusion coefficient $D$ is defined by the Stokes-Einstein equation:

$$D = \frac{k_B T}{6\pi \mu r}$$

**Where:**
* $k_B$ — Boltzmann constant = $1.38 \times 10^{-23} \, \text{J/K}$
* $T$ — temperature = $310 \, \text{K}$ (body temperature, 37°C)
* $\mu$ — viscosity = $3.5 \times 10^{-3} \, \text{Pa}\cdot\text{s}$
* $r$ — nanobot radius = $100 \, \text{nm}$

**Result:**
$$D \approx 6.48 \times 10^{-13} \, \text{m}^2/\text{s}$$

The random displacement per timestep is modeled as $\Delta x \sim N(0, \sqrt{2D\Delta t})$. 
At $\Delta t = 0.001 \, \text{s}$, the displacement standard deviation is $\sigma \approx 36 \, \text{nm}$ per millisecond.


Thirty-six nanometres per millisecond! In a small artery with a radius of 500 micrometres, that is essentially invisible, as flow dominates completely. But in a capillary of radius 5 micrometres, this is significant. It is more than half a percent of the vessel radius per timestep. And over time, it adds up to a visible random walk.

This scale-dependence is one of the more interesting things, and I want to share it directly because I think it is more intuitive as a picture than as a number.

<figure>
  <img src="assets/images/plot heatmap brownian.png" alt="Nanobot on Velocity Heatmap + Brownian Radial Exploration"> 
  <figcaption>Fig 7: Nanobot on Velocity Heatmap + Brownian Radial Exploration.</figcaption>
</figure>

      DESC: 
      Top: the parabolic velocity field rendered as a heatmap — red at the fast-moving centre, blue near the slow walls. The nanobot's path is the black line overlaid. It travels straight, carried by the flow. Bottom: radial position over time. Brownian motion is present but in a small artery it is effectively invisible — the line is nearly flat. This is correct. This is what the physics says should happen here.


<figure>
  <img src="assets/images/plot heatmap brownian.png" alt="Nanobot on Velocity Heatmap + Brownian Radial Exploration">
  <figcaption><b>Fig 7:</b> Nanobot on Velocity Heatmap + Brownian Radial Exploration.</figcaption>
</figure>

**Technical Breakdown
* Velocity Heatmap (Top): Represents the parabolic velocity profile characteristic of laminar (Hagen-Poiseuille) flow. red at the fast-moving centre, blue near the slow walls. The nanobot's path is the black line overlaid. It travels straight, carried by the flow.
* Radial Displacement (Bottom): Nanobot's radial position over time. Brownian motion is present but in a small artery it is effectively invisible, as indicated by the near-flat profile


Up till now, we have spoken about 1 single nanobot, but what we’re actually aiming for is a swarm — millions of coordinated, distributed, collectively working nanobots. So the next question becomes: What does the Poiseuille profile do to a group?

Releasing 20 nanobots simultaneously, placed at different radial positions across the vessel, we see something like this:

<figure>
  <img src="assets/images/swarm of 20 Nanobots.png" alt="Swarm of 20 Nanobots: Dispersion by Blood Flow + Brownian Motion"> 
  <figcaption>
    <strong>Fig 8: Swarm of 20 Nanobots: Dispersion by Blood Flow + Brownian Motion.</strong><br>
    <em>The ones near the centre, in the fast red zone race ahead. 
    <em>The ones near the walls, in the slow blue zone lag behind. 
     As you can see, the velocity field disperses the swarm spatially, which has implications for how swarm coordination needs to work: nanobots released together will not stay together.                       Collective behaviour has to be designed for separation, not assumed proximity.
  </figcaption>
</figure>

          
### Three vessels, three physics regimes
The same nanobot passing through three different vessel environments — a small artery, a capillary, and an artificially exaggerated Brownian scenario (built purely for visual purposes, to make the jitter visible at a scale it wouldn't normally reach) shows the scale-dependence more visibly.

<img width="1589" height="1190" alt="ANIMATED COMPARISON in capillaries, arteries and exag brownian" src="https://github.com/user-attachments/assets/90f01b58-156d-4a71-8a2b-82c38b70892d" />

<figure>
  <img src="assets/images/swarm of 20 Nanobots.png" alt="Swarm of 20 Nanobots: Dispersion by Blood Flow + Brownian Motion"> 
  <figcaption>
    <strong>Fig 9: Three Scenarios Side by Side (Static).</strong><br>
    <em>Left column:</em> Nanobot trajectory in each vessel type. 
    <em>Right column:</em> Radial position over time, showing Brownian fluctuations.
    <em>Small artery (top):</em> Brownian motion is negligible (36nm per millisecond — but against a 500μm vessel radius it registers as nothing).
    <em>Capillary (middle):</em> the wiggly green line, Brownian motion is more noticeable. Here the vessel is 5μm across, and the same 36nm Brownian kick is now a real force. Due to slow flow, dominant
    viscosity, extremely low Re, and relatively larger diffusion coefficient
    Exaggerated Brownian (bottom)
  </figcaption>
</figure>
      
## What comes next

Phase 1 established the fundamentals. The parabola is real. The drag is instantaneous. Brownian motion is scale-dependent and matters in capillaries. The swarm disperses with the flow.

Phase 2 moves into three dimensions. Cylindrical vessels, Y-shaped bifurcations, the question of how a nanobot chooses which branch to take. The answer, at least in biology, involves chemical gradients. Every disease, every tumour, every site of infection has a molecular signature — a concentration field that diffuses outward from the source. How to model that will be a topic of discussion in a coming post.




