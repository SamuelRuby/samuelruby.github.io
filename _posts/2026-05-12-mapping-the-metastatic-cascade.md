---
title: "Lynch: Mapping the Metastatic Cascade"
date: 2026-05-12
layout: post
---


Every post in this series has been building toward this.

We started with the ["Why"](https://samuelruby.github.io/2026/04/10/the-very-first-sin.html) and moved through the foundational physics of the ["River"](https://samuelruby.github.io/2026/04/20/before-the-target-the-river.html). Then we gave the [nanobot intention](https://samuelruby.github.io/2026/04/22/Intention-at-the-nanoscale.html), peers to navigate a [branching world](https://samuelruby.github.io/2026/05/06/drift-detect-decide.html), and finally, a caste system capable of memory and graceful apoptosis in this [Post ](https://samuelruby.github.io/2026/05/11/the-silicon-stem-cell.html). 

**This post brings those layers together**. We are no longer looking at isolated algorithms; we are watching a heterogeneous swarm operate within a complete anatomical vascular network. To test this system, we have chosen one of the most complex challenges in oncology: **Lynch Syndrome.**

**Lynch Syndrome** (ereditary Non-Polyposis Colorectal Cancer) is the perfect "adversary" for a nanorobotic swarm. It is characterized by germline mutations in DNA mismatch repair genes (MLH1, MSH2, MSH6, PMS2), producing aggressive, high-instability (MSI-H) tumors. Its true difficulty lies in its unpredictability OR the problem is it is so unpredictable. 
 A Lynch carrier doesn't just face a high lifetime risk of colorectal cancer (up to 82%); they face the threat of synchronous primaries—two or more independent cancers arising simultaneously in different organs, such as the endometrium (up to 60%) or the liver. The clinical challenge is that carriers often present with advanced disease because the surveillance protocols for detecting Lynch-associated cancers are imperfect, expensive, and invasive.

And this is exactly where our architecture excels. Cuz we fill in the:
* **The Surveillance Gap**: Current protocols are invasive and often miss advanced disease.
* **The Detection Challenge**: A system that can patrol multiple organs simultaneously.
* **The Coordination Requirement**: We need a swarm that can identify a "noise" signal in the colon while maintaining a "sentinel" presence in the liver.


In the simulation scenario that follows, a Lynch Syndrome patient with two simultaneous primaries — hepatocellular carcinoma (HCC) in the right hepatic lobe, fed by the hepatic segmental artery, and colorectal carcinoma (CRC) in the sigmoid colon, fed by the inferior mesenteric artery(IMA). 250 nanobots injected intravenously into the descending aorta. No prior knowledge of which organ contains which cancer. No central controller. Gradient field, beacon protocol, and threat-score resource allocation only.
  ![/lynch_syndrome_scenario](/assets/images/lynch_syndrome_scenario.png)

### THE H-TREE

Before deploying into full anatomical geometry, the single-target question had to be answered: can the swarm find and treat two simultaneous disease sites when it has no prior knowledge of either location?

The test environment is an H-tree vascular network — a hierarchically symmetric branching structure where a single parent vessel bifurcates into daughters across multiple levels. This geometry serves as a computational model of capillary bed architecture, the critical zone where flow slows to millimeter-per-second velocities and active navigation becomes physically possible.

The H-tree here is not merely a synthetic microvasculature; each daughter branch adheres strictly to Murray’s Law to maintain physiological realism. We have calibrated the flow velocities specifically to the capillary regime, a delicate equilibrium where Brownian motion and chemotaxis operate on comparable timescales. This is the 'goldilocks zone' where the nanobot’s sensors can finally overcome the momentum of the river."
   ![/H-tree vascular network schematic](/assets/images/Htree_vascular_network_diagram2.png)

   
Two disease sites: liver (branch A, health=1.0) and lung (branch C, health=0.6). 200 nanobots. No target coordinates pre-loaded. Gradient field only — the nanobots must find the signal themselves.

The first nanobot anchored at the liver site—a result of purely stochastic search. But once that initial beacon fired, the transition was near-instantaneous. Following a rapid recruitment phase, the system achieved full liver clearance: **73 nanobots** anchored concurrently, their 'CLEARED' beacons firing in unison. The target's viability dropped from 1.0 to 0.0 in under ten seconds of peak treatment activity

Once the liver site was neutralized, the first anchor at the lung site followed. The swarm automatically redirected its gradient-following logic toward the remaining active disease signal.

The lung clearance was immediate, followed by a system-wide differentiation trigger. Of the surviving agents:
* 9 **sentinels** formed for long-term monitoring.
* 102 agents** entered standby reserve.
* 68 **nanobots** entered programmed apoptosis (graceful shutdown).

![/Lynch Clearance Info](/assets/images/lynch_clearance details.jpg)

**Mission Survivability:** 132/200 agents (66%).

The sequential engagement pattern observed here is clinically significant. The swarm did not split its resources equally between two targets at the onset. Instead, it **converged on the higher-higher-severity threat** first — the liver (with health=1.0) over the lung (with health=0.6). This happened because the liver's stronger molecular gradient signal, amplified by early beacon activation, influenced bifurcation selection more aggressively. Resource allocation was implicit, encoded in the fluid physics and gradient-following logic. After liver clearance, the surviving nanobots naturally redirected toward the lung's remaining signal. 

The swarm arrived at the same triage logic a medical team would apply: address the most acute threat first. 

The 68 nanobots that entered apoptosis post-clearance degraded via the controlled pathway — enzymatic breakdown into biocompatible components, spread over the post-simulation window. No immune overreaction. No foreign body accumulation. The system cleaned up after itself.

<figure>
  <img src="/assets/images/h_tree_swarm_convergence_and_clearance.png" alt="H-tree swarm convergence and clearance">
  <figcaption>
    <strong>Fig 3:H-tree swarm convergence and clearance.</strong><br>
    <em>Top Left:</em> Vascular network geometry with bot trajectories — two target sites visible in branches A and C.
    <em>Top right:</em> Distance-to-target curves for both sites, showing the sharp convergence at t≈5.5s (liver) and t≈8.5s (lung)
    <em>Middle:</em> Swarm state population over time — the transition from active transit (blue) through anchored treatment (pink) to sentinel and standby post-clearance
    <em>Bottom:</em> Per-bot energy curves and beacon event timeline.
    The beacon firing events at t=6.47s (liver cleared) and t=14.55s (lung cleared) are the two major state transition points visible in all panels simultaneously.
  </figcaption>
</figure>


### Phase III: Anatomical Reality
In Phase III, we move from the idealized H-tree to true anatomical geometry. The vascular network is no longer a symmetric abstraction; it is a directed graph of **19 nodes and 24 edges** representing the actual branching architecture of the human abdominal vasculature. This model includes the primary vessels supplying the liver, colon, spleen, stomach, and small intestine—all loaded from a JSON specification with physically calibrated radii, lengths, and flow velocities.

The injection point is the **descending aorta**. From there, nanobots transit through the celiac trunk to reach hepatic circulation, or through the superior mesenteric artery (SMA) and inferior mesenteric artery (IMA) to reach the colonic network. Crucially, the model includes a **venous return loop**: agents that miss their target recirculate and attempt the passage again, with one complete circulation taking approximately 2.2 seconds at centerline velocity.

![/Phase 3 Vascular Anatomical Geometry](/assets/images/phase3_abdominal_vasculature.png)

![/Phase 3 Vessel Segment Statistics](/assets/images/vessel_segment_statistics.png)


A critical engineering decision in this architecture is the **Multi-Scale Physics Gating**. In a system of this complexity, simulating every physical force at every scale would be computationally prohibitive and scientifically redundant. Instead, the simulation "unlocks" levels of physical detail only when they become relevant to the agent’s behavior.

In macro vessels — radius above 1mm, flow velocity 150–400mm/s — the physics of active navigation is irrelevant. A nanobot thrust of 150 fN produces an effective swimming speed of ~23 μm/s—roughly 1:10,000 against the current. At this scale, the nanobot is a passenger. Simulating Brownian motion, chemotaxis, and wall confinement in these vessels would consume computational resources to model physics that has no meaningful effect on outcome. So in macro vessels: passive transit only. 1D flow. No Brownian noise. No chemotaxis. Fast computation.

In micro vessels — radius below 1mm, flow slowing to 40–80mm/s — the physics shifts. While the agents still cannot "swim" against the current, their thrust (~0.05% of flow velocity) becomes a meaningful tool for influencing trajectory. At this scale, Brownian motion (σ=36nm/timestep) becomes significant relative to the vessel radius. Chemotaxis gradients become detectable and actionable, wall interactions are calculated, and the agent begins to "decide" its path. Full physics activates.

This transition occurs precisely at the boundary between the arterioles and the segmental capillaries; the exact anatomical point where the capillary bed begins. By "gating" the physics in this way, the simulation honors the physics of scale while maintaining the performance required for million-agent populations.



250 nanobots are deployed into the full anatomical network in the Lynch Syndrome scenario. Both the liver and colon targets share the same molecular signature — the simulation treats the disease as one biological threat expressed in two locations, which is the clinical reality of Lynch Syndromewhere synchronous primaries often share the same mismatch-repair-deficient phenotype.

![/Transit times at centreline velocity](/assets/images/transit_times.jpg)


The simulation results for infiltration efficiency are striking. Both targets were successfully infiltrated within the first two seconds—before the majority of the swarm had even been injected.

* **Colon Infiltration (t=1.4s)**: Worker #7 reached the sigmoid terminal capillary in 1.4 seconds. This is notably faster than the theoretical centerline transit time of 1.56 seconds.
* **Liver Infiltration (t=1.8s)**: Worker #114 reached the hepatic segmental capillary in 1.8 seconds, beating the theoretical estimate of 2.21 seconds.

How is this possible? Well, the Poiseuille profile. Nanobots injected near the centerline of the macro vessels travel significantly faster than the average flow velocity. By "riding" the peak of the velocity curve, these early arrivals provide the swarm with almost instantaneous sensory coverage of the entire abdominal cavity.

The log data also reveals a textbook example of implicit triage. The liver target (Health: 1.0) presented a stronger initial gradient than the colon (Health: 0.7). As a result, the swarm did not split 50/50. Instead, it "voted" with its trajectory:
* By **t=4.5s**, 40 nanobots had already converged on the liver, triggering the first **LIVER CLEARED** beacon.
* The "Knowledge Burst" from these 40 beacons immediately updated the remaining swarm.
* By t=8.8s, the swarm had pivoted its full weight to the remaining threat, resulting in **COLON CLEARED**.

Once the acute treatment phase concluded, the swarm executed its differentiation protocol.
* **Sentinels & Standby (14 agents)**: These remain in the target zones for long-term monitoring.
* **Recovery (72 agents)**: Transitioned to S_RECOVERY to begin localized tissue repair at the treatment sites.
* **Sweep Patrol (131 agents)**: Launched a broad-spectrum surveillance mission to detect micro-metastases that were initially below detection thresholds.
* **Apoptosis**: Units that were faulty or reached their energy floor exited the system gracefully, preventing vessel clutter.

At a timestep of **dt=5ms**, this simulation processed **9 million state updates**. It accounted for Murray Ratio violations in the diseased hepatic vasculature, where tumor-induced remodeling changes routing probabilities and maintained the physics of scale throughout.

The result is a system that doesn't just "find" a target; it manages a clinical lifecycle from the first second of injection through to the long-term sentinel phase.

![/Lynch Simulation Table](/assets/images/simulation_tabel_lynch.png)

<figure>
  <img src="/assets/images/phase31_3D_vascular_network.jpg" alt="3D vascular network">
  <figcaption>
    <strong>Fig 1: 3D vascular network with bot trajectories colour-coded by final state .</strong><br>
    <em>Liver</em> target (red) in the upper hepatic branch, colon target (green) in the lower sigmoid branch.
    The spatial separation between the two disease sites in anatomical coordinates is immediately visible.
  </figcaption>
</figure>



<figure>
  <img src="/assets/images/phase31_swarmstatepopulation_overtime.jpg" alt="Swarm Lifecycle — State Population Over Time">
  <figcaption>
    <strong>Fig 2: Swarm Lifecycle — State Population Over Time .</strong><br>
    <em>The DIFF event</em> marks the transition from undifferentiated acute treatment to specialised post-treatment states. 
  </figcaption>
</figure>


<figure>
  <img src="/assets/images/phase31_target_health_recovery.jpg" alt="Target Health and Tissue Recovery">
  <figcaption>
    <strong>Fig 3: Swarm Lifecycle — State Population Over Time .</strong><br>
    <em>Event:</em> Both tumour health curves (red=liver, green=colon) drop sharply to zero within the first 20 seconds. 
    Tissue recovery (dashed) begins immediately after clearance and trends toward 1.0.
    Beacon Events Timeline — the SUSPICIOUS, PROBLEM, and CLEARED event sequence for each target, showing the swarm's detection-to-clearance logic in chronological order.
  </figcaption>
</figure>


<figure>
  <img src="/assets/images/phase31_per_organ_dynamics.png" alt="Per-organ treatment and recovery dynamics">
  <figcaption>
    <strong>Fig 4: Per-organ treatment and recovery dynamics .</strong><br>
    <em>Top row:</em> health and recovery curves with clearance timestamps annotated. Liver cleared at t≈4s post-engagement. 
    Colon cleared at t≈9s (higher initial health, longer treatment duration)
    <em>Middle row:</em> rolling event rate — the sharp green spike at clearance, flat orange (zero PROBLEM events) throughout
    <em>Bottom row:</em> anchored bots + recovery bots over time. The anchored (treating) population peaks and then falls as treatment completes 
    and bots transition to recovery or sentinel mode. The recovery bots (darker) persist longer, scaffolding tissue repair after tumour clearance.
  </figcaption>
</figure>

<figure>
  <img src="/assets/images/phase31_role_evolution.png" alt="Swarm role evolution and energy by role">
  <figcaption>
    <strong>Fig 5: Swarm role evolution and energy by role .</strong><br>
    <em>Top:</em> Full state population including secondary mission states — the complete 12-state lifecycle visible across the 180s window. 
    The transition cascade post-DIFF is the most information-dense region: responding → anchored → treated/detached → sentinel patrol → sweep (micro-met patrol) → scaffold → degrading → dead.
    Each state is a distinct colour; the temporal ordering of state transitions matches theoretical prediction exactly.
    <em>Bottom:</em> Mean Energy per Role tracked at each timestep. 
    Worker energy (blue) depletes steeply during acute treatment, recovers slightly post-clearance as workers transition to lower-energy states. 
    Scout energy (green) depletes continuously — scouts never stop patrolling. Guard energy (orange) holds high — their long energy budget (8000 units, nearly 3× scout allocation) 
    means they remain operational throughout the full 180s window. The DIFFERENTIATION marker (dashed vertical line) is where the role-energy profiles diverge from the initial uniform value.
  </figcaption>
</figure>




To truly stress-test the architecture, we introduced a second layer of complexity. The anatomical network and the Lynch Syndrome scenario remain the same, but the two cancers are now molecularly distinct.

The swarm must do more than just find "disease"; it must discriminate between two different pathologies, partition itself into independent sub-swarms, and deliver site-specific treatment without cross-contamination. This mirrors the clinical reality of a Lynch patient presenting with both Hepatocellular Carcinoma (HCC) in the liver and Colorectal Cancer (CRC) in the colon.

The nanobots distinguish between these targets using a molecular signature classification system. As a bot enters a micro-vessel, its sensors detect a three-dimensional marker vector (representing proxies for VEGF, E-cadherin, and CEA/AFP). It then compares this detected signal against pre-loaded reference vectors:
  * **HCC Signature (Liver)**: marker_vec = [0.2, 0.75, 0.25], norm = 0.815
     Dimensions: [VEGF-proxy · E-cadherin-proxy · CEA/AFP-proxy]
     
  * **CRC Signature (Colon)**: marker_vec= [0.85, 0.15, 0.5], norm = 0.997

The system calculates the **Cosine Similarity** between the detected vector and its targets.
In this run, the similarity between the **HCC** and **CRC** vectors was **0.501**. With a classifier threshold set at **0.85**, the result was categorical: **DISTINCT**.
![/Signature discrimination — the math](/assets/images/Signature_discrimination_math.png)


Because the signatures are reliably separable, a nanobot detecting the liver signal cannot mistake it for the colon signal. This allows for **Autonomous Partitioning**:
  * **Sub-swarm A** locks onto the HCC channel, converging on the liver.
  * **Sub-swarm B** ignores the liver beacons entirely, continuing through the mesenteric arteries until it detects the CRC signature in the colon.
  * **Zero Cross-Contamination**: CRC-specific payloads are never wasted on HCC sites, and vice versa.
We have now moved beyond "search and destroy" to a system capable of **simultaneous, multi-target differential treatment**.


At the DIFF event (Differentiation), the swarm must decide how to partition its limited resources. Rather than a simple 50/50 split, the population uses a Threat Score computed per target to determine the optimal allocation ratio.
The architecture employs a dual-factor formula to assess urgency:
![/Threat score formula](/assets/images/Threat_score_formula.png)

For this simulation, we set **α** = 0.35 and **β** = 0.65. This heavy weighting on the rate of change **β** is a deliberate encoding of clinical knowledge regarding Lynch Syndrome.

In MSI-H (Microsatellite Instability-High) tumors, progression is often aggressive. The absolute magnitude of a molecular signal (mean strength) tells you how big the tumor is now, but the rate of change tells you how dangerous it will be tomorrow.
  * **HCC (Liver) Threat Score: 0.0029**
  * **CRC (Colon) Threat Score: 0.0016**

Based on these specific dynamics, the population partitioned itself: **65%** assigned to Sub-swarm B (Liver) and **35%** to Sub-swarm A (Colon).

This weighting reflects a fundamental truth in precision oncology: a large, established, but slow-growing signal may be less of an immediate priority than a small, rapidly intensifying signal from an early-stage but fast-progressing micrometastasis. By favoring **β**, the swarm automatically prioritizes *"the fire that is spreading"* over *"the fire that is merely large."This is the ultimate goal of the architecture, what we tried to do here, expressing clinical knowledge as algorithmic weights.
![/Synchronous primaries Overview](/assets/images/Synchronous_primaries_Overview.png)


<figure>
  <img src="/assets/images/phase32_3D_vascular_network_bottrajectories.jpg" alt="3D vascular network">
  <figcaption>
    <strong>Fig : 3D vascular network (19 nodes, 24 edges, venous return loop) with the two disease sites visible in distinct anatomical locations.</strong><br>
    <em>The venous return pathway</em> shown in large blue vessel closes the recirculation loop, allowing nanobots that miss their target to re-enter circulation. 
  </figcaption>
</figure>


<figure>
  <img src="/assets/images/phase32_swarmstatepopulation_overtime.jpg" alt="Swarm Lifecycle — State Population Over Time">
  <figcaption>
    <strong>Fig 2: Swarm Lifecycle — State Population Over Time .</strong><br>
    <em>The DIFF line</em> (t≈20s) marks where the initially uniform population transitions. Unlike Phase 3.1, 
    the dominant post-DIFF state is the large pink "Recovery (tissue repair)" band, reflecting the Phase 3.2 extended recovery mechanics where tissue regeneration 
    is tracked across a longer timescale.
  </figcaption>
</figure>


<figure>
  <img src="/assets/images/phase32_subswarm_population_time.jpg" alt="Sub-Swarm Population Over Time">
  <figcaption>
    <strong>Fig 3: "Sub-Swarm Population Over Time .</strong><br>
    <em>Event:</em> The gray "Unassigned" population drops sharply at DIFF as nanobots receive their sub-swarm designation. 
    Sub-swarm B (liver, red/salmon) is larger — 65% allocation. Sub-swarm A (colon, green) is smaller — 35%. 
  </figcaption>
</figure>

<figure>
  <img src="/assets/images/phase32_target_tissue_recovery.jpg" alt="Target Health and Tissue Recovery">
  <figcaption>
    <strong>Fig 3: Target Health and Tissue Recovery .</strong><br>
    <em>Event:</em> Liver cleared first (t=19s), colon second (t=22s), tissue recovery curves diverging based on tumour burden and treatment rate.
  </figcaption>
</figure>




<figure>
  <img src="/assets/images/phase32_per_organ_dynamics.png" alt="Per-organ treatment and recovery dynamics">
  <figcaption>
    <strong>Fig 4: Per-organ treatment and recovery dynamics .</strong><br>
    <em>Top row:</em> Health and recovery with sub-swarm bot count overlay (secondary y-axis). Liver: health drops steeply from t=0, cleared at t=19s. 
    Colon: health drops from t=0, cleared at t=22s. 
    <em>Middle row:</em> rolling event rate. The event rate spike is the system logging treatment completion; its height is proportional to the number of 
    concurrently anchored nanobots at the moment of clearance.
    <em>Bottom row:</em> anchored bots + recovery bots over time. The sharp peak (anchored, treating) followed by the gradual 
    tail (recovery bots, tissue repair) tells the treatment story visually: aggressive initial engagement, orderly withdrawal 
    into recovery mode, no abrupt termination.
  </figcaption>
</figure>


<figure>
  <img src="/assets/images/phase32_role_subswarm_evolution.jpg" alt="Role energy and threat-score resource allocation">
  <figcaption>
    <strong>Fig 10: Role energy and threat-score resource allocation .</strong><br>
    <em>Top:</em> Mean Energy per Role. DIFF marker visible at t≈20s. Worker energy (blue) depletes steadily as the treatment workload is sustained across both sub-swarms simultaneously. 
    Scout energy (green) depletes more slowly in Phase 3.2 than in 3.1 — the larger network requires longer patrol coverage.
    Guard energy (orange) holds close to 85% through t=180s — guards are barely consuming their reserves, which is as designed.
    <em>Bottom:</em> Threat Score Summary + Resource Allocation. Tthe swarm's triage decision, rendered as a bar chart. 
    The allocation ratio is computed from signal dynamics and applied algorithmically at DIFF time.
  </figcaption>
</figure>


###  What these results mean — and what they don't
**The Demonstrated***: We have established a computational framework for autonomous, multi-site cancer treatment that produces clinically coherent outcomes across three levels of complexity:
* 1. H-Tree Phase: 200 nanobots successfully localized and cleared two unknown disease sites in 14.55 seconds without pre-loaded coordinates.
* 2. Phase 3.1 (Anatomical): 250 nanobots navigated a realistic abdominal vascular network, reaching simultaneous targets within 2 seconds of injection and executing a full treatment-sentinel lifecycle.
* 3. Phase 3.2 (Discrimination): The system successfully distinguished between two molecularly distinct cancers (cosine similarity 0.501). It partitioned the population into independent sub-swarms, allocated resources based on a dynamics-weighted threat score (65%/35%), and achieved 100% clearance with zero cross-contamination.
Everything here has been computed from first principles: Poiseuille flow, Stokes drag, and Brownian noise at σ=36nm per millisecond. The geometry is anatomically calibrated, and the "intelligence"—from cosine similarity classifiers to Murray’s Law compliance checking—is built on rigorous, clinically motivated logic.


**The Undemonstrated**: **The Fabrication Gap**. The challenge of building a 100nm device capable of flagellar propulsion, molecular sensing, and immune camouflage remains an open engineering frontier. The transition from in silico to in vivo involves biological variables no simulation can perfectly mirror:
  * Non-Newtonian Rheology: Complex blood behavior at the cellular scale.
  * Active Immunogenicity: The body's immediate response to foreign synthetic agents.
  * Pharmacokinetics: The metabolic path and degradation products of the nanobots themselves.

If a nanobot with these physical capabilities can be built — current advancements in DNA origami and enzymatic propulsion suggest it can — the swarm intelligence required to manage it is already validated.



### What comes next
Phase 4 is in design. The targets are three open problems that the current architecture does not yet address:

* First, energy harvesting. Every nanobot in the current simulation runs on a fixed energy budget — a simplification that works for a 180-second deployment window but fails for long-term sentinel operation (days, weeks, months). Harvesting ATP from blood glucose, the same way mitochondria do, could theorectically be something worth working on.
* Second, the blood-brain barrier(BBB). Probably modeling BBB navigation using Trojan horse strategies — surface receptor mimicry — could be something worth looking into
* Third, self-assembly. This is the furthest-out problem on the roadmap. It is also the one that, if solved, changes everything about long-term in vivo deployment.


The work continues. This series continues. And if any of this has made you think "this is worth pursuing" — I want to hear from you.




---
*P.S:* 
The work continues. This series continues. And if any of this has made you think *"this is worth pursuing"*. I want to hear from you :)

