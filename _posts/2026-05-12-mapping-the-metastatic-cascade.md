---
title: "6. Lynch: Mapping the Metastatic Cascade"
date: 2026-05-12
layout: post
---


Here we integrate the individual components developed throughout this seriess.

We began by establishing the [clinical rationale for nanoscale interventions](https://samuelruby.github.io/2026/04/10/the-very-first-sin.html), followed by modeling the [fluid dynamics of the vascular network](https://samuelruby.github.io/2026/04/20/before-the-target-the-river.html). From there, we introduced the [algorithmic baseline for autonomous navigation](https://samuelruby.github.io/2026/04/22/Intention-at-the-nanoscale.html), mapped the multi-agent logic required to [navigate complex vascular branching](https://samuelruby.github.io/2026/05/06/drift-detect-decide.html), and most recently, detailed the differentiation and apoptosis pathways of the [underlying caste system.](https://samuelruby.github.io/2026/05/11/the-silicon-stem-cell.html)

This post integrates those individual layers, transitioning from isolated algorithms to a unified, heterogeneous swarm operating within a complete anatomical vascular network. To test this framework under complex clinical conditions, we have simulated its deployment against the multi-focal pathology: **Lynch Syndrome.**

**Lynch Syndrome** (Hereditary Non-Polyposis Colorectal Cancer) is characterized by germline mutations in DNA mismatch repair genes (MLH1, MSH2, MSH6, PMS2), producing aggressive, high-instability (MSI-H) tumors. The primary challenge of this pathology lies in its unpredictability and its multi-focal presentation.
A Lynch carrier doesn't just face an exceptionally high lifetime risk of colorectal cancer (up to 82%), but also the imminent threat of synchronous primaries—two or more independent cancers arising simultaneously in different organs, such as the endometrium (up to 60%) or the liver. Because current surveillance protocols are invasive, costly, and frequently miss early-stage anomalies, patients often present with advanced, multi-organ disease that overwhelms traditional localized therapies.

And this is exactly where our architecture excels. We fill in the:
* **The Surveillance Gap**: Where current protocols are invasive and often miss advanced disease.
* **The Detection Challenge**: A system that can patrol multiple organs simultaneously.
* **The Coordination Requirement**: A swarm that can identify a "noise" signal in the colon while maintaining a "sentinel" presence in the liver.


In the simulation scenario that follows, we modeled a complex Lynch Syndrome presentation featuring two simultaneous, unmapped primaries:

* Hepatocellular Carcinoma (HCC): Located in the right hepatic lobe and vascularized via the hepatic segmental artery.
* Colorectal Carcinoma (CRC): Located in the sigmoid colon and vascularized via the inferior mesenteric artery (IMA).

 250 agent nanobots introduced intravenously via a single intravenous injection into the descending aorta. Zero a priori data regarding tumor location, organ pairing, or presence. No central controller. The system relies strictly on localized gradient sensing, the peer-to-peer beacon protocol, and threat-score-driven resource allocation.
  ![/lynch_syndrome_scenario](/assets/images/lynch_syndrome_scenario.png)

### THE H-TREE

To evaluate this decentralized logic, we first had to answer a fundamental question: can the swarm autonomously locate and partition resources between two simultaneous disease sites without any a priori location data?

Before introducing full, asymmetric anatomical geometries, we isolated this capability within a symmetric H-tree vascular network. This hierarchically branching structure serves as a controlled computational model of capillary bed architecture, a critical operational zone where blood flow slows to millimeter-per-second velocities, rendering active nanoscale navigation physically viable.

Within this H-tree framework, each daughter branch scales strictly according to Murray’s Law to preserve physiological realism. Flow velocities were also calibrated precisely to the capillary regime. In this specific microvascular environment, local chemical gradients become stable enough for the agent's sensors to overcome advective transport.
   ![/H-tree vascular network schematic](/assets/images/Htree_vascular_network_diagram2.png)

   
Two disease targets: Site A (Liver, initial health=1.0) and Site B (Lung, initial health=0.6). 200 nanobots. No target coordinates pre-loaded, autonomous gradient-seeking logic.

Initial target interception at the liver site was achieved via stochastic search, with a single agent anchoring there. The subsequent beacon deployment initiated a rapid, non-linear recruitment phase. Following this phase, 73 agents concurrently anchored at the target zone. The target's functional viability dropped from 1.0 to 0.0 in under 10 seconds of peak agent density. And upon target neutralization, the primary beacon network deactivated, prompting the remaining unanchored population to redirect toward the surviving disease signal in the lungs.

The lung clearance was immediate, followed by a system-wide differentiation and shutdown protocol. Of the surviving agents:
* 9 **sentinels** differentiated into persistent monitoring roles.
* 102 agents** entered an unallocated, low-energy standby state.
* 68 **agents** initiated programmed, non-inflammatory cellular clearance.

![/Lynch Clearance Info](/assets/images/lynch_clearance details.jpg)

**Mission Survivability:** 132/200 agents (66%).

The sequential engagement pattern observed here is clinically significant. The swarm did not split its resources equally between two targets at the onset. Instead, it converged on the **higher-severity threat** first (the liver first with health=1.0, then the lungs with health=0.6). This happened because the liver's stronger molecular gradient signal, amplified by early beacon activation, influenced bifurcation selection more aggressively. After liver clearance, the surviving nanobots naturally redirected toward the lung's remaining signal. 

The swarm arrived at the same triage logic a medical team would apply: address the most acute threat first. 

<figure>
  <img src="/assets/images/h_tree_swarm_convergence_and_clearance.png" alt="H-tree swarm convergence and clearance">
  <figcaption>
    <strong>Fig 3:H-tree swarm convergence and clearance.</strong><br>
    <em>Top Left:</em> Vascular network geometry with bot trajectories. Two target sites visible in branches A and C.
    <em>Top right:</em> Distance-to-target curves for both sites, showing the sharp convergence at t≈5.5s (liver) and t≈8.5s (lung)
    <em>Middle:</em> Swarm state population over time: the transition from active transit (blue) through anchored treatment (pink) to sentinel and standby post-clearance
    <em>Bottom:</em> Per-bot energy curves and beacon event timeline: The beacon firing events at t=6.47s (liver cleared) and t=14.55s (lung cleared) are the two major state transition points visible in all panels simultaneously.
  </figcaption>
</figure>


### Phase III: Anatomical Reality
In Phase III, we move from the idealized H-tree to true anatomical geometry. The vascular network is no longer a symmetric abstraction here but a directed graph of **19 nodes and 24 edges** representing the actual branching architecture of the human abdominal vasculature. This model includes the primary vessels supplying the liver, colon, spleen, stomach, and small intestine; all loaded from a JSON specification with physically calibrated radii, lengths, and flow velocities.

The injection point is the **descending aorta**. From there, nanobots transit through the celiac trunk to reach hepatic circulation, or through the superior mesenteric artery (SMA) and inferior mesenteric artery (IMA) to reach the colonic network. Crucially, the model includes a **venous return loop**: agents that miss their target recirculate and attempt the passage again, with one complete circulation taking approximately 2.2 seconds at centerline velocity.

![/Phase 3 Vascular Anatomical Geometry](/assets/images/phase3_abdominal_vasculature.png)

![/Phase 3 Vessel Segment Statistics](/assets/images/vessel_segment_statistics.png)


A critical engineering decision in this architecture is the **Multi-Scale Physics Gating**. In a system of this complexity, simulating every physical force at every scale would be computationally prohibitive and scientifically redundant. Instead, the simulation "unlocks" levels of physical detail only when they become relevant to the agent’s behavior.

In macro vessels (radius above 1mm, flow velocity 150–400mm/s), a nanobot thrust of 150 fN produces an effective swimming speed of ~23 μm/s—roughly, 1:10000 against the current. At this scale, simulating Brownian motion, chemotaxis, and wall confinement in these vessels would consume computational resources to model physics that has no meaningful effect on outcome. So in macro vessels: passive transit only. 1D flow. No Brownian noise. No chemotaxis. Fast computation.

In micro vessels (radius below 1mm, flow slowing to 40–80mm/s), while the agents direct upstream propulsion remains impossible, their thrust (~0.05% of flow velocity) becomes sufficient to influence its lateral trajectory. At this scale, Brownian motion (σ=36nm/timestep) becomes highly significant relative to the vessel radius. Local chemotactic gradients become detectable, wall biundary interactions are computed, and the agent begins executing autonomous path-selection logic. This transition occurs precisely at the boundary between terminal arterioles and segmental capillaries; the exact anatomical point where the capillary bed begins. Abstracting the fluid mechanics through this scale-dependent gating mechanism enforces physiological accuracy while maintaining the computational performance required for million-agent populations.

For the multi-focal Lynch Syndrome scenario, the simulation initialization is defined as follows:

* Population Density: 250 agent units deployed intravenously into the primary anatomical network.
* Phenotypic Profiling: Both the hepatocellular carcinoma (HCC) and colorectal cancer (CRC) sites are configured with identical molecular signatures.
This configuration reflects the clinical reality of Lynch Syndrome, where synchronous primary tumors share the same underlying mismatch-repair-deficient (MMR-D) phenotype, presenting a single biological signature across distinct anatomical compartments.
![/Transit times at centreline velocity](/assets/images/transit_times.jpg)

The simulation results for infiltration efficiency are striking. Both targets were successfully infiltrated within the first two seconds, before the majority of the swarm had even been injected.

* **Colon Infiltration (t=1.4s)**: Worker #7 reached the sigmoid terminal capillary in 1.4 seconds. This is notably faster than the theoretical centerline transit time of 1.56 seconds.
* **Liver Infiltration (t=1.8s)**: Worker #114 reached the hepatic segmental capillary in 1.8 seconds, beating the theoretical estimate of 2.21 seconds.

How is this possible? Well, the 'Poiseuille profile'! Nanobots injected near the centerline of the macro vessels travel significantly faster than the average flow velocity. By "riding" the peak of the velocity curve, these early arrivals provide the swarm with almost instantaneous sensory coverage of the entire abdominal cavity.

The log data also reveals that the liver target (Health: 1.0) presented a stronger initial gradient than the colon (Health: 0.7). As a result, the swarm did not split 50/50. Instead, it "voted" with its trajectory.
* By **t=4.5s**, 40 nanobots had already converged on the liver, triggering the first **LIVER CLEARED** beacon.
* The "Knowledge Burst" from these 40 beacons immediately updated the remaining swarm.
* By t=8.8s, the swarm had pivoted its full weight to the remaining threat, resulting in **COLON CLEARED**.

Once the acute treatment phase concluded, the swarm executed its differentiation protocol.
* **Sentinels & Standby (14 agents)**: These remain in the target zones for long-term monitoring.
* **Recovery (72 agents)**: Transitioned to S_RECOVERY to begin localized tissue repair at the treatment sites.
* **Sweep Patrol (131 agents)**: Launched a broad-spectrum surveillance mission to detect micro-metastases that were initially below detection thresholds.
* **Apoptosis**: Units that were faulty or reached their energy floor exited the system gracefully, preventing vessel clutter.

At a timestep of **dt=5ms**, this simulation processed **9 million state updates**. It accounted for Murray Ratio violations in the diseased hepatic vasculature, where tumor-induced remodeling changes routing probabilities and maintained the physics of scale throughout. The result is a system that manages a clinical lifecycle from the first second of injection through to the long-term sentinel phase.

![/Lynch Simulation Table](/assets/images/simulation_tabel_lynch.png)

<figure>
  <img src="/assets/images/phase31_3D_vascular_network.jpg" alt="3D vascular network">
  <figcaption>
    <strong>Fig 1: 3D vascular network with bot trajectories colour-coded by final state .</strong><br>
    <em>Liver</em> target (red) in the upper hepatic branch, colon target (green) in the lower sigmoid branch.
  </figcaption>
</figure>

<figure>
  <img src="/assets/images/phase31_swarmstatepopulation_overtime.jpg" alt="Swarm Lifecycle — State Population Over Time">
  <figcaption>
    <strong>Fig 2: Swarm Lifecycle: State Population Over Time.</strong><br>
    <em>The DIFF event</em> marks the transition from undifferentiated acute treatment to specialised post-treatment states. 
  </figcaption>
</figure>

<figure>
  <img src="/assets/images/phase31_target_health_recovery.jpg" alt="Target Health and Tissue Recovery">
  <figcaption>
    <strong>Fig 3: Swarm Lifecycle: Target Health and Tissue Recovery.</strong><br>
    <em>Event:</em> Both tumour health curves (red=liver, green=colon) drop sharply to zero within the first 20 seconds. 
    Tissue recovery (dashed) begins immediately after clearance and trends toward 1.0.
  </figcaption>
</figure>

<figure>
  <img src="/assets/images/phase31_per_organ_dynamics.png" alt="Per-organ treatment and recovery dynamics">
  <figcaption>
    <strong>Fig 4: Per-organ treatment and recovery dynamics .</strong><br>
    <em>Top row:</em> Health and recovery curves with clearance timestamps annotated. Liver cleared at t≈4s post-engagement. Colon cleared at t≈9s (higher initial health, longer treatment duration)
    <em>Middle row:</em> Rolling event rate
    <em>Bottom row:</em> Anchored bots + recovery bots over time. The anchored (treating) population peaks and then falls as treatment completes as bots transition to recovery or sentinel mode.
  </figcaption>
</figure>

<figure>
  <img src="/assets/images/phase31_role_evolution.png" alt="Swarm role evolution and energy by role">
  <figcaption>
    <strong>Fig 5: Swarm role evolution and energy by role .</strong><br>
    <em>Top:</em> Full state population including secondary mission states. The complete 12-state lifecycle visible across the 180s window. 
    The transition cascade post-DIFF is the most information-dense region: responding → anchored → treated/detached → sentinel patrol → sweep (micro-met patrol) → scaffold → degrading → dead.
    <em>Bottom:</em> Mean Energy per Role tracked at each timestep. Worker energy (blue) depletes steeply during acute treatment. Scout energy (green) depletes continuously as scouts never stop patrolling. Guard energy (orange) holds high because of
   their long energy budget (8000 units, nearly 3× scout allocation), meaning they remain operational throughout the full 180s window.
  </figcaption>
</figure>

To rigorously stress-test the control architecture, we introduced a second layer of operational complexity. While the underlying anatomical network remains identical, the two synchronous cancers are now configured as molecularly distinct targets.

The swarm must execute three simultaneous tasks: discriminate between separate pathologies, partition the population into isolated sub-swarms, and deliver site-specific interventions without cross-recruitment. This mirrors the clinical reality of a Lynch patient presenting with a primary hepatocellular carcinoma (HCC) and a primary colorectal cancer (CRC) exhibitng divergent phenotypic profiles.

As an agent enters a microvessel, its sensor array samples a three-dimensional marker vector (m), which serves as a proxy for localized biomarkers (VEGF, E-cadherin, and CEA/AFP). The agent classifies the target by computing the Cosine Similarity (Sc) between the detected vector and its pre-loaded reference vectors:
![/ Cosine Similarity between detected vector and its pre-loaded reference vectors](/assets/images/Algorithmic_Implementation_Phenotypic_Discrimination.png)

The simulation configures the target signatures across three dimensions [VEGF · E-cadherin · CEA/AFP] as follows:
  * **HCC Reference Vector (Liver)**: marker_vec = [0.20, 0.75, 0.25], norm = 0.815     
  * **CRC Reference Vector (Colon)**: marker_vec= [0.85, 0.15, 0.50], norm = 0.997

In this validation run, the cross-similarity between the target vectors was calculated at **0.501**. With the classifier's internal decision threshold set at a conservative **0.85**, the agent's discrimiation matrix returned a definitive classification: **DISTINCT**.
![/Signature Discrimination Math](/assets/images/Signature_discrimination_math.png)


Because the signatures are reliably separable, a nanobot detecting the liver signal cannot mistake it for the colon signal. This allows for **Autonomous Partitioning**:
  * **Sub-swarm A** locks onto the HCC channel, converging on the liver.
  * **Sub-swarm B** ignores the liver beacons entirely, continuing through the mesenteric arteries until it detects the CRC signature in the colon.
  * **Zero Cross-Contamination**: CRC-specific payloads are never wasted on HCC sites, and vice versa.
We have now moved beyond "search and destroy" to a system capable of **simultaneous, multi-target differential treatment**.

At the differentiation (DIFF) event, the swarm must decide how to optimize resource allocation across its restricted population. Rather than a simple 50/50 split, the system computes a dynamic **Threat Score** for each localized pathology to determine the ideal sub-swarm distribution ratio.
The framework evaluates urgency using a dual-factor objective function:
![/Threat score formula](/assets/images/Threat_score_formula.png)

For this deployment validation, the weights were initialized at **α** = 0.35 and **β** = 0.65. This aggressive prioritization of the kinetic rate of change **β** explicitly translates clinical oncology protocols for managing microsatellite instability-high (MSI-H) malignancies into autonomous agent logic.

While the absolute magnitude of a molecular signal denotes current mass, its rate of change reflects its immediate proliferation trajectory. The system generated the following operational metrics:
  * **HCC (Liver) Threat Score: 0.0029**
  * **CRC (Colon) Threat Score: 0.0016**

Based on these real-time calculations, the agent population partitioned itself programmatically: **65%** assigned to Sub-swarm B (Liver) and **35%** to Sub-swarm A (Colon).
This distribution pattern operationalizes a core principle of precision oncology: a highly active, rapidly intensifying micro-malignancy can pose a more immediate systemic threat than a larger, slow-growing primary lesion. By biasing the swarm toward kinetic velocity **β**, clinical knowledge is successfully embedded directly into the multi-agent control layer, ensuring autonomous prioritization of the most aggressive disease site.
![/Synchronous primaries Overview](/assets/images/Synchronous_primaries_Overview.png)


<figure>
  <img src="/assets/images/phase32_3D_vascular_network_bottrajectories.jpg" alt="3D vascular network">
  <figcaption>
    <strong>Fig 6: 3D vascular network with the two disease sites visible in distinct anatomical locations.</strong><br>
  </figcaption>
</figure>

<figure>
  <img src="/assets/images/phase32_swarmstatepopulation_overtime.jpg" alt="Swarm Lifecycle — State Population Over Time">
  <figcaption>
    <strong>Fig 7: Swarm Lifecycle — State Population Over Time .</strong><br>
    <em>The DIFF line</em> (t≈20s) marks where the initially uniform population transitions. Unlike Phase 3.1, 
    the dominant post-DIFF state is the large pink "Recovery (tissue repair)" band, reflecting the Phase 3.2 extended recovery mechanics where tissue regeneration 
    is tracked across a longer timescale.
  </figcaption>
</figure>

<figure>
  <img src="/assets/images/phase32_subswarm_population_time.jpg" alt="Sub-Swarm Population Over Time">
  <figcaption>
    <strong>Fig 8: "Sub-Swarm Population Over Time .</strong><br>
    <em>Event:</em> The gray "Unassigned" population drops sharply at DIFF as nanobots receive their sub-swarm designation. Sub-swarm B recieves 65% allocation. While Sub-swarm A gets 35%. 
  </figcaption>
</figure>

<figure>
  <img src="/assets/images/phase32_target_tissue_recovery.jpg" alt="Target Health and Tissue Recovery">
  <figcaption>
    <strong>Fig 9: Target Health and Tissue Recovery.</strong><br>
    <em>Event:</em> Liver cleared at t=19s, then colon at t=22s. Tissue recovery curves diverged based on tumour burden and treatment rate.
  </figcaption>
</figure>

<figure>
  <img src="/assets/images/phase32_per_organ_dynamics.png" alt="Per-organ treatment and recovery dynamics">
  <figcaption>
    <strong>Fig 10: Per-organ treatment and recovery dynamics.</strong><br>
    <em>Top row:</em> Health and recovery with sub-swarm bot count overlay (secondary y-axis).
    <em>Middle row:</em> Rolling event rate: The spike is a log of treatment completion, its height proportional to the number of concurrently anchored nanobots at the moment of clearance.
    <em>Bottom row:</em> Anchored bots + recovery bots over time. The sharp peak (anchored, treating) followed by the gradual 
    tail shows aggressive initial engagement and orderly withdrawal into recovery mode.
  </figcaption>
</figure>

<figure>
  <img src="/assets/images/phase32_role_subswarm_evolution.jpg" alt="Role energy and threat-score resource allocation">
  <figcaption>
    <strong>Fig 11: Role energy and threat-score resource allocation .</strong><br>
    <em>Top:</em> Mean Energy per Role. DIFF marker visible at t≈20s. Worker energy (blue) depletes steadily as the treatment workload is sustained across both sub-swarms simultaneously. 
    Scout energy (green) depletes more slowly in this Phase 3.2 compared to 3.1, as the larger network requires longer patrol coverage.
    Guard energy (orange) holds close to 85% through t=180s.
    <em>Bottom:</em> Threat Score Summary + Resource Allocation, with allocation ratio computed from signal dynamics and applied algorithmically at DIFF time.
  </figcaption>
</figure>


###  Discussion: Validated Architectures vs. Translational Boundaries
**The Demonstrated***: We have established a computational framework for autonomous, multi-site oncology interventions that yields clinically coherent outcomes across three distinct validation tiers:
* Idealized Morphologies (H-Tree Framework): A 200-agent population successfully localized and neutralized two unmapped disease sites within 14.55 seconds, relying solely on stochastic search and local gradient-seeking mechanics.
* Full Anatomical Integration (Phase 3.1): A 250-agent population navigated a realistic abdominal vascular network, intercepting simultaneous targets within 2.0 seconds of injection and executing a complete treatment-to-apoptosis lifecycle.
* Phenotypic Discrimination (Phase 3.2): The control architecture successfully differentiated between two molecularly distinct pathologies (cosine similarity 0.501). The population programmatically partitioned into independent sub-swarms, allocated resources according to a dynamics-weighted Threat Score (65% to Liver, 35% to Colon), and achieved complete target clearance with zero cross-contamination.
  
Every component of this framework is modeled from first principles: Poiseuille flow profiles, Stokes drag mechanics, and Brownian diffusion σ=36nm/ms. The underlying control logic, from the cosine similarity classification matrix to Murray’s Law compliant branching, is structurally integrated with real-world physiological parameters.

**The Undemonstrated (The Physical Translation Gap)**: Manufacturing a 100nm physical device capable of sustained flagellar propulsion, multiplexed molecular sensing, and active immune evasion remains an open micro-engineering frontier. Translating this architecture from in silico to in vivo involves highly complex biological variables that require independent physical validation:
  * Non-Newtonian Rheology: Accounting for localized hemodynamics, shear-thinning behavior, and red blood cell crowding effects at the capillary scale.
  * Active Immunogenicity: Mitigating the reticuloendothelial system's (RES) clearance mechanisms and immediate inflammatory responses to foreign synthetic agents.
  * In Vivo Pharmacokinetics: Mapping the metabolic pathways, systemic degradation timelines, and clearing mechanisms of the primary structural substrates.

If a physical nanobot platform matching these operational parameters can be realized, a milestone increasingly supported by advancements in synthetic DNA origami and enzymatic bio-hybrid propulsion, the decentralized swarm intelligence required to govern it is already computationally validated.

### Future Work: Phase 4 Architecture
The next development phase targets three open operational challenges not yet addressed by the current control framework:
* In Situ Energy Harvesting: The current model operates on a fixed, non-renewable energy budget. While sufficient for brief, acute treatment windows, long-term monitoring requires a sustainable power architecture. Phase 4 will explore metabolic harvesting loops that extract ATP directly from blood glucose substrates to sustain persistent sentinel operations.
* Blood-Brain Barrier (BBB) Transmigration: Navigating neuro-oncological targets requires specialized transport mechanics. Future iterations will model autonomous BBB penetration utilizing biomimetic "Trojan horse" strategies, such as functionalizing agent surfaces with ligands that exploit receptor-mediated transcytosis. *could be something worth looking into*
* Reconfigurable Self-Assembly: To prevent premature renal clearance and enhance localized therapeutic payloads, agents must dynamically adjust their effective volume. We are designing multi-agent rules that allow independent nanobots to temporarily self-assemble into larger functional macrosystems upon target localization, reversing back into individual units for systemic clearance.

---
*P.S:* 
The work continues. This series continues. And if any of this has made you think *"this is worth pursuing"*, or you are working on the physical fabrication, molecular synthesis, or algorithmic refinement of micro-scale robotics systems. I want to hear from you :)

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
