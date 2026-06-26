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

Represents the ability of existing molecules to catalyse their own synthesis, a central concept in prebiotic chemistry (Kauffman, 1993). A saturating nonlinear term is used to prevent unbounded growth:

$$R_{\text{auto}} = \frac{k_a [X]^2}{1 + K_m [X]^2}$$

The [X]² term reflects the requirement for two molecules to interact during autocatalytic replication; one acting as template, one as substrate. This produces sigmoidal (S-shaped) growth: slow at low concentrations, accelerating through a threshold, then saturating as K_m limits the ceiling. During dry phases in the pond, the concentration factor $\alpha$ amplifies autocatalysis to reflect evaporative crowding of existing molecules:

$$R_{\text{auto, dry}} = \frac{k_a [X]^2 \cdot (1 + \alpha\cdot \text{dry-phase})}{1 + K_m [X]^2}$$

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

**Advection and outflow: $k_{\text{out}}$**

Represents physical loss through dilution and flushing:

- Surface pond: k_out = 0.01 day⁻¹ - semi-enclosed, evaporative environment retains molecules
- Hydrothermal vent: k_out = 0.08 day⁻¹ - continuous fluid flow flushes molecules into the surrounding ocean (Martin & Russell, 2007)

### Analytical Steady State

Setting d[X]/dt = 0 and ignoring autocatalysis and cycling, the linear steady-state concentration is:

$$[X]^* = \frac{C_0}{k_{\text{deg}}(T) + k_{\text{UV}} + k_{\text{out}}}$$

This expression isolates the contribution of linear chemistry alone — production balanced against loss. It is plotted alongside the full simulation as a reference baseline, allowing direct visual assessment of how much autocatalysis and wet-dry cycling contribute to accumulation beyond what linear chemistry predicts.

## Parameter Justification

All parameters are expressed in normalised concentration units and days. The pre-exponential factor A is converted from s⁻¹ to day⁻¹ by multiplying by 86,400 (seconds per day) to ensure unit consistency across the simulation.

| Parameter | Symbol | Value | Justification |
|---|---|---|---|
| Universal gas constant |  $R$ | $8.314 \text{ J/mol}\cdot\text{K}$ | Physical constant |
| Baseline production | $C_0$ | $0.05 \text{ day}^{-1}$ | Normalised abiotic production rate |
| Autocatalytic rate | $k_a$ | $0.08 \text{ day}^{-1}$ | Mathematical framework for autocatalytic sets (Kauffman, 1993) |
| Saturation constant |$K_m$ | $5.0 \text{ a.u.}$ | Michaelis-Menten type half-saturation concentration |
| Activation energy | $E_a$ | $105,000 \text{ J/mol}$ | Hydrolytic breakdown of prebiotic nucleobases (Levy & Miller, 1998) |
| Pre-exponential factor | $A$ | $8.64 \times 10^{14} \text{ day}^{-1}$ | Standard unimolecular degradation estimate, converted to day⁻¹ |
| Pond temperature | $T_{\text{pond}}$ |  $303 \text{ K } (30^\circ\text{C})$ | Continental geothermal field baseline (Mulkidjanian et al., 2012) |
| Pond UV rate | $k_{\text{UV\_pond}}$ | $0.04 \text{ day}^{-1}$ | Archean surface solar UV flux without ozone (Cnossen et al., 2007) |
| Pond outflow | $k_{\text{out\_pond}}$ | $0.01 \text{ day}^{-1}$ | Semi-enclosed evaporative basin (Damer & Deamer, 2015) |
| Dry-phase factor | $\alpha$ |  $2.5$ | Evaporative concentration factor (dimensionless) |
| Cycling frequency | $\omega$ | $2\pi/10 \text{ day}^{-1}$ | Angular frequency for a 10-day wet-dry cycle (Damer & Deamer, 2015) |
| Vent temperature |  $T_{\text{vent}}$ | $363 \text{ K } (90^\circ\text{C})$ | Core fluids of alkaline systems like Lost City (Kelley et al., 2005) |
| Vent UV rate | $k_{\text{UV\_vent}}$ | $0.0 \text{ day}^{-1}$ | Complete UV shielding at depth |
| Vent outflow |$k_{\text{out\_vent}}$ | $0.08 \text{ day}^{-1}$ | Open flow-through system (Martin & Russell, 2007) |
| Simulation duration | $t_{\text{end}}$ | $365 \text{ days}$ | One year. Sufficient to reach or approach steady state |
| Initial condition | $[X]_0$ | $0.0 \text{ a.u.}$ | No prior accumulation assumed. Conservative baseline |


