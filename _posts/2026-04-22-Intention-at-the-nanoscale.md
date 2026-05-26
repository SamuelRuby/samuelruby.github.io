---
title: "3. Intention at the Nanoscale"
date: 2026-04-22
layout: post
---

While we have touched-on the [human-medium fluid dynamics landscape](https://samuelruby.github.io/2026/04/20/before-the-target-the-river.html), we are yet to talk about **'navigation'**. How does a mission-driven nanobot find its target?

Navigation, here, operates in 2 distinct hybrid modes: **Passive** and **Active**. In Passive navigation, the nanobot drifts with blood flow, using the turbulent flow of large vessels to conserve energy, while its location sensors remain active. On the other hand, in active navigation, when a chemical signal crosses a detection threshold, the nanobot, which until that moment was a passenger, becomes a pilot.

That switch from passive to active, is what I mean by **intention at the nanoscale**.


### The Switching Logic: Hybrid Navigation

Before the nanobot enters the body, we already know quite a lot- well, usually! An MRI/PET/X-ray/CT scan has told us where the tumour is, at the organ or region level. The left iliac crest bone marrow. The upper lobe of the right lung. A specific quadrant of the liver, etc. That macro-map of information gets loaded into every nanobot before injection, something like a GPS route. It is not exactly turn-by-turn at the molecular level, but enough to know the destination neighbourhood.

What happens after injection is a three-layer problem, each of which layer requires a different kind of intelligence.

**Layer 1**: passive drift with steering. In large vessels — like the aorta and major arteries — blood flow moves at 30 to 40cm/s (centimetres per second). It'll make no sense for a nanobot to flow against that. It would most likely burn its entire energy budget going nowhere. So here, it *goes with the flow*. And just like a kayaker on a fast river (cannot paddle upstream, but can angle yourself to exit at the right tributary), the nanobot reads the pre-loaded vascular map, recognises branch points as they approach, and makes micro-adjustments — small flagellar nudges, or magnetic steering if externally assisted — to take the correct branch. Landmark chemistry helps confirm position: oxygen gradients, vessel wall markers, the molecular signature of entering a new organ bed, etc. 

**Layer 2**: active-assisted navigation. In mid-range vessels like arterioles and venules, flow slows enough for the nanobot to participate a bit more actively. The macro-map is still relevant, and we're getting somewhat closer. The nanobot is in the right region; it just needs to find the right street. Chemical gradients from the target tissue begin to be detectable. Shear stress at the vessel wall starts to drop. The nanobot increases thrust, begins following the gradient more deliberately, confirming its position against the expected anatomy.

**Layer 3**: terminal micro-navigation. In capillaries, flow drops to around one millimetre per second. Active swimming is dominant. The pre-loaded map cannot help here cuz clinical MRI resolves to about one millimetre, and the nanobot is now operating at the micrometre scale, where healthy and malignant cells sit side by side, indistinguishable from above. Only local molecular sensing can identify the target now: cancer cell surface antigens, metabolic fingerprints, abnormal pH, the specific combination of markers, etc. This is exactly where the switching logic I described earlier comes in. Here, the nanobot switches to active navigation: chemotaxis, surface marker recognition, gradient following.

The switching between these layers is triggered by a combination of 3 things: 
* local flow sensing (shear stress drops below a threshold, signalling a smaller vessel),
* chemical landmark detection (the nanobot has entered the target organ's signature chemistry), and
* in the full swarm system, a consensus among multiple nanobots that the region has been reached.

The simulation in this post models the worker caste (nanobots that have identified the problem, and therefore has a finite mission, either a payload to deliver or a treatment to execute). Workers are the simplest navigation case: get there, do the thing, disengage, share treatment knowledge amongst circulating bots, then degrade.

But not every nanobot in the system are workers. Some patrol without a target, sampling for signals we did not know to look for during analysis: metastatic cells below imaging resolution, early infection signatures, anomalies that no scan/imaging caught, etc. Others hold position around treatment sites. Others start the repair process. There are a host of other castes, which we cannot cover exhaustively in this post. But know that each of these roles uses the same underlying physics — large vessel passive, small vessel active — however with entirely different goals, triggers, and termination conditions. I guess what I'm trying to say is that the navigation doctrine is *caste-dependent.*


With this navigation logic in mind, what does it mean to swim at this scale.

### Swim, Swim...

Knowing where to go is one thing. Getting there is another. And at the nanoscale, the question is **"what kind of motion is even physically possible here?"** 

The constraint comes from the physics of the environment. We established in the last post that blood flow in small vessels is laminar: smooth, orderly, dominated by viscosity. What we did not fully explore is what viscosity dominance means for an object trying to move *through* that fluid under its own power, rather than being carried by it. Something really discussed in the [E.M Purcell paper.](https://www.damtp.cam.ac.uk/user/tong/fluids/lowreynolds.pdf)

At nanoscale, the Reynolds number for a swimming nanobot is approximately 10⁻⁶. Really really low!. At this scale, viscosity is so dominant that t effectively erases the concept of momentum. There is no coasting. There is no glide. Stop propelling, you stop moving — instantaneously and completely. The environment effectively resets around you with every timestep. This is the physical reality of the **scallop theorem**; without coasting, the nanobot cannot rely on past momentum or inherited inertia and must instead execute a non-stop sequence of real-time decisions to achieve navigation.

This is the design constraint. 

<figure>
  <img src="/assets/images/nav_reynolds_comparison.png" alt="Reynolds Numbers Across Swimming Scales + Stopping Distance">
  <figcaption>
    <strong>Fig 1: Reynolds Numbers Across Swimming Scales + Stopping Distance.</strong><br>
    <em>Left:</em> Reynolds numbers across biological and engineered swimmers, on a log scale. 
    <em>Right:</em> stopping distance after ceasing propulsion. A human swimmer coasts ~1,856 metres. 
    A nanobot stops in 0.00nm. 
  </figcaption>
</figure>


So reciprocal motion is out. What works however, inspired by biology, is non-reciprocal motion: one that traces a path through space that is not the same forward and backward. The bacterial flagellum, the rotating helical tail used by E. coli and many other bacteria, is non-reciprocal by design. It rotates continuously in one direction, like a corkscrew, generating thrust via a travelling wave. Sperm cells use a similar principle: a whipping tail that propagates a wave from base to tip, generating net forward motion.

So inspired by this, we make a flagellum-like mechanism that generates thrust in a direction determined by the navigation controller. Without modeling the full hydrodynamics of a rotating helical filament, a simplified power-to-thrust scaling relation can provide a rough estimate of thrust. Often used in bio-motility modeling, this relation treats the flagellum as a propulsion system where thrust is proportional to motor power, scaled by an efficiency factor, and is given by...

![Flagellar Thrust — Simplified Model](/assets/images/Flagellar Thrust — Simplified Model.png)

Where:
* **η** — motor efficiency (rotation → forward motion)
* **P_motor** — motor power

Assumptions being made here are:
  * flagellum always available, thrust instantaneous, efficiency η = 1.0, direction changes instantly
  * Energy cost: proportional to thrust magnitude × velocity

These assumptions make the simulation tractable. And also make it unrealistic in ways like, how real flagella have rotational dynamics, run-and-tumble behaviour, energy depletion, mechanical wear, etc. All of that which we dealt with later on.

Earlier, I touched on how most of our ideas, is us learning from the natural world. Biology has been running its own form of *"computation"* for approximately 3.5 billion years, and nowhere is this more apparent than in the concept of *gradient-following*. In computer science, we call this *gradient descent*.

Many organisms perform behaviors that are mathematically equivalent to *"gradient descent"*. That is, they sense a signal, compare it to a moment ago or a nearby location, and bias their movement toward "improvement". This happens all over biology, from immune cells to slime molds to sperm cells to neurons.

### The Logic of the Descent
In machine learning however, gradient descent is an iterative optimization process. You measure the error (loss) at your current position in parameter space, calculate the gradient (the direction of steepest increase), and then step in the opposite direction. You don’t need to see the entire "landscape" of the problem; you only need to know which way is downhill from where you are. Biology operates on this same principle, albeit using vastly different hardware:

* E. coli: Despite lacking a nervous system, uses chemotaxis to find food. It measures the concentration of glucose molecules, compares that to its state a moment ago, and biases its movement toward the highest concentration. If conditions improve, it keeps going; if they worsen, it "tumbles" to reorient randomly and tries again.
* Immune and Sperm Cells: Neutrophils chase bacteria by following chemokine gradients ( (IL‑8, fMLP, C5a). **A literal biochemical hill-climbing algorithm!** Similarly, human sperm cells use calcium-mediated chemotaxis to follow progesterone gradients released by the egg, adjusting their flagellar beat to swim "uphill" toward their target.
* Cellular Development and Motion: Fibroblasts use durotaxis to follow stiffness gradients, while B cells navigate lymph nodes by following chemokine signals (like CXCL13, CCL21 and CCL19) to optimize antigen encounters. Even the growth cones of developing neurons navigate toward targets by sampling chemical gradients  of netrin, semaphorin, ephrin and NGF, and extending microtubules in the direction of the highest concentration.
* Slime mold (Dictyostelium discoideum), which has been the gold standard of multicellular chemotaxis since forever, do this too. A  colony of individual mold self-organizes into a multicellular organism when starving by following cAMP waves. An awesome example of biological consensus algorithm - distributed optimization with no central controller .

At a molecular scale, even inside the cell, molecular motors like dynein and kinesin follow gradients of ATP concentration, microtubule post-translational modifications and mechanical tension. Even metastatic cancer cells utilize these same tactics, following gradients of oxygen, nutrients, ECM stiffness and chemokines (CXCL12) to navigate toward more favorable microenvironments.
 
When we map these biological behaviors onto the framework of machine learning, we can see the same underlying logic, but executed with:
* Receptors instead of digital sensors
* Molecular concentration fields instead of abstract parameter spaces
* Phosphorylation cascades instead of arithmetic
* Brownian motion instead of random initialization
* Actin polymerization instead of vector updates


This is what I mean when I say **biological ingenuity paired with computational intelligence**. Biology tells us the mechanism is physically possible and evolutionarily validated, and computation tells us how to formalise it, optimise it, and make it programmable.

The chemical gradient field the nanobot navigates is modelled using **a simplified diffusion- decay profile.** When a molecule diffuses while simultaneously being removed, its steady‑state concentration falls off exponentially. This behavior is captured by the radial exponential‑decay solution to the diffusion–decay equation:
![diffusion‑decay equation.png](/assets/images/diffusion‑decay equation.png)

Solving this differential equation for a 3D space, gives us the exponential decay function where C is given as:

![radial exponential decay solution](/assets/images/radial exponential decay solution.png)

Where:

𝐷 = diffusion coefficient

𝜏 = decay time constant

𝜆 = √(Dτ) — diffusion/decay length, the characteristic distance over which the signal fades (also called the attenuation length).

C(r) — concentration at distance r from source

C₀ — concentration right at source (e.g. tumour, infection site)

  * Signal diffuses isotropically outward from source.
  * Nanobot measures local C(r) at current position.
  * Gradient ∇C tells it: which direction is uphill from here?

The target — a tumour, an infection site, a malfunctioning cell cluster — continuously releases molecules: metabolites, antigens, cytokines. These diffuse outward through surrounding tissue and blood, creating a steady-state concentration field that is highest at the source and decays with distance. The nanobot reads this field, and this field points towards the tumour.

Let's visualise this field. We'll put a target at a specific (x, y) position in the vessel to diffuse its signal isotropically. As the nanobot moves, its sensors measure the local concentration and gradient at its current position. This environment is mapped out in the chemical heatmap and gradient vector field image below.

<figure>
  <img src="/assets/images/NAV_chemical gradient.png" alt="Chemical Concentration Field AND Gradient Vector Field">
  <figcaption>
    <strong>Fig 2: Chemical Concentration Field AND Gradient Vector Field.</strong><br>
    <em>Left:</em> the concentration heatmap. Red area is target area, target is marked with a red star. The concentric contours show the exponential decay from source outward.
    <em>Right:</em> the gradient vector field. Every arrow points toward the target. From anywhere in the vessel, the nanobot can measure local concentration, compare it to a moment ago, and follow the arrow.
  </figcaption>
</figure>


With this navigation algorithm in place, the next question is: Does it actually work? Well, depends entirely on where the nanobot is. I ran the same simulation in two vessel environments; a small artery and a capillary, ad got different outcomes.


<figure>
  <img src="/assets/images/nav_artery vs capilary.png" alt="Active Navigation: Artery vs Capillary">
  <figcaption>
    <strong>Fig 3: Active Navigation: Artery vs Capillary.</strong><br>
    <em>Top row:</em> Artery. The blue trajectory shows nanobot being carried by flow, never reaching the target (red star). Distance plot climbs linearly after an initial brief approach
    <em>Bottom row:</em> Capillary. The green trajectory is wiggly (Brownian motion is visible at this scale), but the nanobot navigates to within 0.1μm of the target before being carried past
  </figcaption>
</figure>

A nanobot small enough to carry meaningful payload cannot generate enough thrust to overcome high-velocity arterial flow, so it will rely entirely on passive transport for systemic distribution. Active navigation is strictly reserved for the capillary environment, where flow fluid velocities drop low enough for the bot's micro-propulsion to effectively steer.

In the capillary simulation, the nanobot successfully navigates to within fractions of a micrometre of the target, touches it, and then boom, gets carried past it by the flow. From the capillary's distance plot,  we see a sharp drop to near-zero, then a climb as the nanobot is swept downstream. So, reaching the target is one problem. Staying there is another. A *"reached but drifted"* problem.

**The solution**? Hovering mode. The nanobot reaches within the defined proximity of the target, the so-called 'hover threshold', and switches from navigation mode to active position maintenance.

<figure>
  <img src="/assets/images/nav_hovering.png" alt="Without and With Hovering">
  <figcaption>
    <strong>Fig 4: Without and With Hovering.</strong><br>
    <em>Top row:</em> without hovering. The nanobot reaches the target (distance drops to near-zero) then is immediately carried past.
    <em>Bottom row:</em> with hovering. At t = 0.11s, the nanobot enters the hover threshold (orange dashed circle). Navigation mode hands off to hover mode.
  </figcaption>
</figure>

However, Inasmuch as the nanobot can hold position when it needs to, hovering is energy-intensive. This means the nanobot cannot hover indefinitely, as energy budget constraints will eventually force a decision. Which brings us to the concept of **"energy budget"**, something we'll go into much details later. 

Now, once the nanobot reaches and holds position at the target, what does it actually do? That depends entirely on what it was sent to do. I think this is where the system becomes a bit more....**MORE**. Because different medical objectives require fundamentally different energy budget and behaviours at the target site.

Here we introduce three mission types. There are more, but that will be something for another day

<figure>
  <img src="/assets/images/Nav_three missions.png" alt="Adaptive Multi-Strategy Navigation: Three Missions">
  <figcaption>
    <strong>Fig 5: Adaptive Multi-Strategy Navigation</strong><br>
    <em>Drug Delivery (blue, top):</em> Touch-and-go. The nanobot reaches the target, releases its payload, and disengages. Mission complete.
    <em>Monitoring (orange, bottom):</em> Active hovering. The nanobot stays at the target continuously, sensing and reporting.
  </figcaption>
</figure>


Out of all these modes, the **'monitoring'** mode is the most energy-intensive one, and it eventually hits energy limits first.  The "reached but drifted" problem is also still very much visible in all three mission types. Capillary flow is still stronger than hovering thrust, and solutions for it are already in the roadmap.

The energy budget in that right column is the first signal of a constraint that will become central to the system design. How do nanobots power themselves for extended deployment? Blood glucose harvesting, magnetic coupling, ultrasound energy transfer? Lots of solutions have been proffered in literature, and is something you'll see our approach to in later posts. Another thing that will be introduced is the decision tree that governs which mode the nanobot enters — navigation, approach, engagement, fallback. We have been calling it the **adaptive mission mode.** Hope you read on to see why:)



---
*P.S:* Here, we mostly spoke about things from a 2D angle: a flat cross-section of a single vessel, target coordinates specified in advance, one nanobot at a time (mostly). However, as we know, the body is none of these things. Geometry is 3D. Vessels branch. And there can't be just one nanobot for therapy to happen effetcively. There has to be some great number (**Swarm of Intelligent Emergent Nanobots** — heading into the thousands), and they have to coordinate on task, and recruit each other toward a shared goal or a discovered target.. These will all be in subsequent issues.




---
## Sources & Further Reading
* https://chemotaxis.biology.utah.edu/projects/ecolichemotaxis/ecolichemotaxis.html
* Wilson (1971) The Insect Societies.
* https://pmc.ncbi.nlm.nih.gov/articles/PMC5361430/
* Purcell, E.M. — Life at Low Reynolds Number (1977). American Journal of Physics. The original scallop theorem paper. Essential reading.
* Berg, H.C. — E. coli in Motion (2004). Springer. The definitive account of bacterial chemotaxis and run-and-tumble navigation.
* Patiño Padial, T., Chen, S., Hortelão, A.C., Sen, A. & Sánchez, S. — Swarming Intelligence in Self-Propelled Micromotors and Nanomotors.
* Fraire, J.C. et al. — Swarms of Enzymatic Nanobots for Efficient Gene Delivery. Includes flagellar propulsion and collective navigation models.
* Guo, Z., Liu, J. et al. — Biofriendly Micro/Nanomotors Operating on Biocatalysis: From Natural to Biological Environments.
* Venugopalan et al. — Conformal Cytocompatible Ferrite Coatings Facilitate the Realization of a Nanovoyager in Human Blood. Indian Institute of Science, Bangalore.
* Sutton & Barto — Reinforcement Learning: An Introduction. For the gradient descent / chemotaxis parallel and the decision framework.
* ABILITY OF POLYMORPHONUCLEAR LEUKOCYTES TO ORIENT IN GRADIENTS OF CHEMOTACTIC FACTORS- by SALLY H. ZIGMOND (From the Biology Department, University of Pennsylvania, Philadelphia, Pennsylvania 19104)
