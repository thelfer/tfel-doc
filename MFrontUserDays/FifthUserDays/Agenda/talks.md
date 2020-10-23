---
title: Agenda of the fifth MFront User Meeting
author: Thomas Helfer
date: 25/11/2020
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

# Overview of `TFEL-3.4` and `MGIS-1.2`. T. Helfer, G. Marois. (13h30-14h00)

This talk will present the new features of Version 3.4 of the [`TFEL`
project](http://tfel.sourceforge.net).

## Version 3.4 of the `TFEL` project

This talks will present some new features and improvements introduced
during the development of Version 3.4 of the `TFEL/MFront` project.

![Noticeable applications of `MFront` and `MGIS` in 2020. a) Normalised
residual topography after an indentation test on a single crsytal of
copper with Méric-Cailletaud' finite stain behaviour [@meric_fe_1994]
using `Ansys` (courtesy of A. Bourceret, FEMTO) b) Slope failure
analysis with strength reduction in
[`OpenGeoSys`](https://www.opengeosys.org/) (courtesy of T. Deng and T.
Nagel, [@deng_slope_2020]) c) Integration of the `MGIS` integration in
`Europlexus` (courtesy of P. Bouda, CEA) d) Simulation of rolling using
the innovative CEA' proto-application `MEFISTO` (courtesy of O. Jamond,
CEA)](img/Highlights.pdf){#fig:tfel width=90%}

Figure @fig:tfel illustrates some noticeable applications of
`TFEL/MFront` not presented in any of the subsequent talks.

The major features of this version are:

- A better support of the implicit resolution of generalised behaviours,
  in particular regarding the computation of the consistent tangent
  operator [@helfer_assisted_2020]. Examples of this features will be
  presented in Talks @sec:bleyer and @sec:simo and in references
  [@bleyer_monolithic_2020;@bleyer_multiphase_2020;@bleyer_transient_2020;@helfer_using_2020].
- An extension of the [`StandardElastoViscoPlasticity`
  brick](http://tfel.sourceforge.net/StandardElastoViscoPlasticityBrick.html)
  to porous materials. This is discussed in Talk @sec:porous:plasticity.
- The ability to store `MFront` behaviours in a [`madnex`
  file](https://github.com/thelfer/madnex). `madnex` is a data model
  based on `HDF5` file format that was originally designed by EDF as
  part of their in-house projects for capitalising their experimental
  data and which is now being shared among the main actors of the french
  nuclear industry with the aim of becoming the de facto standard to
  exchange experimental data, `MFront` implementations and unit tests of
  those implementations. Documentation is available here:
  <http://tfel.sourceforge.net/madnex.html>.
- The `MFront`' `generic` interface now exports functions to rotate
  gradients in the material frame before the behaviour integration and
  rotate the thermodynamics forces and the tangent operator blocks in
  the global frame after the behaviour integration. Such functions are
  particularly useful for generalised behaviours.

A special effort has been set on the documentation with many new
tutorials
[@helfer_assisted_2020;@bleyer_monolithic_2020;@bleyer_multiphase_2020;@bleyer_small-strain_2020;@bleyer_transient_2020;@bleyer_stationnary_2020;@bleyer_stationnary_2020b;@bleyer_phase-field_2020;@;@helfer_using_2020].

In order to increase the community of developers, a first tutorial
showing how a new stress criteria can be added to the
[`StandardElastoViscoPlasticity`
brick](http://tfel.sourceforge.net/StandardElastoViscoPlasticityBrick.html)
has been published [@helfer_extending_2020]. Other similar tutorials are
being considered.

## Version 1.2 of the `MGIS` project

**`mgis.fenics`** The `mgis.fenics` `python` module, developed by J.
  Bleyer and discussed in depth in Talk @sec:bleyer, is the major
  development of this version.

**Orthotropoic behaviours** This version also provide a better support
  for orthotropic behaviours, following the developments made in
  `MFront`' `generic` interface. Prior to this version, orthotropy had
  to be handled by the calling solver, which implied:

- rotating the gradients in the material frame before the behaviour
  integration.
- rotation the thermodynamics forces and the tangent operator blocks in
  the global frame after the behaviour integration.

`MFront`' `generic` interface now exports appropriate functions which
are seamlessly integrated in `MGIS`.

**Integration in new solvers** `MGIS` has been tested in a growing
  number of solvers and numerical platforms:

- [`MoFEM`](http://mofem.eng.gla.ac.uk/mofem/html), see Talk @sec:mofem.
-
  [`NairnMPM`](http://osupdocs.forestry.oregonstate.edu/index.php/NairnMPM),
  see Talk @sec:mpm.
- [`esys.escript`](https://launchpad.net/escript-finley).
- [`Kratos Multiphysics`](https://www.cimne.com/kratos/). A dedicated
  application has been developed in the `mfront/application` branch with
  the help of Vicente Mataix Ferrándiz and Riccardo Rossi.
- [`DUNE`](https://dune-project.org/). Small and finite strain examples
  are available here: <https://github.com/thelfer/dune-mgis>

An [`MFEM`](https://mfem.org/) application is currently under heavy
developments here: <https://github.com/thelfer/mfem-mgis>.

# Implementing a shear-transformation-zone model in `MPM` and `SPFEM` using `MFront`. Ning Guo, Wenlong Li (14h00-14h30) {#sec:mpm}

In this study, we have implemented a shear-transformation-zone (STZ)
model in both the material point method (MPM) and the smoothed particle
finite element method (SPFEM) using [`MFront`](http://tfel.sourceforge.net), a code generator for the
efficient implementation of constitutive models, to study the
large-deformation problems of geomaterials. 

![Equivalent strain contour from the MPM simulations: the footing (left) and the column collapse (right)](img/STZ_MPM_MFront.pdf){#fig:mpm width=95%}

The applicability of the STZ model in simulating geomaterials and the
efficiency of the [`MFront`](http://tfel.sourceforge.net) interface are
demonstrated with two examples, namely, the footing and the granular
column collapse problems (See Figure @fig:mpm).

#  Binding `MFront` with `FEniCS` for automated formulation and resolution of generalized non-linear behaviours. J. Bleyer, T. Helfer (14h30-15h00) {#sec:bleyer}

In this talk, we will present a [`python`](https://fenicsproject.org/)
package called
[`mgis.fencis`](https://thelfer.github.io/mgis/web/mgis_fenics.html)
[@bleyer_overview_2020] which provides a binding between
[`MFront`](http://tfel.sourceforge.net) [@helfer_introducing_2015] and
[`FEniCS`](https://fenicsproject.org/)
[@logg_automated_2012;@alnaes_fenics_2015], a finite-element library
dedicated to the automated formulation and resolution of PDEs.

Using this package, we alleviate the difficulties of implementing
complex material constitutive laws in
[`FEniCS`](https://fenicsproject.org/) by relying on the
[`MGIS`](https://github.com/thelfer/MFrontGenericInterfaceSupport)
project [@helfer_mfrontgenericinterfacesupport_2020]. The development of
this package enabled to test [`MFront`](http://tfel.sourceforge.net)
recent extension to generalized behaviours.

Typical applications involve for instance fully coupled non-linear
thermoelasticity or phase-field models of fracture.

![An example of finite strain plastic results with the `mgis.fencis` module](img/finite_strain_plasticity_solution.png){#fig:Talk1 width=50%}

A set of tutorials provide an overview of the features of the module and
its usage:

* [Stationnary non-linear heat
  transfer](https://thelfer.github.io/mgis/web/mgis_fenics_nonlinear_heat_transfer.html)
  [@bleyer_stationnary_2020].
* [Stationnary non-linear heat transfer: 3D problem and performance
  comparisons](https://thelfer.github.io/mgis/web/mgis_fenics_nonlinear_heat_transfer_3D.html)
  [@bleyer_stationnary_2020b].
* [Transient heat equation with phase
  change](https://thelfer.github.io/mgis/web/mgis_fenics_heat_equation_phase_change.html)
  [@bleyer_transient_2020].
* [Monolithic transient
  thermoelasticity](https://thelfer.github.io/mgis/web/mgis_fenics_monolithic_transient_thermoelasticity.html)
  [@bleyer_monolithic_2020].
* [Small-strain von Mises
  elastoplasticity](https://thelfer.github.io/mgis/web/mgis_fenics_small_strain_elastoplasticity.html)
  [@bleyer_small-strain_2020].
* [Finite-strain elastoplasticity within the logarithmic strain
  framework](https://thelfer.github.io/mgis/web/mgis_fenics_finite_strain_elastoplasticity.html)
  [@bleyer_finite-strain_2020]. See also Figure @fig:Talk1.
* [Multiphase model for fiber-reinforced
  materials](https://thelfer.github.io/mgis/web/mgis_fenics_multiphase_model.html)
  [@bleyer_multiphase_2020].
* [Phase-field approach to brittle
  fracture](https://thelfer.github.io/mgis/web/mgis_fenics_phase_field.html)
  [@bleyer_phase-field_2020].

We will also mention the current limitations which are planned to be
resolved when adapting the package to the next-generation `FEniCS-x`
project.

# One application of the `MGIS` project to the a variational problem resolution modelling a damaged elasto-plastic material. V. Alves Fernandes, G. Bacquaert, J. Bleyer, D. Kondo, C. Maurini, S. Raude, F. Voldoire (15h00-15h30)

The analysis of soil materials leads to the need to formulate highly
nonlinear constitutive laws. In this presentation, the `mgis.fenics`
project is efficiently used to implement a regularized model coupling
elasto-plasticity and damage, initially proposed by J.-J. Marigo and K.
Kazymyrenko in [@marigo_micromechanical_2019].

The numerical strategy to solve the mechanical problem is based on the
interaction between the [`FEniCS`](https://fenicsproject.org/) and
[`MFront`](http://tfel.sourceforge.net) environments. On the one hand,
benefiting from the local integration of
[`MFront`](http://tfel.sourceforge.net), the elasto-plastic problem is
treated. In another time, the damage problem is solved in
[`FEniCS`](https://fenicsproject.org/) with the help of the TAO
optimization software library.

![From a simulated compression under axisymmetric conditions, the final volumetric plastic field (at U/H=12%) and the evolution of every stored and dissipated energy part supplied by the external mechanical work.](img/Talk2.pdf){#fig:talk2 width=75%}

Solving these two successive sub-problems ensures the resolution of a
well-posed variational problem, by providing a physically acceptable
solution (respecting equilibrium equations, plasticity and damage
criteria). Typical numerical simulations to geomaterials are illustrated
and commented on. See Figure @fig:talk2 for a first example.

The need to complexify the initial model to account for a richer
phenomenology (cyclic and hydromechanical behavior mainly) is finally
briefly discussed.

# Extension of the `StandardElastoViscoPlasticity` brick to porous materials. M. Shokeir, J. Hure, T. Helfer (16h00-16h30) {#sec:porous:plasticity}

This talk presents an extension of [`MFront`'
`StandardElastoViscoPlasticity`
brick](http://tfel.sourceforge.net/StandardElastoViscoPlasticityBrick.html)
to constitutive equations developed for porous materials.

Porous plasticity models describe the nucleation, growth and coalescence
of voids, accounting for the porosity as an additional state variable.
These models are used to perform numerical simulations of ductile
fracture of metals. 

From a numerical point of view, the implementation of these constitutive
equations based on implicit schemes is known to be challenging for
various reasons, such as strong evolutions of state variables, singular
yield surfaces when the porosity reaches a critical value, and / or
thresholds for evolutions laws. An innovative fully implicit numerical
algorithm has been set-up based on an accelerated fixed-point method to
handle these challenges.

Gurson-Tvergaard-Needleman and Rousselier yield criteria have been
implemented and validated, along with several nucleations laws. Complex
constitutive equations of porous visco-plasticity can now be implemented
in a few lines in a very intuitive and readable way, as follows:

~~~~{.cxx}
@Brick StandardElastoViscoPlasticity{
  stress_potential : "Hooke" {young_modulus : 200e3, poisson_ratio : 0.3},
  inelastic_flow : "Plastic" {
    criterion : "GursonTvergaardNeedleman1982" {
      f_c : 0.01, f_r : 0.10, q_1 : 2, q_2 : 1, q_3 : 4
    },
    isotropic_hardening : "Linear" {R0 : 200.}
  },
  porosity_evolution : {
    nucleation_model : "Chu-Needleman 1980 (stress)" {
      fn : 0.1, sigm : 20, sn : 10, fmax : 0.1
    }
  }
};
~~~~

Additional constitutive equations for porous materials can also be added
by the user, as described in [@helfer_extending_2020]. The
Gurson-Tvergaard-Needleman model implemented in the brick has been used
to perform the numerical simulations of ductile tearing using the
finite-element solver [`Cast3M`](http://www-cast3m.cea.fr/). 

![a) `Cast3M` simulation of a Notched Tensile sample of an AA6061-T6
found in the core of Jules Horowitz Reactor and comparison of simulation
result with experimental data. b) `Cast3M` simulation of a Charpy test
on the PWR reactor core vessel'
steel](img/PorousPlasticity.pdf){#fig:porous_plasticity width=95%}

The first example is the modelling of the 6061-T6 aluminum alloy which
is the material for the vessel-rack assembly of the Jules Horowitz
Reactor (JHR). This alloy, as many others, undergoes damage by void
nucleation and extremely rapid coalescence. This makes it difficult for
researchers to model such a behaviour. However, the innovative
accelerated fixed-point method allows the user to model such harsh
behaviours. As depicted in Figure @fig:porous_plasticity a), simulations
are performed on axisymmetric notched samples and compared to
experimental results in order to calibrate the parameters of the model.

The second example deals with the low alloy steel used for Reactor
Pressure Vessel (RPV) of Pressurized Water Reactor (PWR), where impact
tests are simulated, as illustrated in Figure @fig:porous_plasticity b),
and compared to numerical results.

# MoFEM: An open source, parallel finite element library - MFront integration. Karol Lewandowski, Lukasz Kaczmarczyk (16h30-17h00) {#sec:mofem}

[`MoFEM`](http://mofem.eng.gla.ac.uk/mofem/html) (Mesh-oriented Finite
Element Method) is a C++ library for managing complexities related to
the finite element method (FEM) [@kaczmarczyk_mofem_2020].

[`MoFEM`](http://mofem.eng.gla.ac.uk/mofem/html) is developed to provide a
finite element library incorporating modern approximation approaches and
data structures for engineers, students and academics.

[`MoFEM`](http://mofem.eng.gla.ac.uk/mofem/html) is specifically designed
to solve complex engineering problems, enabling seamless integration of
meshes that comprise multiple element types and element shapes, which
are typically encountered in industrial applications.

In the first part of this talk, we will present a brief introduction to
[`MoFEM`](http://mofem.eng.gla.ac.uk/mofem/html) library, focusing
attention on the [`MoFEM`](http://mofem.eng.gla.ac.uk/mofem/html) software
design, and examples applications.

![Example necking of a rod with two
[`Mfront`](http://tfel.sourceforge.net) behaviours. a) Discretised one
eighth of the geometry. b) Parallel domain decomposition. c) Deformed
specimen and distribution of deformation
gradient.](img/mofem.pdf){#fig:mofem width=75%}

In the second part of the presentation, a
[`MoFEM`](http://mofem.eng.gla.ac.uk/mofem/html) user module integrating
[`MFront`](http://tfel.sourceforge.net) behaviours library will be
demonstrated. Using off-the-shelf
[`MoFEM`](http://mofem.eng.gla.ac.uk/mofem/html) operators and bilinear
form integrators results in clean and concise implementation, allowing
for usage of multiple material models on different parts of the finite
element mesh. The performance of the module will be shown with numerical
examples. See Figure @fig:mofem for a first example.

# Implementation of constitutive models for clay rocks and bentonite-based materials using MFront for safety and performance assessments of HLW-repositories in clayey formations. Eric Simo, P. Herold, M. Mánica, T. Helfer, D. Masin, T. Nagel (17h00-17h30) {#sec:simo}

Argillaceous formations are currently being considered in several
countries as a possible host medium for deep geological disposal of
high- and intermediate-level and long-lived nuclear waste
[@delage_clays_2010]. They exhibit desirable features such as low
hydraulic conductivity, low molecular diffusion, and significant
radionuclide retention capacity [@armand_fundamental_2017].
Particularly, in Germany, rock salt, clay rock as well as crystalline
rock are being considered for the final disposal of high-level waste in
accordance with the site selection act [@StandAG2017]. In step one of
the first phase of the site selection procedure, the German Waste
Management Organisation, Bundesgesellschaft für Endlagerung mbH (BGE),
identified and proposed nine clay formations (so-called sub-areas) as
potential repository sites based on a defined set of of geoscientific
criteria and requirements [@bge_2020].

In clay formations, bentonite materials are considered as the main
sealing component of the engineered barrier system. This is particularly
due to their swelling capacity upon hydration, which helps to close
remaining gaps and voids at the sealing location, and thus ensure
confinement of the disposed radioactive waste.

In repository conditions, clay rocks and bentonite materials will be
subjected to coupled thermo-hydro-mechanical loads, which can
potentially compromise their sealing capacity if certain tolerances are
exceeded. The understanding of the complex thermo-hydro-mechanical
behaviour of clay materials is therefore necessary for the safety
assessment of repository systems. Constitutive models able to reproduce
such material behaviour are needed for the numerical based safety and
performance assessments of repository systems.

The development of material models for clay rocks and bentonite-based
materials has been addressed in the past two decades by several
researchers; see for instance
[@manica_malcom_analysis_2018;@pardoen_hydro-mechanical_2015;@ly_contribution_2018@]
for clay rocks and
[@masin_hypoplastic_2012;@masin_d_coupled_2017;@darde_experimental_2019;@dieudonne_hydromechanical_2016;@alonso_modelling_1999;@restrepo_hydro-mechanical_2020]
for bentonite constitutive models. For this purpose, BGE funded the
research project PIONIER which takes advantage of multiple experimental
investigations on clays and bentonite materials in the work packages GAS
and HITEC of the European Joint Programme on Radioactive Waste
Management – EURAD, to deal with such constitutive models. The
approaches proposed by [@manica_malcom_analysis_2018] for clay rocks and
[@masin_hypoplastic_2012;@masin_d_coupled_2017] for bentonite have been
selected by the authors for further development and implementation in
the numerical code [`OpenGeoSys`](https://www.opengeosys.org/)
[@kolditz_opengeosys_2012;@Bilke2019]. The implementation will be
carried out in MFront, a code generation tool for constitutive modelling
[@helfer_introducing_2015;@helfer_mfrontgenericinterfacesupport_2020].
The complexity of the considered material behaviour requires addressing
several numerical challenges. For instance, a new concept has been
developed to use [`MFront`](http://tfel.sourceforge.net) as a wrapper to
integrate an existing implementation of a constitutive equation in a
solver supported by
[`MFront`](http://tfel.sourceforge.net)[@helfer_using_2020]. The concept
has been successfully validated for the hypoplastic model for bentonite
by [@masin_hypoplastic_2012;@masin_d_coupled_2017]. For clay rocks,
theoretical works are being carried out to incorporate a nonlocal
plasticity approach within the
[`MFront`](http://tfel.sourceforge.net)-[`OpenGeoSys`](https://www.opengeosys.org/)
framework. The latter is necessary for the implementation of the
nonlocal plasticity model proposed by [@manica_malcom_analysis_2018].

The present contribution intends to give an overview to the
implementation strategies for the two selected models within the
[`MFront`](http://tfel.sourceforge.net) framework and their application
in the scope of the project PIONIER.

# About the speakers

Thomas Helfer is an engineer at CEA in the Fuel Simulation Laboratory at
Cadarache, France and the main developer of `TFEL/MFront` and `MGIS`.
<https://www.researchgate.net/profile/Thomas_Helfer>.

Goustan Bacquaert is a PhD student working at EDF Lab Paris-Saclay,
supervised by the École des Ponts PariTech and the Sorbonne Université.
His work focuses on the modelling of soil behaviours, both theoretically
and numerically, for structural engineering studies.

Jérémy Hure is a researcher at CEA in the Department of Materials for
Nuclear Applications, working on fracture mechanics of irradiated
materials. <http://jeremy.hure.free.fr>

Mohamed Shokeir is a PhD student at CEA & École des Mines Paristech on
the influence of radiation damage on the fracture toughness of aluminum
alloys - computational methods on the AA6061-T6.
<https://www.linkedin.com/in/mohamed-shokeir-897b2614a>

Ning Guo is an Assistant Professor at Zhejiang University in the
Department of Civil Engineering. He is working on computational
geomechanics and multiscale modeling of geomaterials.

Wenlong Li is a PhD student in civil engineering department at Zhejiang
University.His research focus is modeling granular materials,especially
sand.

Karol Lewandowski is a research associate at University of Glasgow, a
key contributor to the finite element library `MoFEM`. His current
research interest lies in plasticity and contact mechanics for
incremental cold flow forming simulations.

# References