## Results and Interpretation

### Figure 1 — Two-Environment Comparison

![Two-Environment Comparison](two_environment_comparison.png)

**Surface Pond**

The surface pond simulation produces a rapid early accumulation 
that decelerates gradually into a stable plateau. Starting from 
zero, [X] rises steeply through the first 50 days, driven by 
the combined effect of environmental input and early autocatalytic 
amplification. Growth slows as the system approaches the 
autocatalytic saturation ceiling, stabilising at approximately 
1.24 — well above the analytical steady state of 0.987.

The plateau is sustained by the interplay between wet-dry cycling 
and autocatalysis. During dry phases, outflow drops to zero and 
evaporative concentration amplifies autocatalytic production. 
During wet phases, fresh precursor input refreshes the system. 
The result is a stable dynamic equilibrium that significantly 
exceeds what linear chemistry alone predicts.
This behaviour demonstrates that wet-dry cycling and
autocatalytic production together generate accumulation that
linear chemistry cannot predict and that the vent environment
cannot achieve. It is consistent with the coupled-phase
accumulation model proposed by Damer and Deamer (2015).

**Hydrothermal Vent**

The hydrothermal vent simulation reaches its analytical steady
state of 0.067 within approximately five days and remains
there for the entire 365-day simulation without deviation. The concentration ceiling is approximately 18 times lower than 
the pond's peak accumulation.

This result reflects the thermal trap inherent to
high-temperature vent environments. At 90°C, the
Arrhenius-computed k_deg alone exceeds both the baseline
production rate (C₀ = 0.05 day⁻¹) and the maximum
autocatalytic rate constant (k_a = 0.08 day⁻¹). Autocatalysis
never meaningfully engages because [X] cannot reach the
threshold concentration required for the nonlinear term to
become significant. The vent reaches equilibrium between
continuous production and continuous loss — and stays there.

The vent's continuous production and UV shielding provide real
advantages not captured by this model. But under an
accumulation-only framework, thermal degradation and rapid
outflow together prevent the concentration buildup that
autocatalytic chemistry requires.

### Figure 2 — Sensitivity Analysis

![Sensitivity Analysis](sensitivity_analysis.png)


The heatmap shows steady-state concentration [X]* across a
broad temperature and outflow parameter space, computed
analytically without autocatalysis or cycling. Accumulation
is maximised in the lower-left region — low temperatures and
low outflow rates. The transition from high to low
accumulation is steep along the temperature axis and more
gradual along the outflow axis, indicating that temperature
is the dominant control on steady-state concentration under
these parameter choices.

The two environment markers illustrate this directly. The
pond marker sits within the mid-bright accumulation zone,
consistent with the simulation result. The vent marker sits
deep in the dark low-accumulation zone, far from any
meaningful accumulation threshold.

The black region in the upper-left corner represents
parameter combinations where [X]* exceeds the colorbar cap
of 2.0 — extreme low-temperature, low-outflow conditions
beyond the range of either modelled environment.

Together, the three panels — pond simulation, vent
simulation, and sensitivity heatmap — support a consistent
conclusion: accumulation of prebiotic organic molecules is
strongly favoured by cool temperatures, physical containment,
and periodic concentration events. The surface pond
satisfies all three. The hydrothermal vent satisfies none
under these parameter regimes.

## Limitations

1. **Well-mixed compartments.** Each environment is modelled
   as a single spatially homogeneous box. Spatial gradients
   within environments — such as temperature and pH gradients
   across a vent pore wall — are not captured.

