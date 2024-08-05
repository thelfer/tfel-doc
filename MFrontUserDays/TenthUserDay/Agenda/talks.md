---
title: Agenda of the tenth `MFront` User Meeting
author: Thomas Helfer, Maxence Wangermez
date: 02/10/2023
toc: true
toc-depth: 1
numbersections: true
lang: en-EN
geometry:
- paper=a4paper
- margin=2cm
link-citations: true
colorlinks: true
figPrefixTemplate: "$$i$$"
tabPrefixTemplate: "$$i$$"
secPrefixTemplate: "$$i$$"
eqnPrefixTemplate: "($$i$$)"
bibliography: bibliography.bib
csl: iso690-numeric-en.csl
---

<!--
pandoc -f markdown+tex_math_single_backslash --filter pandoc-crossref --citeproc talks.md -o talks.pdf
-->

# Back to basics: implementing point-wise models with `MFront`

- Maxence Wangermez
  - CEA Cadarache, IRESNE, DES, DEC, SESC, LSC, 13 108 St Paul lez Durance, France.
- Thomas Helfer
  - CEA Cadarache, IRESNE, DES, DEC, SESC, LSC, 13 108 St Paul lez Durance, France.

# Overview of `TFEL-5.0`

- Thomas Helfer
  - CEA Cadarache, IRESNE, DES, DEC, SESC, LMCP, 13 108 St Paul lez Durance, France.
- Maxence Wangermez
  - CEA Cadarache, IRESNE, DES, DEC, SESC, LMCP, 13 108 St Paul lez Durance, France.

## Improving the robustness of implicit schemes using homotopy-based algorithms

# Automatic differentiation with Enzyme

- Arthur Geromel Fischer
  - Recherche et Développement - DORD/PM/ZEU/SIM/ER
  - Manufacture Française des Pneumatiques Michelin
- Thomas Helfer
  - CEA Cadarache, IRESNE, DES, DEC, SESC, LMCP, 13 108 St Paul lez Durance, France.
- Maxence Wangermez
  - CEA Cadarache, IRESNE, DES, DEC, SESC, LMCP, 13 108 St Paul lez Durance, France.

# Modeling clad failure in Reactivity Injection Accident by non local constitutive equations

- Matthieu Reymond
  - CEA Cadarache, IRESNE, DES, IRESNE, DEC, SESC, LEVA, 13 108 St Paul lez Durance, France.
- Jérôme Sercombe
  - CEA Cadarache, IRESNE, DES, IRESNE, DEC, SESC, LEVA, 13 108 St Paul lez Durance, France.

# An implementation of a unilateral constitutive law for a dynamic damage phase field modeling owing to a FEniCSx/MFront association

- Lamia Mersel
- Pascal Bouda
  - Université Paris-Saclay, CEA, Service d’Etudes Mécaniques et Thermiques, 91191 Gif-sur-Yvette, France

# Comportement polycristallin de l’UO2: développements, au format `MFront`, de lois micromécaniques basées sur l’approche NTFA et validations

- Rodrigue Largenton

# No title yet

- Olivier Fandeur

# Micromechanical modelling of closed-cell polymer foams under compression by coupling `FEniCS` with `MFront`

- Lei Liu

# Implementation of Hoek-Brown Failure Criterion in MFront and its application to the rock with up to 3 ubiquitous joint sets

- Mehran Ghasabeh
  - TU Bergakademie Freiberg, Institute of Geotechnics, Chair of Soil Mechanics and Foundation Engineering, Gustav-Zeuner-1, 09599,Freiberg, Germany

# MFront/MGIS for the solution of a fictitious non-linear grid problem in Arbitrary Lagrange-Euler CFD simulations

- Vincent Faucher
  - CEA Cadarache, IRESNE, DES, IRESNE, DTN, 13 108 St Paul lez Durance, France.
- Maria-Adela Puscas
  - CEA Sacalay, ISAS, DES, DM2S, STMF, LMSF

The proposed talk introduces an original usage of `MFront` in the field
of Computational Fluid Dynamics with moving fluid mesh in Arbitrary
Lagrange Euler description.

A promising way to manage very large mesh mesh distortions due to moving
boundaries is to assign to the fluid grid itself a fictitious mechanical
problem with a material model close to incompressibility as a condition
to prevent excessive shear of the mesh cells.

`MFront` and its generic interface `MGIS` thus appear as the ideal tools
to implement this strategy in `TrioCFD` software based on the [`TRUST`
platform](https://github.com/cea-trust-platform). It provides the robust
and straightforward integration of highly non-linear models such as
Ogden constitutive law and at the same time avoids adding in `TrioCFD`
any method and algorithm specific to structural mechanics that would not
belong and would generate maintenance constraints in the program.

Finally, the `MGIS` implementation introduced in `TrioCFD` is also
directly inherited from the one available in the new generation program
for mechanics `MANTA` [@jamond_manta_2022;@jamond_manta_2024]
for another example of positive resources sharing and cooperation.


# Advanced material modeling in FEniCSx

- Jeremy Bleyer
 - École nationale des ponts et chaussées

[`FEniCSx`](https://fenicsproject.org/) is a powerful code-generation
software package for automating complex PDE systems. While hyperelastic
materials can be formulated using UFL operators and automatic
differentiation (AD) of a free energy potential, more complex materials
often require solving for the evolution of internal state variables at
the material point level.

However, this two-level procedure is not easily enabled by
[`FEniCSx`](https://fenicsproject.org/), which is not designed for it.
This work aims to provide a simple way of defining such implicit
constitutive equations, albeit with a slight computational and memory
overhead.

The library can handle various implementation types of constitutive
equations, starting with user-defined constitutive updates using
[`NumPy`](https://numpy.org/) and
[`JAX`](https://jax.readthedocs.io/en/latest/quickstart.html), which are
ideal for educational purposes [@latyshev_framework_2024]. For better
computational performance and behavior complexity, the library also
supports material models defined by the `MFront` code generator.

Additionally, advanced material modeling techniques such as multiscale
material models using Thermodynamics-based Artificial Neural Networks
(TANN) can also be used very easily.

<!--
# VTT?
-->

# References