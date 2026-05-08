---
title: Drift, Detect, Decide
date: 2026-05-06
layout: post
---


The previous posts in this series have deliberately, been about one nanobot. One particle in one vessel, navigating toward one target. We established the physics of blood flow, the switching logic of hybrid navigation, the scallop theorem, the chemical gradient, the hovering problem. All of it with a single agent.

But a single nanobot is not a medical system. It is a proof of concept. We need a swarm, and One cannot understand a swarm without first understanding the individual.

The human body contains approximately 100,000 kilometres of blood vessels. A single nanobot injected intravenously will be carried by the circulation through that network, and the probability of it encountering the correct capillary bed, at the right moment, with the right conditions for active navigation to work, is vanishingly small without coordination, redundancy, and intelligence at the collective level.

So, here we're focusing on building that collective. Three things will happen in sequence here, each building on the last: 
* we extend the simulation into three dimensions,
* we introduce the branching vascular network and the decision problem(or complexity?) it creates, and then
* we release a swarm — and watch what emerges when you give many simple agents the same goal and let them work.

We'll conclude on a harder note, one we cannot fully answer here due to the sheer scale of its complexity, and which will be the focus of the subsequent succeeding post. It's a question the current literature in swarm nanorobotics is only beginning to seriously address: **What happens when the agents are not identical**? When different nanobots have different roles, capabilities, and missions? When the swarm is not a crowd but a team?

## Into Three Dimensions (3D)

The 2D simulations of the previous posts were a necessary simplification. A cross-section of a vessel, with position described by axial distance *x* and radial distance *y*, captures the essential physics of Poiseuille flow and Brownian motion. However, a blood vessel is not a flat plane and cannot be represented in 2D. Its geometry is cylindrical, which alters both the flow field and the spatial constraints the nanobot must navigate.

In 3D, position is described by *(x, y, z)* in Cartesian coordinates, or equivalently *(x, r, θ)* in cylindrical coordinates, where *r* is the **radial distance** from the centreline and *θ* is the **angular position** around it. For axisymmetric Poiseuille flow — which is the correct model for a straight cylindrical vessel — the velocity field depends only on *r*, not on θ. The flow is rotationally invariant: every cross-sectional plane through the vessel axis looks identical.

This means the 3D velocity profile takes the same parabolic form as in 2D, but now extended into the full cross-section, like so:

          v(r) = v_max × (1 − r²/R²)

Where:
* **r:**  √(y² + z²)


Here, the velocity is highest at the centerline and reaches zero at the vessel wall. The previous 2D parabola is now a paraboloid of revolution — a bowl-like shape extending symmetrically in all directions from the central axis.

 
**Brownian motion changes in 3D**. In 2D, thermal kicks were confined to the x-y plane. In 3D, they act in all three directions simultaneously. The random displacement per timestep in each dimension remains:

          Δx_random ~ N(0, √(2DΔt))

But the net 3D displacement is the vector sum of three independent random processes. This means that **Brownian motion explores volume, not just area.** The nanobot's stochastic behaviour in 3D is richer, and in capillaries where Brownian forces dominate, this matters for how we think about targeting accuracy.


<figure>
  <img src="/assets/images/3D Nanobot Trajectory in Cylindrical Vessel.png" alt="3D Nanobot Trajectory in Cylindrical Vessel">
  <figcaption>
    <strong>Fig 1: 3D Nanobot Trajectory in Cylindrical Vessel.</strong><br>
    <em>Left:</em> The first simulation in three dimensions. 
    The nanobot trajectory (blue) spirals slightly as it moves axially through the cylindrical vessel — the Brownian kicks in y and z rotate it around the centreline while blood flow carries it
    forward along x. Start (green dot) and target (red star) are placed in the same capillary-scale geometry as the 2D simulations. 
    <em>Right:</em> Distance to target over time. The nanobot approaches, reaches the target threshold briefly, then is carried past. 
    The "reached but drifted" problem familiar from prev. 2D plays out also in full 3D geometry.
  </figcaption>
</figure>


### The Bifurcation Problem

A straight cylindrical vessel is a tractable problem. However, the vasculature of living things is anything but straight. It has tons of branches — repeatedly and hierarchically — at every scale, from the aorta splitting into iliac arteries down to arterioles dividing into capillary beds. Every branching point is a decision point. Venture into plant territory and the network complexity only intensifies. Regardless of the host, the nanobot eventually arrives at a bifurcation and must choose.

