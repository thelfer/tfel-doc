---
title: Agenda of the seventh `MFront` User Meeting
author: Thomas Helfer
date: 211/2021
toc: true
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

# Overview of `TFEL-4.0` and `MGIS-2.0`.

- Thomas Helfer
  - CEA Cadarache, IRESNE/DES/DEC/SESC/LSC, 13 108 St Paul lez Durance, France.

This talk will:

- Present arious applications of `MFront` during the past year which are not
  described in the other talks.
- Discuss some new features of Version 4.0 of the [`TFEL`
  project](http://tfel.sourceforge.net) and Version 2.0 of `MGIS`.

Both projects are now based on the `C++-17` standard.

As a consequence, the `TFEL/Math` library have been deeply overhauled.
From the end user point of view, the library:

- better handles quantities, i.e. values with units, allowing the
  compiler to perform dimensional analysis.
- supports higher order objects.

Quantities are now supported by the implicit domain specific languages
(DSLs) of `MFront` and the DSL dedicated to material properties.

# Development of a Novel Damage Model for Concrete Subjected to Creep

- Alexandre Gangnant
  - Sixense necs, Sceaux, France 
- Michel Sagno
  - Sixense necs, Sceaux, France 
- Jefri Draup
  - EDF Energy R&D UK Centre, Manchester, UK 
- Thomas Helfer
  - CEA Cadarache, IRESNE/DES/DEC/SESC/LSC, 13 108 St Paul lez Durance, France.

Concrete exhibits visco-plastic behaviour when subjected to high
temperature whilst supporting an external load; this component of
plasticity is referred to as load induced thermal strain (LITS), which
is also known as transient thermal creep (TTC). LITS phenomena can be
important in pre-stressed concrete structures. Indeed, this can
potentially lead to a loss in pre-stress, and also residual tensile
stress development under transient thermal conditions. Hence, structures
which have been subjected to a high temperature thermal loading cycle
may experience cracking and subsequent loss of rigidity; this could
ultimately lead to a loss of functionality or even structural failure.
In addition to that, under pre-stressed structures, basic creep effects
might be observed in concrete and could contribute to the failure .

Thus, in a process of catching physical & mechanical phenomena that
could occur during the lifetime of a concrete structure (e.g.
Pre-stressed concrete vessel of an AGR), a novel model has been
developed in order to take into account the damage, creep and LITS
phenomena.

Firstly, a damage model (named FLB based on Mazars) coupled with LITS
model has been developed using Mfront and tested. Secondly, basic creep
has been added to this initial constitutive law. This latter coupled
model is based on simple viscoelastic creep model (Burger) in a
framework a quasi-brittle material such as concrete. The constitutive
behaviour is coupled to the damage law via the total stress tensor. By
decomposing the elastic, creep and thermal strains, the damage evolution
is driven from the elastic strains and in a proportion of creep strains.
Finally, the effective stress is computed and used to obtain the total
stress at each iteration with unilateral effect (rigidity recovery due
to crack closure) included.

This type of model is most suitable for fully implicit integration
schemes and will allow more accurate assessment of structural damage
from transient thermal events, such as fire. This work is only based on
the coupling damage and creep where thermal effect has been disabled on
purpose.

![Evolution of creep stains & damage under constant load followed by an
unloading by applying the new developed damage creep model](img/ConcreteBehaviour.png)

# A novel approach of using existing implementations of constitutive material models in any numerical code interfacing with `MFront`

![Vertical displacement after the excavation of a gallery in a clay formation using an advanced hypoplastic model](img/PlaxisVsOpenGeoSys.png)

- Eric Simo
  - BGE Technology, Germany
- Thomas Helfer (CEA)
  - CEA Cadarache, IRESNE/DES/DEC/SESC/LSC, 13 108 St Paul lez Durance, France.
- Thomas Nagel
  - TU Bergakademie Freiberg, Freiberg, Germany.
- David Mašín
  - Charles University in Prague Faculty of Science Albertov 6, 128 43 Prague 2, Czech Republic 

# Implementation of a coupled thermo-elasto-viscoplastic polycrystalline finite element model using `FEniCS` (`mgis.fenics`) and `MFront`

- Nikhil Mohanan
  - Laboratoire de Mécanique des Solides (LMS), CNRS UMR 7649, Ecole Polytechnique, Institut Polytechnique de Paris, 91128 Palaiseau Cedex, France
- Jérémy Bleyer
  - Laboratoire Navier, CNRS UMR 8205, ENPC, Univ Gustave Eiffel, F-77447 Marne-la-Vallée, France
- Thomas Helfer
  - CEA Cadarache, IRESNE/DES/DEC/SESC/LSC, 13 108 St Paul lez Durance, France.
- Manas Upadhyay
  - Laboratoire de Mécanique des Solides (LMS), CNRS UMR 7649, Ecole Polytechnique, Institut Polytechnique de Paris, 91128 Palaiseau Cedex, France

# Implementation of a polycristalline model to simulate the radiation induced deformation of Zircaloy cladding tubes using the MFront code generation tool

![""](img/ZircaloyHomogeneization.png)

- Cécilia Gicquel
  - CEA Saclay ISAS/DES/DMN/SRMA/LA2M, 91191 Gif-sur-Yvette cedex.
- Renald Brenner
  - CNRS, d'Alembert
- Renaud Masson
  - CEA Cadarache, IRESNE/DES/DEC/SESC/LSC, 13 108 St Paul lez Durance, France.
- Fabien Onimus
  - CEA Saclay ISAS/DES/DMN/SRMA/LA2M, 91191 Gif-sur-Yvette cedex.


The aim of this work is to implement a polycrystalline model in the
MFront code generation tool to simulate the radiation induced
deformation of cladding tubes made of Zircaloy. Thanks to Mandel’s
correspondence principle [@mandel_mecanique_1966] and thanks to an
internal variables formulation derived by Ricaud and Masson
[@ricaud_effective_2009], equivalent to Schapery’s collocation method
[@schapery_approximate_1962], the self-consistent scheme
[@kroner_self-consistent_2001] is applied to determine the behaviour of
a non-ageing, linear viscoelastic polycrystal. The coefficients of the
model are calibrated using the experimental data from Soniak et al.
[@soniak_irradiation_2002]. The numerical simulation is then compared,
at the macroscopic and at the local scale, with the quasi-elastic
approach developed by Brenner et al. [@brenner_quasi-elastic_2002] and
adapted to in-reactor deformation by Onimus et al.
[@onimus_polycrystalline_2021].

# Implementing geomechanical models in `MFront`/`OpenGeoSys` for hydrogeological and geotechnical applications

![Foundation problem under gravity: Comparison of the Mohr-Coulumb model and the
modified Cam clay model at the onset of plastic localization.](img/MoC_vs_MCC_epsQ.png "")

- Christian B. Silbermann
  - TU Bergakademie Freiberg, Freiberg, Germany.
- Dominik Kern
  - TU Bergakademie Freiberg, Freiberg, Germany.
- Francesco Parisio
  - TU Bergakademie Freiberg, Freiberg, Germany.
- Thomas Nagel
  - TU Bergakademie Freiberg, Freiberg, Germany.

In order to properly simulate complex hydrogeological or geotechnical processes it is
crucial to reasonably capture the mechanical behavior of the geomaterials 
(soils or rocks, specifically). Mechanically speaking, a
constitutive relation is needed for the calculation of the effective
stress tensor as part of the constitutive closing of the thermo-hydro-mechanical
models used. Therefore, a variety of inelastic (geo)mechanical
material models is available in literature, such as the basic and more generally 
applicable Mohr-Coulomb and Cambridge (Cam) clay models along with more advanced 
models for specific materials and applications such as models for rock failure in
the brittle-ductile transition or for creep in rock salt formations.

Many formulations have in comman that they need to capture effects like
elasto-plastic deformation, irreversible (plastic) pore compaction / consolidation,
hardening and softening, different loading and unloading
stiffness, and temperature dependence. The goal of this work is to highlight recent
implementations of geomechanical models in `MFront`/`OpenGeoSys`, such as

- a consistent and clear presentation of the basic modified Cam clay model for
cohesive soils
- models for primary, secondary and tertiary creep of rock salt
- rock mechanical models at temperatures and pressures spanning the brittle-ductile
transition

This talk describes the implementation of several material models for small strains 
in the open-source multi-field finite element software
[`OpenGeoSys`](https://www.opengeosys.org/) based on `MFront`. For this,
the set of constitutive equations and their implementation are outlined. Then, 
exemplary numerical studies are presented for typical hydrogeological and geotechnical 
applications demonstrating significant differences with respect to the chosen material 
model.

# Validation and performance of Cosserat media in small deformation

![Results obtained for a torsion Test a) Comparison with the analytical
solution in elasticity￼. b) Magnitude of the rotation of the
microstructure](img/CosseratResults.png "")