2. **Single-species proxy.** [X] represents a generalised
   prebiotic organic molecule rather than a specific chemical
   species. Degradation kinetics are represented by a single
   average activation energy. In reality, different molecular
   species degrade at different rates under different
   conditions.

3. **Abundant precursors assumed.** Raw chemical feedstocks
   are assumed to be non-limiting and constant. This isolates
   accumulation dynamics from upstream supply constraints but
   does not reflect environments where precursor availability
   is itself a limiting factor.

4. **No exchange between environments.** The pond and vent
   are modelled as independent systems. Material transport
   between surface and subsurface environments is not
   included.

5. **Idealised wet-dry cycling.** The pond's wet-dry cycling
   is represented as a square wave with a fixed 10-day
   period. Real Hadean cycling would have been irregular and
   climate-dependent.

6. **Arrhenius degradation uses fixed activation energy.**
   A single Ea is applied across all degradation pathways.
   In reality, different molecular species and bond types
   degrade at different rates under different temperatures.
   **Dry-phase concentration factor.** During dry phases, the
concentration factor α amplifies the autocatalytic production
term to reflect evaporative crowding of existing molecules.
This is a simplification — physical evaporation would scale
local concentration across all ODE terms simultaneously. A
more rigorous implementation would define a time-dependent
local concentration variable γ(t) = α during dry phases,
applied globally. This is noted as a direction for future
refinement.

8. **Sensitivity analysis is two-dimensional.** Temperature
   and outflow are varied simultaneously, but UV
   degradation rate, autocatalytic parameters, and
   production rate are held fixed. A fuller sensitivity
   analysis would explore the full parameter space.

9. **Accumulation-only framework.** The model does not
   capture redox chemistry, mineral catalysis, or reaction
   network complexity — all of which are genuine advantages
   of hydrothermal vent environments not represented here.

## References

Bada, J.L. & Lazcano, A. (2002). Some like it hot, but not
the first biomolecules. *Science*, 296(5575), 1982–1983.

Cnossen, I. et al. (2007). Habitat of early life: Solar
X-ray and UV radiation at Earth's surface 4–3.5 billion
years ago. *Journal of Geophysical Research*, 112, E02008.

Damer, B. & Deamer, D. (2015). Coupled phases and
combinatorial selection in fluctuating hydrothermal pools:
A scenario to guide experimental approaches to the origin
of cellular life. *Life*, 5(1), 872–887.

Hordijk, W. & Steel, M. (2004). Detecting autocatalytic,
self-sustaining sets in chemical reaction systems. *Journal
of Theoretical Biology*, 227(4), 451–461.

Kauffman, S.A. (1993). *The Origins of Order:
Self-Organization and Selection in Evolution.* Oxford
University Press.

Kelley, D.S. et al. (2005). A serpentinite-hosted
ecosystem: The Lost City hydrothermal field. *Science*,
307(5714), 1428–1434.

Lane, N. & Martin, W.F. (2012). The origin of membrane
bioenergetics. *Cell*, 151(7), 1406–1416.

Levy, M. & Miller, S.L. (1998). The stability of the RNA
bases: Implications for the origin of life. *PNAS*,
95(14), 7933–7938.

Martin, W. & Russell, M.J. (2007). On the origin of
biochemistry at an alkaline hydrothermal vent.
*Philosophical Transactions of the Royal Society B*,
362(1486), 1887–1926.

Miller, S.L. (1953). A production of amino acids under
possible primitive Earth conditions. *Science*, 117(3046),
591–592.

Mulkidjanian, A.Y. et al. (2012). Origin of first cells
at terrestrial, anoxic geothermal fields. *PNAS*, 109(14),
E821–E830.

Powner, M.W., Gerland, B. & Sutherland, J.D. (2009).
Synthesis of activated pyrimidine ribonucleotides in
prebiotically plausible conditions. *Nature*, 459,
239–242.

Russell, M.J. & Hall, A.J. (1997). The emergence of life
from iron monosulphide bubbles at a submarine hydrothermal
redox and pH front. *Journal of the Geological Society*,
154(3), 377–402.
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