This is, structurally, one of the harder problems in nanorobotics navigation. At a Y-shaped bifurcation, the nanobot has two options. Without intelligence, it is carried by whichever branch has higher flow — which is not necessarily the branch containing the target. With intelligence, it reads the local chemical gradient and steers towards whichever branch shows higher concentration. But the gradient at the bifurcation point may be weak, noisy, or ambiguous, especially if the target is far downstream.

The geometry of the bifurcation itself is governed by **Murray's Law**, first derived by *Cecil Murray in 1926*, describing how vessels branch to minimise the energy cost of blood transport. 

Murray's Law for *N* Vessel Branching
    ![/Murray's Branching Law](/assets/images/Murray_Branching_Law.png)
   
**Where**:
*r₀* — parent vessel radius
*r₁, r₂* — daughter vessel radii
    
 For a parent vessel splitting into multiple daughter vessels, it'll be given as this:   

  ![Flow Distribution Daughters](/assets/images/FlowDistribution_daughters.png)

Each branch receives equal share of volumetric flow

Murray's Law is a physical optimisation that the vascular system has converged on, minimising both the metabolic cost of maintaining blood volume and the viscous resistance to flow. The cube relationship between radii emerges from the balance between two competing costs: 
* the energy to pump viscous fluid (which favours wider vessels) and
* the metabolic cost of maintaining vascular tissue (which favours narrower vessels).

The optimal branching exponent of 3 falls directly out of Poiseuille's law and the assumption of constant wall shear stress across the network.

That means, for a nanobot navigating this network, Murray's Law has a practical implication in that: at each bifurcation, flow splits approximately equally between daughter branches. Without active steering, a nanobot has roughly 50% probability of entering the correct branch at each junction. In a network with multiple branching levels, that probability compounds — a nanobot navigating four bifurcations without gradient guidance has only a 6.25% chance of reaching the correct terminal vessel by chance alone. This is why **chemotaxis-guided branch selection** is a necessity.

In the simulation below, branch selection is implemented using a **two-stage gradient sensing protocol**. At approach to a bifurcation point, the nanobot samples chemical concentration in both prospective branch directions and selects the branch with the steeper gradient — the one pointing more directly toward the source. A beacon-weighting term is also applied: if another nanobot has already reached the target and released a **recruitment beacon**, that signal biases branch selection toward the beacon's origin, even at distances where the primary gradient is still weak.

<figure>
  <img src="/assets/images/Y-bifurcation geometry.png" alt="Y-Bifurcation: Geometry, Branch Selection, Vessel Occupancy">
  <figcaption>
    <strong>Fig 2: Y-Bifurcation: Geometry, Branch Selection, Vessel Occupancy.</strong><br>
    <em>Left:</em> 3D rendering of the Y-bifurcation geometry. 
    Parent vessel enters from the left; two daughter vessels diverge at ±30°. The nanobot trajectory (purple) navigates through the parent and selects Branch 1, the branch containing the target
    <em>Centre:</em> Top-view showing branch selection, where target is not at Branch 2 (red).
    <em>Right:</em> Vessel occupancy timeline. The nanobot spends a brief moment in the parent vessel, enters Branch 1 quickly, and stays. Branch 2 is never occupied.
  </figcaption>
</figure>


### The Swarm: Independent Agents with Emergent Behaviour

A single nanobot navigating a bifurcation? Easy! Fifteen (15) nanobots doing it simultaneously and independently, without a central controller telling any of them what to do? Now, we're getting somewhere interesting.

A centralised system — one where a controller outside the body directs each nanobot individually — faces fundamental physical constraints. At the nanoscale, inside living tissue, communication bandwidth is limited, signal attenuation is severe, and the latency between sensing and commanding is clinically unacceptable. More fundamentally, a centralised system has a single point of failure. If the controller is disrupted, the entire swarm becomes inert. This is the core design principle of **swarm nanorobotics.**

A decentralised swarm has none of these weaknesses. Each nanobot makes its own decisions based on local information: what it can sense at its current position. No single nanobot knows the global state of the system nor do they need to. The collective behaviour — convergence on the target, redundancy, robustness to individual failure — emerges from the interactions of simple local rules applied in parallel across many agents. This is the same principle that governs ant colonies, T-cell responses, and cortical neural networks. It is, arguably, the most robust architecture known for complex adaptive behaviour in uncertain environments.

In the simulation below, 15 nanobots are released simultaneously from the vessel entrance. They navigate independently, with each of them following the gradient using the same algorithm. No communication between them — except for one mechanism, introduced deliberately: **the beacon.**

When any one of them reaches the target, it releases a chemical recruitment signal — a secondary gradient that other nanobots can detect. This is modelled directly on quorum sensing in bacterial communities, where cells release signalling molecules that accumulate with population density and trigger coordinated behaviour when a threshold is reached. In our system, the beacon does something simpler: it says **"target found, converge here,"** and any nanobot that detects the beacon adds it as a weighted component to its gradient-following direction.

<figure>
  <img src="/assets/images/SwarmTrajectories_Convergence_and FinalDistribution.png" alt="Swarm Trajectories, Convergence, and Final Distribution">
  <figcaption>
    <strong>Fig 3:Swarm Trajectories, Convergence, and Final Distribution.</strong><br>
    <em>Left:</em> Swarm trajectories. 
    Bright trajectories reached the target; faded trajectories drifted. 11 of 15 nanobots (73.3%) reached the target zone. 
    <em>Centre:</em> Convergence plot. The yellow dotted line marks beacon activation at t = 0.11s — the moment nanobot #4 first reached the target and began recruiting. 
    After the beacon fires, the distance curves of the remaining nanobots drop sharply.
    <em>Right:</em> Final spatial distribution. Stars indicate nanobots that reached the target (clustered near the red X). Circles indicate ones scattered downstream along Branch 2 and the distal reaches of Branch 1. 
    73.3% success rate on first deployment, without any inter-nanobot communication beyond the beacon signal.
  </figcaption>
</figure>

The 73.3% success rate validates the algorithm: gradient following plus beacon recruitment, operating without central control, produces reliable convergence on a target in a branching 3D vascular environment.

The 26.7% failure rate is not a bug to be fixed. In clinical deployment, one would send thousands, perhaps millions of these. And statistical failure at the individual level is irrelevant if the collective success rate produces sufficient treatment at the target site. What does it matter if some don't go in the right branch? Look at it from this angle. In initial circulation, the distribution of nanobots in branches is stochastic, and so they're in different branches, before the beacon is active, i.e, before any nanobot reaches the target to signal the others. If a nanobot enters the wrong branch before the beacon is activated, it has no information to correct its trajectory. It is effectively 'lost' to the 'wrong' part of the network. So, here we identify a specific vulnerability: early-arriving nanobots in the wrong branch have no information to correct their trajectory. In the next phase, **scouts** — a specialized nanoroobt caste, address this directly.

### Division of Labour
Every nanobot in the simulation so far has been identical. Same thrust, same payload capacity, same energy budget, same behavioural parameters. But this is wrong, especially as a design philosophy. 

You see, the human body is not a uniform environment, and disease is not a uniform event. A tumour in the left iliac crest presents a completely different navigation problem from sepsis spreading through the portal circulation, which also presents a completely different problem from a neurodegenerative lesion behind the blood-brain barrier. These scenarios differ not just in location but  also in the physics of the local environment, the molecular signatures involved, the urgency of response, the required duration of treatment, and the proximity to sensitive structures that must not be damaged.

An identical swarm can only be optimised for one set of these parameters. Make the nanobots fast and you burn energy budget before they reach deep targets. Make them maximally loaded with payload and you sacrifice the thrust needed to navigate against capillary flow. Make them aggressive in gradient-following and you sacrifice the conservative caution needed near healthy tissue. **Every parameter choice that optimises for one scenario degrades performance in another**.

How do we solve this? Let's look at Biology again — *which btw, solved this approximately 600 million years ago*. The vertebrate immune system deploys at least a dozen functionally distinct cell types in response to a single pathogen. **Neutrophils** arrive first — fast, aggressive, short-lived, expendable. They create inflammation, kill pathogens, and die within hours. **Macrophages** follow — slower, longer-lived, capable of phagocytosis and antigen presentation. **T-cells** come next — highly specific, require activation, but once activated, devastatingly effective against their precise target. **B-cells** produce antibodies — molecular weapons manufactured remotely and delivered to the site. **Natural killer cells** patrol continuously, not waiting for activation, destroying cells that display the wrong surface markers. **Memory cells** remain after the threat is cleared, dormant but ready, their threshold for activation permanently lowered.

Not one single cell type could have done all of this. The specificity, efficiency, and robustness of the immune response is a direct consequence of division of labour — a specialisation that allows each cell type to be deeply optimised for its particular role rather than broadly adequate for all of them.

The same principle applies here. A uniform nanobot swarm just doesn't work. A heterogeneous swarm with role-specific design,.... now we're rolling....

Recent work in the field has begun to demonstrate this experimentally. Researchers at multiple institutions have shown that heterogeneous microswarms — where different agents carry different functional modules — can execute complex sequential tasks that homogeneous swarms cannot: one subpopulation sensing and mapping, another navigating using the map, another delivering payload at the identified site. The Sensing-Navigating-CargoDropping sequence requires agents specialised for each step, because the physical requirements of each step are in direct tension with the others. A nanobot optimised for sensing (low payload, high sensitivity) is not optimised for drug delivery (high payload capacity, robust anchoring). 

In the first layer of this architecture, we have three roles, each with distinct physical parameters and behavioural logic.

### SPECIFICATION TABLE 

| Attribute | Scouts (30%) | Workers (50%) | Guards (20%) |
| :--- | :--- | :--- | :--- |
| **Max Thrust** | 15 fN | 10 fN | 12 fN |
| **Payload** | 0.5× | 2.0× | Minimal |
| **Energy** | 3000 units | 5000 units | 8000 units |
| **Mode** | Patrol / Aggressive | Standby / Proceed | Escort / Patrol |
| **Special** | 1.5× Exploration gain | High drug capacity | Immune camouflage (PEG) |

### OPERATION ROLES TABLE

| Unit | Primary Role |
| :--- | :--- |
| **Scouts** | Find target first. Take risks. Report and recruit. |
| **Workers** | Deliver treatment. Execute efficiently and safely once beacon is detected. |
| **Guards** | Protect the swarm. Intercept immune cells. |


The design logic behind each role follows directly from the physical demands of each function.

**Scouts** need speed because their job is coverage — sampling as much of the vascular network as possible in the shortest time to find a target that may be below imaging resolution or in a location not anticipated by pre-injection imaging. High thrust and aggressive exploration gain serve this. They do not need to carry treatment payload, so their payload capacity is minimal, freeing mass and volume for sensors and speed. Their energy budget is deliberately short: scouts are expendable. They find the target, fire the beacon, and either degrade or a secret third thing.

**Workers** are the opposite, they carry the treatment. Double payload capacity means double drug concentration at the target site, or the same concentration with half the number of workers required. But workers can't explore aggressively, cuz exploration burns energy before the treatment site is reached, and aggressive navigation near sensitive tissues increases the risk of off-target action. That means, workers should be conservative by design: they wait for the scout beacon before committing to a route, then follow the confirmed path efficiently. The scout takes the risk. The worker delivers the result.

**Guards** solve a problem that purely navigation-focused designs ignore: the immune system. Any foreign object in the bloodstream is a target for macrophages, neutrophils, and complement activation. A nanobot that successfully navigates to its target can still be destroyed before it acts. Guards carry active immune camouflage — a PEG (polyethylene glycol) coating that mimics the glycocalyx of red blood cells, making them appear "self" to immune surveillance. More importantly, when a guard detects an immune cell approaching a worker cluster, it can release decoy signals — molecular patterns that attract immune attention toward the guard and away from the workers doing the actual treatment. 

The energy budget differences reflect mission duration. Scouts are short-lived. Workers execute their payload delivery and then degrade. Guards persist — their job continues as long as the swarm is active, and potentially continues into the post-treatment sentinel phase. Hence the large unit budget: nearly three times the scout allocation.

The interaction between these roles produces behaviour that none of them could produce alone. Together they create a system that is simultaneously aggressive in finding the target, conservative in treating it, and robust against biological counter-measures. This is a direct computational implementation of the same architectural principle that governs biological immune function.


<figure>
<img src="/assets/images/Three-Patient Swarm Comparison_.png" alt="Phase 1: Three-Patient Swarm Comparison">
  <figcaption>
    <strong>Fig 4: Phase 1 — Three-Patient Swarm Comparison.</strong><br>
    <em>Patient A:</em> carotid stenosis. <em>Patient B:</em> celiac trunk lesion. <em>Patient C:</em> coronary stenosis.
    Each column shows the same five analyses: vessel trajectories colour-coded by role (blue = worker, green = scout, red = guard), swarm state population over time, energy per nanobot by role, beacon timeline, and final composition pie chart.
    The population dynamics are particularly revealing. In all three scenarios, the acute phase begins with 100% workers. As scouts reach the target and the problem is confirmed, workers converge and treat. 
    Post-treatment, the composition shifts: some workers degrade (controlled apoptosis, the process discussed in the safety architecture), while a subset differentiate into the sentinel configuration — scouts transitioning to passive patrol mode,
            workers standing by in reserve, guards maintaining immune interface.
    The energy curves show role-differentiated depletion: scouts burn energy early and fast (high exploration activity), workers burn steadily during treatment, guards sustain low-level consumption throughout. 
    The pie charts at the bottom show final swarm composition — the ratio of anchored (treating), sentinel-passive (monitoring), standby (reserve), and degraded (mission-complete) nanobots. 
    Patient C, the coronary stenosis, shows the highest degradation rate, reflecting the shorter treatment duration and faster target clearance in that geometry.
  </figcaption>
</figure>



### The Deployment Lifecycle — From Acute Response to Long-term Sentinel
The three-role architecture above describes the composition of the swarm during active treatment. But treatment has phases, and the optimal swarm composition changes across them.

* Phase one: Acute phase. A problem has been identified — a tumour, an infection, a thrombosis. The immediate goal is resolution. The swarm is deployed at 100% workers, maximum payload, aggressive treatment. Scouts are injected simultaneously to confirm target location and guide worker convergence. The acute phase ends when the target is cleared — target health in the simulation drops to zero, which triggers a state transition in every nanobot.
* Phase two: Differentiation into the sentinel configuration. After acute treatment, most workers degrade via controlled apoptosis — the biocompatible self-destruction mechanism modelled on cellular programmed death, where the nanobot breaks down into amino acids, sugars, and mineral components that the body clears through normal macrophage activity. But a subset (approx. 5%) will not degrade. Instead, they differentiate: some become scouts in continuous patrol mode, some become workers in low-energy standby, and some remain as guards in immune-interface mode. This happens in the ratio 60:100:40

This sentinel configuration addresses what I described [previously](https://samuelruby.github.io/2026/04/20/before-the-target-the-river.html) as the monitoring gap. The sentinel swarm circulates continuously through the vascular network, sampling every major organ bed on a cycle of 30 to 60 seconds (one complete circulation). Scouts measure molecular signatures — circulating tumour DNA, surface antigens, metabolic markers, inflammatory cytokines. If a scout detects a signature above threshold — a relapse, a metastatic seeding, an early infection — it releases a beacon, the swarm consensus protocol evaluates whether the signal is confirmed across multiple scouts, and if confirmed, an escalation request goes to the human oversight layer: *"Anomaly detected in left hepatic lobe, recommend investigation."*

* Phase three: Relapse or reinfection response. The sentinel is already present. Scouts detect the problem before symptoms *a.k.a before the patient knows*. Workers wake from standby. If the existing reserve is insufficient for the new threat, a reinforcement injection supplements the sentinel swarm. New workers combine with existing sentinels. The system responds faster than any symptom-triggered clinical pathway could.

This lifecycle — acute treatment, sentinel transition, continuous monitoring, relapse response — is the architecture we are building towards. We currently implement Phase 1 and a bit of the post-treatment differentiation. Phase 2 and 3 are in the works. As you can see, this is a frmework grounded in the same principle that makes the immune system work: different problems require different responses, and a well-designed system prepares for all of them before they arrive.

## Currently on Today's news.....
The concept of swarm nanorobotics for targeted drug delivery is nothing new. Enzymatic nanomotors, DNA origami robots, magnetic microswimmers — all of these have been demonstrated experimentally at small scales in biological fluids. Sánchez's group at the Institute for Bioengineering of Catalonia has shown enzyme-powered nanomotors exhibiting swarming behaviour in vivo in a bladder model. The Indian Institute of Science team demonstrated a nanobot navigating in human blood. Multiple groups have published on chemotaxis-guided navigation in microfluidic channels. The technology roadmap for micro and nanorobots, recently published in ACS Nano, identifies collective behaviour and embodied intelligence as the two most critical open challenges for the next generation of systems.

What is less developed in published literature — and where this work attempts to contribute — is the systems-level architecture. Most published work focuses on individual nanobot capabilities: propulsion mechanisms, surface chemistry, drug loading, all of that. However, the question of how a large heterogeneous population of nanobots with different roles should be organised, deployed, and coordinated across the full vascular network — from injection to sentinel — is largely unanswered.

Recent work on heterogeneous magnetic microswarms has demonstrated a division of labour between sensing and drug-carrying agents in simple geometries. while promising and directly relevant, these studies often rely on external magnetic actuation at the micrometer scale, which limits in vivo applicability. It also does not address the full deployment lifecycle: the transition from acute treatment to long-term monitoring, where, I argue the true clinical value lies.

Thus far, we have presented a computational architecture—a rigorous model designed to guide design decisions as physical fabrication becomes feasible. However, such a decentralized and autonomous system introduces significant risks, from hardware malfunction to adversarial interference. To mitigate these, we have integrated a robust safety architecture. By incorporating Byzantine fault tolerance, controlled degradation, and Level 2 human oversight, the system is specifically engineered to defend against real-world attack vectors.

Phase 2 of the simulation extends the vascular model from a simple Y-bifurcation to an H-tree network — a hierarchically branching structure that more closely approximates real vascular anatomy, with two simultaneous disease targets at different locations. The question Phase 2 asks is: can the swarm find and treat multiple targets concurrently, without being told in advance how many there are or where they are? The *'metastasis problem'*. 

Phase 3 moves to a full graph-based vascular model — the vasculature represented as a directed graph of nodes and edges, with anatomically inspired vessel segments from the aorta through the celiac trunk to the hepatic, splenic, and mesenteric beds. Multiple nanobots navigate this network simultaneously. The simulation tracks which vessels they occupy, when they make branching decisions, and how the swarm distributes across the anatomy.



---
*P.S:* As noted earlier, the next post will go deeper into the architecture itself: the specialisation logic, the differentiation triggers, the coordination protocols between roles, and the harder questions that arise when you try to make this biologically plausible. How does a nanobot know when to transition from worker to sentinel? How does the swarm reach consensus without central arbitration? And crucially, how do you prevent the scout beacon from triggering a false-positive response that draws the entire swarm to a noise signal?

These are open questions. Some we attempt to answer; others..well, we'll cross those bridges when we get there. 


---

## Sources & Further Reading

* Murray, C.D. — The Physiological Principle of Minimum Work (1926). PNAS. The original derivation of Murray's Law.
* Hortelao, A.C. et al. — Swarming behavior and in vivo monitoring of enzymatic nanomotors within the bladder. Science Robotics, 2021.
* Venugopalan, P.L. et al. — Conformal Cytocompatible Ferrite Coatings Facilitate the Realization of a Nanovoyager in Human Blood. Indian Institute of Science, Bangalore.
* Patiño Padial, T., Chen, S. et al. — Swarming Intelligence in Self-Propelled Micromotors and Nanomotors.
* Fraire, J.C. et al. — Swarms of Enzymatic Nanobots for Efficient Gene Delivery.
* Wang, Y. et al. — Swarm Autonomy: From Agent Functionalization to Machine Intelligence. Advanced Materials, 2025.
* Harnessing Disparities in Magnetic Microswarms — heterogeneous swarm division of labour, sensing-navigating-cargo-dropping demonstration. Advanced Science, 2024.
* Technology Roadmap of Micro/Nanorobots — ACS Nano. Collective behaviour and embodied intelligence identified as critical open challenges.
* Ferrante, E. et al. — Evolution of Self-Organized Task Specialization in Robot Swarms. PLOS Computational Biology, 2015.
* Berg, H.C. — E. coli in Motion. Springer, 2004. Quorum sensing and bacterial collective behaviour.  