FABRICATION!!!

---

## Sources & Further Reading

* Vasen, H.F. et al. — Lynch Syndrome. Nature Reviews Disease Primers, 2022. Clinical background, MSI-H phenotype, synchronous primary risk in germline mutation carriers.
* Janeway's Immunobiology, 9th ed. — Cellular basis of immune response, memory B and T cells, effector vs central memory.
* Alberts, B. et al. — Molecular Biology of the Cell, 6th ed. Apoptosis mechanism, cellular metabolism, tissue repair.
* Murray, C.D. — The Physiological Principle of Minimum Work (1926). PNAS. Murray's Law derivation and implications for vascular branching.
* Hortelao, A.C. et al. — Swarming Behavior and in Vivo Monitoring of Enzymatic Nanomotors within the Bladder. Science Robotics, 2021.
* Harnessing Disparities in Magnetic Microswarms — heterogeneous swarm division of labour. Advanced Science, 2024. PMC11321641.
* Ju, X. et al. — Technology Roadmap of Micro/Nanorobots. ACS Nano, 2025. 19(27), 24174–24334.
* Venugopalan, P.L. et al. — Conformal Cytocompatible Ferrite Coatings Facilitate the Realization of a Nanovoyager in Human Blood. Indian Institute of Science, Bangalore.
