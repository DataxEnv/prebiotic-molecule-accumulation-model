# Prebiotic Molecule Accumulation Model

## Version History

This repository contains two versions of a dynamical systems model tracking the accumulation of prebiotic organic molecules on the early Earth.

**Version 1** (`prebiotic_molecule_accumulation_model.ipynb`) was developed as a introductory computational exploration. It implements a 
single-compartment linear ODE with constant production, linear decay, and a sinusoidal environmental input term. That model established the basic result: periodic environmental input and loss rate are the key determinants of whether molecules accumulate at all.

**Version 2** (`prebiotic_model_v2.ipynb`) extends that foundation into a more physically and chemically realistic framework. It introduces nonlinear autocatalytic production, Arrhenius-based temperature-dependent degradation, UV photodegradation, wet-dry cycling, and a two-environment 
comparison between a surface hydrothermal pond and a submarine hydrothermal vent system. 

---

# Prebiotic Molecule Accumulation Model: Version 2

## Scientific Background

### The Accumulation Problem

Experimental work has established that the building blocks of life—amino acids, nucleotide precursors, sugars, and lipids—can form abiotically under early Earth conditions. Stanley Miller demonstrated this in 1953, producing amino acids within days by sparking electricity through a simulated early atmosphere (Miller, 1953). John Sutherland and colleagues later showed that RNA nucleotide precursors could form under plausible prebiotic conditions through an alternative synthetic pathway (Powner et al., 2009).

But formation is not accumulation. Prebiotic organic molecules face simultaneous competing pressures: they are produced by abiotic chemistry, but they are also destroyed by thermal degradation, photodegradation by ultraviolet radiation, and physical dilution into the surrounding environment. Whether a molecule persists long enough to participate in further chemistry depends entirely on the balance between these processes, and that balance is strongly environment-dependent.

This is the accumulation problem: Whether the abundant prebiotic molecule they can reach and sustain concentrations high enough to matter chemically. It is a necessary precondition for any origin-of-life scenario. A molecule that forms and immediately degrades contributes nothing to prebiotic chemistry regardless of how efficiently it was synthesised.

Two environments have dominated origin-of-life research as candidate settings for early prebiotic chemistry:

- **Surface hydrothermal ponds:** shallow, geothermally heated pools subject to wet-dry cycling driven by evaporation and rainfall. First proposed by Darwin (1871) as a "warm little pond" and as a plausible setting for life's origin. The hypothesis was formalised computationally by Mulkidjanian et al. (2012), who identified geothermal fields as geochemically favourable environments for prebiotic synthesis, and developed experimentally by Damer and Deamer (2015), who proposed that wet-dry cycling drives combinatorial selection and concentration of organic polymers. The pond's key advantage is physical containment: molecules are retained in a semi-enclosed environment, concentrated by evaporation during dry phases, and periodically refreshed by rainfall during wet phases.

- **Submarine hydrothermal vents:** continuous, mineral-rich fluid systems on the deep ocean floor, shielded from UV radiation and driven by serpentinization reactions. Proposed as a prebiotic setting by Russell and Hall (1997) and developed theoretically by Lane and Martin (2012), who argued that the proton gradients across alkaline vent pore walls provide a direct analogue to the chemiosmotic energy systems of modern cells. The vent's key advantage is sustained energy: continuous fluid flow delivers chemical energy and mineral catalysts indefinitely, shielded
from UV radiation by the overlying ocean water column.

These two environments represent different hypotheses about what the accumulation problem requires. The pond hypothesis prioritises concentration
through physical containment. The vent hypothesis prioritises sustained energy through geochemical flow. This model tests which environment, under physically motivated parameter choices, provides more favourable conditions for organic molecule accumulation.

## Model Overview

Version 2 models the concentration of a generalised prebiotic organic molecule *[X]* in two contrasting early Earth environments: a surface hydrothermal pond and a submarine hydrothermal vent, using a system of ordinary differential equations (ODEs). The model tracks how *[X]* changes over time as a function of production, autocatalytic amplification, and three competing loss mechanisms.

The central scientific question is: under which environmental regime do organic molecules accumulate to concentrations
sufficient to sustain chemistry, and why?

The model is not a *reaction-specific* chemical simulation. *[X]* is a generalised proxy for prebiotic organic polymers: proto-RNA strands, peptides, or nucleotide precursors, rather than a specific molecular species. This abstraction allows the model to focus on accumulation dynamics rather than species-specific reaction pathways, consistent with published dynamical models of prebiotic chemistry (Kauffman, 1993; Hordijk & Steel, 2004).

### Key Upgrades from Version 1

| Feature | Version 1 | Version 2 |
|---|---|---|
| Environments | Single surface environment | Surface pond + hydrothermal vent |
| Production | Constant linear term | Constant + nonlinear autocatalytic term |
| Degradation | Fixed constant | Arrhenius temperature-dependent rate |
| Environmental input | Smooth sinusoidal | Square-wave wet-dry cycling (pond) / constant (vent) |
| UV degradation | Not included | Explicit term. Active in pond, zero in vent |
| Sensitivity analysis | Single parameter variation | Two-parameter heatmap (T vs k_out) |
| Analytical steady state | Not computed | Derived and plotted alongside simulation |

