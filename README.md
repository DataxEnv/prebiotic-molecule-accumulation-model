# Computational Modeling Series
### Prebiotic Molecule Accumulation Model

## About This Repository

This repository marks the beginning of my **Computational Modeling & Simulation Series**, where I explore astrobiology-related questions using simple, beginner-friendly computational models.

The goal of this series is not to present fully realistic or exhaustive models, but to **learn how modeling is used as a scientific tool**—especially in fields like astrobiology, where direct experiments are often impossible. Each project is approached as both a scientific investigation and a learning process.

This first project focuses on the **accumulation of prebiotic organic molecules in early Earth surface environments**.


## Context 

This project is closely connected to a Medium article I am writing on **computational modeling and simulations in astrobiology**, where I discuss:

* what scientific models are,
* why scientists build them, and
* why modeling is especially important for studying the origin of life and life beyond Earth.

The Medium article provides the **conceptual background**, while this repository contains the **computational work**.

Medium article: *Upcoming*


## Project summary

This project uses a simple time-dependent computational model to explore how prebiotic organic molecules might accumulate in an open surface environment, such as a pond, on the early Earth.

Rather than modeling specific chemical reactions, the focus is on **overall dynamics**:

* production of molecules,
* destruction and loss processes,
* and periodic environmental input.

The model is implemented as an ordinary differential equation and solved numerically. Sensitivity analyses are used to explore how environmental parameters influence molecule accumulation.

All technical details, assumptions, equations, and interpretations are documented **inside the notebook itself**.


## About the Notebook

The Jupyter Notebook in this repository is intentionally written to be:

* **self-contained** (it can be read independently),
* **step-by-step**, and
* **beginner-friendly**, to reflect my own learning process as a biologist learning computational modeling.

It walks through:

* model assumptions,
* parameter definitions,
* numerical integration,
* visualization of results,
* and basic sensitivity analyses.

To avoid redundancy, technical explanations are intentionally kept within the notebook, while this README serves as a high-level guide


## Tools Used

* Python
* NumPy
* SciPy
* Matplotlib
* Jupyter Notebook

## Why a “Series”?
This project is the first in a planned sequence of modeling efforts aligned with major themes in astrobiology:

1. **Origin & Evolution of Life on Earth** *(this project)*
2. Life in Extreme Environments
3. Planetary habitability
4. Habitable Environments in Our Solar System
5. Exoplanets and Biosignatures

Each project will build gradually in complexity as I continue learning and refining my modeling skills.

## Final Note

This repository represents my **first serious attempt at computational modeling**. The model is intentionally simple, transparent, and reflective. The emphasis is on understanding the modeling process, interpreting results physically and acknowledging limitations.

Feedback, discussion, and curiosity are always welcome.