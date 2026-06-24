# Prebiotic Molecule Accumulation Model

## Version History

This repository contains two versions of a dynamical systems model tracking the accumulation of prebiotic organic molecules on the early Earth.

**Version 1** (`prebiotic_molecule_accumulation_model.ipynb`) was developed as a introductory computational exploration. It implements a 
single-compartment linear ODE with constant production, linear decay, and a sinusoidal environmental input term. That model established the basic result: periodic environmental input and loss rate are the key determinants of whether molecules accumulate at all.

**Version 2** (`prebiotic_model_v2.ipynb`) extends that foundation into a more physically and chemically realistic framework. It introduces nonlinear autocatalytic production, Arrhenius-based temperature-dependent degradation, UV photodegradation, wet-dry cycling, and a two-environment 
comparison between a surface hydrothermal pond and a submarine hydrothermal vent system. 

# Prebiotic Molecule Accumulation Model: Version 2

## Scientific Background
A central question in origin-of-life research is not simply whether the building blocks of life could have formed on the early Earth—experimental work has established that they could—but whether those molecules could have accumulated to concentrations high enough to matter chemically.

This is the accumulation problem. Prebiotic organic molecules face competing pressures: they are produced by abiotic chemistry, but they are simultaneously lost to thermal degradation, photodegradation by ultraviolet radiation, and physical dilution into the surrounding environment. Whether a molecule accumulates or disappears depends on the balance between these processes, and that balance differs significantly between environments.

Two environments have dominated origin-of-life research as candidate settings for early prebiotic chemistry:

- **Surface hydrothermal ponds:** shallow, geothermally heated pools subject to wet-dry cycling driven by evaporation and rainfall. First proposed by Darwin (1871) as a "warm little pond", and formalised in prebiotic chemistry by Mulkidjanian et al. (2012) and Damer &  Deamer (2015).

- **Submarine hydrothermal vents:** continuous, mineral-rich fluid systems on the deep ocean floor, shielded from UV radiation and driven by serpentinization reactions. Proposed as a prebiotic setting by Russell & Hall (1997) and developed extensively by Lane & Martin (2012).

This model compares the accumulation dynamics of a generalised prebiotic organic molecule under both regimes, using a system of ordinary differential equations (ODEs) that encode the key physical and chemical differences between them.

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