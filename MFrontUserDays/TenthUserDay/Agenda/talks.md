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

\newcommand{\Frac}[2]{{{\displaystyle \frac{\displaystyle #1}{\displaystyle #2}}}}
\newcommand{\deriv}[2]{{\displaystyle \frac{\displaystyle \partial #1}{\displaystyle \partial #2}}}
\newcommand{\derivtot}[2]{{\displaystyle \frac{\displaystyle \mathrm{d} #1}{\displaystyle \mathrm{d} #2}}}

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

Version `5.0` is a port to the `TFEL` library to `C++-20` which a major
overhaul of the `TFEL/Math` and `TFEL/Material` libraries.

See this page for a full description:
<https://thelfer.github.io/tfel/web/release-notes-5.0.html>

## Improvements to `MFront`

## The MFrontJIT project

# Improving the robustness of implicit schemes using homotopy-based algorithms

- Thomas Helfer
  - CEA Cadarache, IRESNE, DES, DEC, SESC, LMCP, 13 108 St Paul lez Durance, France.

In this talk, we show how to implement and use homotopy-based algorithms
in `MFront` to enhance the robustness of implicit schemes. Such schemes
leads to solve a system of non linear equations of the form:
\[
F\left(\Delta\,\vec{Y}\right) = 0
\]
where \(\Delta\,\vec{Y}\) are the value of the increment of the internal
state variables.

Homotopy algorithms introduces a parameter \(\mu\) continuously
transforming the system of equations to be solved into a simpler one,
the solution of which is trivial. By convention, \(\mu=0\) corresponds to
the simpler system. The initial system is retrieved for a finite
positive value \(\mu_{inf}\).

Homotopy algorithms thus solve a set of non-linear equations:
\[
F_{\mu}\left(\Delta\,\vec{Y}_{\mu}, \mu\right) = 0
\]{#eq:homotopy:mu_system}

by making the \(\mu\) parameter from \(0\) to \(\mu_{inf}\), the
solutions \(\Delta\,\vec{Y}_{\mu}\) evolving from the trivial solution
\(\Delta\,\vec{Y}_{0}\) to the searched one \(\Delta\,\vec{Y}\).

Two algorithms are discussed:

- The first one uses the \(\theta\)-parameter of the implicit scheme as
  the homotopy parameter. For \(\mu=0\), the implicit scheme corresponds
  to the explicit Euler scheme which is trivial for viscoplastic
  behaviours. The initial system is retrieved for \(\mu=\theta\). This
  algorithm is not applicable to time-independent behaviours
  (plasticity, damage) as the system of equations degenerates for
  \(\theta=0\) in those cases.
- The second one uses a new parameter which is only valid for standard
  (visco)-plastic behaviours. The parameter allows to make a smooth
  transition between a system describing an elastic behaviour and the
  system to be solved. We will show that plasticity requires special
  care.

Once the dependency to the homotopy parameter is chosen, the question of
how to make it evolves must be settled. Two strategies will be
discussed:

- The first one consists in starting with \(\mu=\mu_{inf}\) and
  decreasing the value of \(\mu\) by a factor \(2\) if one cannot solve
  System @eq:homotopy:mu_system. In this case, we do not rely on the
  fact that the solution is known for \(\mu=0\), but on the fact that
  System @eq:homotopy:mu_system is simplier to solve when \(\mu\)
  decreases. Once System @eq:homotopy:mu_system can be solved, the
  value of \(mu\) can be gradually increased again. Each step provides a
  better estimate of the solution. This strategy is meaningful if the
  Newton method is satisfying most of the times and allows to treat rare
  divergences.
- The second one uses the implicit function theorem to find an ordinary
  differential equation satisfied by \(\Delta\,\vec{Y}_{\mu}\):
  \[
  \derivtot{\Delta\,\vec{Y}_{\mu}}{\mu}=-\left(\deriv{F_{\mu}}{\Delta\,\vec{Y}_{\mu}}\right)^{-1}\,\deriv{F_{\mu}}{\mu}
  \]
  This ordinary differential equation can be solved by one of the
  Runge-Kutta algorithm available in the `TFEL/Math` library using
  the initial value \(\Delta\,\vec{Y}_{0}\).

Those algorithms are applied to two test cases for which the Newton
method exhibits poor performances.

As a perspective, we will also discuss how those algorithms could be
transposed at the structural scale in a straight-forward manner.

# Automatic differentiation with Enzyme: application to hyperelastic materials

- Arthur Geromel Fischer
  - Recherche et Développement - DORD/PM/ZEU/SIM/ER
  - Manufacture Française des Pneumatiques Michelin
- Thomas Helfer
  - CEA Cadarache, IRESNE, DES, DEC, SESC, LMCP, 13 108 St Paul lez Durance, France.
- Maxence Wangermez
  - CEA Cadarache, IRESNE, DES, DEC, SESC, LMCP, 13 108 St Paul lez Durance, France.

[`Enzyme`](https://enzyme.mit.edu/) is a [`LLVM`](https://llvm.org/)
compiler plugin that performs automatic differentiation (AD) in forward
and reverse mode. It aims at generating high performance gradients of
programs in languages including `C/C++`, `Fortran`, `Julia`, and `Rust`
[@moses_reverse-mode_2021,@moses_scalable_2022].

A library, named
[`TFELMathEnzyme`](https://github.com/thelfer/TFELMathEnzyme), is
currently under development to seamlessly integrate `TFELMath` with
`Enzyme` to compute the derivatives of arbitrary functions.

In this talk, we present some experiments on using `TFELMathEnzyme` in
`MFront` to compute:

- the stress and consistent tangent operator from the free energy of
  some hyperelastic materials.
- the normal and its derivative of some yield surfaces for plastic
  behaviours.

# Modeling Zy-4 cladding failure during a Reactivity Initiated Accident with a GTN non local damage model

- Matthieu Reymond
  - CEA Cadarache, IRESNE, DES, DEC, SESC
  - Ecole Polytechnique Fédérale de Lausanne, Laboratory for Reactor Physics and Systems Behaviour
- Jérôme Sercombe
  - CEA Cadarache, IRESNE, DES, DEC, SESC
- Jérémy Hure
  - CEA Saclay, DES, ISAS, DRMP, SEMI
- Thomas Helfer
  - CEA Cadarache, IRESNE, DES, DEC, SESC
  
Cladding failure during the Pellet-Clad Mechanical Interaction (PCMI)
phase of a Reactivity Initiated Accident (RIA) have been shown to be the
result of a mixed mode mechanism with a brittle fracture of the heavily
hydrided periphery followed by a ductile propagation in the remaining
clad ligament ([@Sercombe2016]).

In this work, the ductile fracture propagation in hydrided Zircaloy-4
cladding tubes is modelled with a Gurson-Tveergard-Needleman (GTN)
damage model. The extended GTN potential for anisotropic and
viscoplastic behaviors is used, considering Hill's yield criterion to
account for the plastic anisotropy of Zircaloy-4 ([@LeSaux2015]). This
GTN model account for void nucleation under shear dominated loading
conditions which are specific to RIA with an additional shear controlled
voids nucleation rate based on the Lode parameter of the plastic strain
rate tensor ([@Macdonald2016], [@Morgeneyer2011]).

However, it is well-known that one of the limitations of local damage
models are their pathological mesh size and orientation dependency. In
this work, the GTN model is regularised with an implicit gradient
formulation with two internal lengths associated respectively to void
nucleation and growth.

The implementation of the implicit gradient formulation with `MFront`
and the `Cast3M` finite element solver is first presented and the
behavior of the regularised GTN model is discussed. In particular, we
will show that the width of the damage band can be controlled with the
characteristics lengths introduced by the implicit gradient formulation.

The `Cast3M` generalised plane strain 2D-(r$\theta$) scheme developed
for RIA simulations is then presented. Simulations results of the recent
experiments carried out in the Nuclear Safety Research Reactor (NSRR,
Japan) are presented and compared to experimental observations. In
particular, our model accurately reproduces the slant fracture beneath
the brittle fracture that is observed in metallographic examinations
(Fig. @fig:GTN).

Perspective of this work is the potential implementation of the
2D-(r$\theta$) scheme and underlying models in the ALCYONE fuel
performance code of the CEA ([@Sercombe2016]).

![Through-wall fracture of a Zircaloy-4 cladding tube obtained with the
GTN model. The typical 45° fracture direction below the brittle fracture
of the hydrided periphery obtained during integral tests (see
metallography, test CABRI REP-Na8) is well
reproduced.](img/ReymondGTN.png){#fig:GTN width=95%}

# An implementation of a unilateral constitutive law for a dynamic damage phase field modeling owing to a FEniCSx/MFront association

- Lamia Mersel
- Pascal Bouda
  - Université Paris-Saclay, CEA, Service d’Etudes Mécaniques et Thermiques, 91191 Gif-sur-Yvette, France

Keywords : phase field for fracture model, isotropic behavior, transient regime, unilaterial condition

The phase field approach to fracture modeling
[@bourdin_variational_2008;@borden_phase-field_2012;@ziaei-rad_massive_2016]
has faced significant advancements in recent years, mostly focusing on
quasi-static regimes in isotropic brittle materials. However, the
challenge of accurately representing unilateral contact conditions
between crack lips remains an obstacle. Indeed, spurious crack can
appear under compressive forces, necessitating a more sophisticated
treatment of the elastic energy density [@chaboche_damage_1992].

The classical splitting models, such as the spherical-deviatoric
[@amor_regularized_2009] and spectral strain decompositions
[@miehe_phase_2010], have shown some limitations when applied to dynamic
fracture regimes or anisotropic materials where the orthogonality
between stress and strain eigenvalues is not preserved. To this end, a
novel approach that leverages the square root of the elastic tensor
within the Kelvin decomposition framework
[@desmorat_dissymetrie_2000;@francois_damage_2012;@he_closed-form_2019],
offering a promising alternative for capturing the complex behavior of
materials under transient loading conditions.

We present an implementation of this model using MFront, integrated with
the open-source FEniCSx software [@baratta_dolfinx_2023], a powerful
tool for solving partial differential equations (PDEs). Building upon
the previous work of [@bleyer_overview_2020] on fenics-mgis module, we
have updated and extended their contributions to accommodate the latest
features of FEniCS.

The presentation will detail our computational framework and discuss on
a comparative analysis of the various elastic energy density
decompositions in the context of transient brittle fracture mechanics,
providing valuable insights for future research in this domain.

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

# Zirconium alloys high temperature oxidation laws determination and MFront implementation

- Ali Charbal
  - CEA Saclay, ISAS, DES, DRMP, SRMA, LA2M, 91 191 Gif sur Yvette, Essonne, France
- Thomas Guilbert
  - CEA Saclay, ISAS, DES, DRMP, SRMA, LA2M, 91 191 Gif sur Yvette, Essonne, France
- Maxence Wangermez
  - CEA Cadarache, IRESNE, DES, DEC, SESC, LSC, 13 108 St Paul lez Durance, France.
- Jean-Christophe Brachet
  - CEA Saclay, ISAS, DES, DRMP, SRMA, LA2M, 91 191 Gif sur Yvette, Essonne, France
- Thomas Helfer
  - CEA Cadarache, IRESNE, DES, DEC, SESC, LSC, 13 108 St Paul lez Durance, France.

The knowledge of the behavior of cladding materials under hypothetical
accident conditions, such as Loss-of-Coolant Accident (LOCA), is
essential for the "design" of emergency cooling systems for nuclear
reactors. Specifically, it is crucial to ensure that the geometry of the
fuel assemblies remains "coolable" at every point during and after the
transient phase.

During LOCA transients, the cladding experiences rapid heating due to
the residual heat from the uranium pellets combined with the loss of
primary coolant. After a first temperature peak is reached, the cladding
continues to heat up more slowly, allowing time for the emergency
cooling systems to operate. The evaporation of water from the primary
circuit as steam causes accelerated oxidation of the cladding materials
at high temperatures. To prevent cladding fragmentation due to excessive
oxidation, the fuel cladding materials must also meet certain
physicochemical requirements (oxidation and/or hydriding limited to a
certain level) to ensure their strength during the final quench (and
ensure sufficient "post-quench" strength and ductility).

At CEA experimental methods have been developed over the two past
decades in order to establish oxidation laws that describe the cladding
tubes behaviors at high temperature under water vapor atmosphere. The
evolutions of the mass gain, the zirconia and alpha(O) thicknesses were
determined for various alloys. Recently these laws were implemented in
`MFront`. After presenting an overview of the different CEA experimental
rigs and methodologies used for HT oxidation kinematic laws
determination, examples of oxidation laws implemented in `MFront` are
illustrated.

<!--
# VTT?
-->

# References