## Mathematical Formulation

The accumulation of *[X]* in each environment is governed by a single ODE:

$$\frac{d[X]}{dt} = P_{\text{env}}(t) + R_{\text{auto}}([X]) - [X]\cdot\left(k_{\text{deg}}(T) + k_{\text{UV}} + k_{\text{out}}\right)$$

The right-hand side contains two production terms and three loss terms. Each term encodes a physically distinct process.

### Production Terms

**Environmental input: $P_{\text{env}}(t)$**

Represents abiotic production of *[X]* from environmental chemistry.

- Hydrothermal vent: constant continuous influx
$$P_{\text{env}}(t) = C_0$$

- Surface pond: pulsed input following a square wave that alternates between wet phase (baseline delivery) and dry phase (delivery stops, evaporation concentrates existing molecules)
$$\text{wet-phase} = \frac{\text{square}(\omega t) + 1}{2}$$
$$P_{\text{env}}(t) = C_0 \cdot \text{wet-phase}$$

The square wave is mapped to oscillate between 1 (wet) and 0 (dry) rather than between +1 and -1, preventing physically impossible negative production rates.

**Autocatalytic production: $R_{\text{auto}}([X])$**

Represents the ability of existing molecules to catalyse their own synthesis — a central concept in prebiotic chemistry (Kauffman, 1993). A saturating nonlinear term is used to prevent unbounded growth:

$$R_{\text{auto}} = \frac{k_a [X]^2}{1 + K_m [X]^2}$$

The [X]² term reflects the requirement for two molecules to interact during autocatalytic replication; one acting as template, one as substrate. This produces sigmoidal (S-shaped) growth: slow at low concentrations, accelerating through a threshold, then saturating as K_m limits the ceiling. During dry phases in the pond, the concentration factor $\alpha$ amplifies autocatalysis to reflect evaporative crowding of existing molecules:

$$R_{\text{auto,dry}} = \frac{k_a [X]^2 \cdot (1 + \alpha\cdot \text{dry-phase})}{1 + K_m [X]^2}$$

### Loss Terms

**Thermal degradation: $k_{\text{deg}}(T)$**

Molecular destruction rate as a function of temperature, modelled using the Arrhenius equation:

$$k_{\text{deg}}(T) = A \cdot e^{-E_a / RT}$$

- A: pre-exponential frequency factor (converted to day⁻¹)
- Ea: activation energy for degradation (J/mol)
- R: universal gas constant (8.314 J/mol·K)
- T: environmental temperature (Kelvin)

Higher temperatures exponentially increase degradation rate. The activation energy Ea = 105,000 J/mol reflects the covalent backbone bonds of prebiotic polymers such as proto-RNA and peptides, consistent with hydrolysis rate data for RNA and related molecules (Levy & Miller, 1998). 

**UV photodegradation: $k_{\text{UV}}$**

A fixed degradation constant representing destruction by ultraviolet radiation. The early Earth lacked an ozone layer, exposing surface environments to significantly higher UV flux than today (Cnossen et al., 2007).

- Surface pond: k_UV = 0.04 day⁻¹ - continuous UV exposure
- Hydrothermal vent: k_UV = 0.0 - completely shielded by the overlying ocean water column

**Advection and outflow — $k_{\text{out}}$**

Represents physical loss through dilution and flushing:

- Surface pond: k_out = 0.01 day⁻¹ - semi-enclosed, evaporative environment retains molecules
- Hydrothermal vent: k_out = 0.08 day⁻¹ - continuous fluid flow flushes molecules into the surrounding ocean (Martin & Russell, 2007)

### Analytical Steady State

Setting d[X]/dt = 0 and ignoring autocatalysis and cycling, the linear steady-state concentration is:

$$[X]^* = \frac{C_0}{k_{\text{deg}}(T) + k_{\text{UV}} + k_{\text{out}}}$$

This expression isolates the contribution of linear chemistry alone — production balanced against loss. It is plotted alongside the full simulation as a reference baseline, allowing direct visual assessment of how much autocatalysis and wet-dry cycling contribute to accumulation beyond what linear chemistry predicts.

---
### Prebiotic Molecule Accumulation Model- v1

This project uses a simple time-dependent computational model to explore how prebiotic organic molecules might accumulate in an open surface environment, such as a pond, on the early Earth.

Rather than modeling specific chemical reactions, the focus is on **overall dynamics**:

* production of molecules,
* destruction and loss processes,
* and periodic environmental input.

The model is implemented as an ordinary differential equation and solved numerically. Sensitivity analyses are used to explore how environmental parameters influence molecule accumulation.

All technical details, assumptions, equations, and interpretations are documented **inside the notebook itself**.


## Tools Used

* Python
* NumPy
* SciPy
* Matplotlib
* Jupyter Notebook