- Tamara Dancheva
  -  Basque Center for Applied Mathematics
- Raffaele Russo
  - University of the Basque Country
- Flavien Ghiglione
  - MINES ParisTech
- Unai Alonso
  - University of the Basque Country
- Michael Barton
  - Basque Center for Applied Mathematics

In this talk, we will dive into the implementation and performance of a
Generalized Continua-based model, more specifically within the framework
of Cosserat media [@cosserat1909theorie]. We aim to use this model for
simulating the realistic behaviour of metal alloys during the process of
machining (a material removal process during which the material
undergoes severe plastic deformation). We developed this model using the
open-source FEM framework [`FEniCS`](https://fenicsproject.org/) and
`TFEL/MFront` using the
[`mgis.fenics`](https://thelfer.github.io/mgis/web/mgis_fenics.html)
module [@bleyer_overview_2020]).

We present our work on the validation and performance of the elastic and
elastoplastic model ([@forest2003elastoviscoplastic],
[@russo2020thermomechanics]) in small deformation for a representative
test case i.e. torsion test of a cylindrical specimen, by comparing the
results from [FEniCS](https://fenicsproject.org/) and TFEL/MFront with
the analytical/semi-analytical solution and a solution obtained with the
ZSet framework, using the solver Zebulon.

# `MFront`, `code_aster` and wood: mechanical behaviors for structural applications and cultural heritage conservation.

![Modelling of Drying schrinkage of wood with `code_aster` and `MFront`](img/WoodViscelasticity.jpg ""){width=80%}

- Lorenzo Riparbelli
  - University of Florence, Dagri Dept., Florence, Italy.
- Ioannis Christovasilis
  - Aether Engineering, Florence, Italy.
- Marco Fioravanti
  - University of Florence, Dagri Dept., Florence, Italy.

# Modélisation du freinage à partir d’une formulation thermomécanique

![Modélisation du freinage avec `code_aster` et `MFront`](img/Braking.png ""){width=80%}

- Thierry Ndzana Satoh
  - LMGC UMR5508 CNRS, Université de Montpellier, Montpellier, France.
  - SAFRAN Landing Systems, Velizy, France
- Mathieu Renouf
  - LMGC UMR5508 CNRS, Université de Montpellier, Montpellier, France.
- André Chrysochoos
  - LMGC UMR5508 CNRS, Université de Montpellier, Montpellier, France.

# Neural network based constitutive models implemented using TensorFlow inside `MFront`

- Marius Duvillard
  - CEA Cadarache, IRESNE/DES/DEC/SESC/LSC, 13 108 St Paul lez Durance, France.
- Loïc Giraldi
  - CEA Cadarache, IRESNE/DES/DEC/SESC/LSC, 13 108 St Paul lez Durance, France.
- Thomas Helfer
  - CEA Cadarache, IRESNE/DES/DEC/SESC/LSC, 13 108 St Paul lez Durance, France.

# The Hybrid High Order method in nonlinear solid mechanics with `MFront` and `MGIS`

![Comparison of the hydrostatic pressure computed using the Hybrid High Order (HHO) method and standard finite elements](img/HHO.png ""){width=80%}

- David Siedel
  - CEA Cadarache, IRESNE/DES/DEC/SESC/LSC, 13 108 St Paul lez Durance, France.
  - CEA Saclay, ISAS/DES/DM2S/SEMT/LM2S, 91191 Gif-sur-Yvette cedex.
  - Mines ParisTech, PSL University, Centre des matériaux, CNRS UMR
    7633, 91003 Évry, France.
- Thomas HELFER
  - CEA Cadarache, IRESNE/DES/DEC/SESC/LSC, 13 108 St Paul lez Durance, France.
- Olivier FANDEUR
  - CEA Saclay, ISAS/DES/DM2S/SEMT/LM2S, 91191 Gif-sur-Yvette cedex.
  - IMSIA, UMR 9219, CNRS-EDF-CEA-ENSTA Paris.
- Nicolas Pignet
  - EDF R&D, 
- Jacques BESSON
  - Mines ParisTech, PSL University, Centre des matériaux, CNRS UMR
    7633, 91003 Évry, France.

The standard finite element method suffers from volumetric locking when
nearly- incompressible materials are considered, leading to spurious
oscillations of the hydrostatic pressure. In order to circumvent this
phenomenon, the recent HHO (Hybrid High Order)
[@abbas_hybrid_2018;@abbas_hybrid_2019] method is considered. This
method has several advantages over standard finite elements:

- It is robust to volumetric locking phenomena in primal formulation.
- Polyhedral elements are natively supported.
- The displacements and the strains have the same approximation order.

The HHO method is hybrid as it introduces cell displacement unknowns and
faces displacement unknowns which live on the cell boundaries. In the
spirit of discontinouous Galerkin methods, displacement can be
discontinuous at the cell boundary. Cell unknowns are local and can be
condensated during the Newton iterations at the structural scale, hence
providing an attractive numerical cost.

Generic implementations of the HHO method have been developped in `C++`
([`Disk++` project](https://github.com/wareHHOuse/diskpp) project,
`CERMICS`) and in `Python` ([`H2O`
project](https://github.com/davidsiedel/h2o), CEA/MinesParisTech) to
solve non- linear mechanics problems. Both implementations can use
`MFront` generated behaviour through `MGIS`. The method has also been
introduced in industrial solvers [`code_aster`](https://code-aster.org/)
and [`Cast3M`](http://www-cast3m.cea.fr/).

In this presentation, we show the robustness of the HHO method to
volumetric locking, using non-linear behaviour laws in finite
transformations. Moreover, we show that using the HHO method polyhedral
support feature, crystalline plasticity laws can be expressed at the at
the element level, with a direct correspondence between a Voronoï cell
and an HHO polyhedral element in the mesh.

# The `MFEM-MGIS` project: coupling of `MFEM` and MGIS for High Performance non linear simulations

- Guillaume Latu
  - CEA Cadarache IRESNE/DES/DEC/SESC/LSC, 13 108 St Paul lez Durance, France.
- Thomas HELFER
  - CEA Cadarache IRESNE/DES/DEC/SESC/LSC, 13 108 St Paul lez Durance, France.

# References
