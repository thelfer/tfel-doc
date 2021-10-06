---
title: A step-by-step implementation of the plastic-viscoplastic behaviour of Aimery Assire
author: Thomas Helfer
date: 21/11/2021
lang: en-EN
toc: true
numbersections: true
documentnumber:
documentversion: 0
abstract: |
  This report describes how to implement the plastic-viscoplastic behaviour of Aimery Assire in `MFront`.
monofont: DejaVuSansMono.ttf 
geometry:
- paper=a4paper
- margin=2.5cm
link-citations: true
colorlinks: true
bibliography: bibliography.bib
csl: iso690-numeric-en.csl
figPrefixTemplate: "$$i$$"
tblPrefixTemplate: "$$i$$"
secPrefixTemplate: "$$i$$"
lstPrefixTemplate: "$$i$$"
eqnPrefixTemplate: "($$i$$)"
header-includes:
- \usepackage{tcolorbox}
- \newtcolorbox{myquote}{colback=gray!5!white, colframe=gray!75!black}
- \renewenvironment{quote}{\begin{myquote}}{\end{myquote}}
---

\newcommand{\paren}[1]{\left(#1\right)}
\newcommand{\norm}[1]{\lVert #1 \rVert}
\newcommand{\tenseur}[1]{\underline{#1}}
\newcommand{\tenseurq}[1]{\underline{\underline{\mathbf{#1}}}}
\newcommand{\tepsilonto}{\tenseur{\varepsilon}^{\mathrm{to}}}
\newcommand{\tdepsilonto}{\tenseur{\dot{\varepsilon}}^{\mathrm{to}}}
\newcommand{\tepsilonel}{\tenseur{\varepsilon}^{\mathrm{el}}}
\newcommand{\tdepsilonel}{\tenseur{\dot{\varepsilon}}^{\mathrm{el}}}
\newcommand{\tepsilonvp}{\tenseur{\varepsilon}^{\mathrm{vp}}}
\newcommand{\tdepsilonvp}{\tenseur{\dot{\varepsilon}}^{\mathrm{vp}}}
\newcommand{\tepsilonp}{\tenseur{\varepsilon}^{\mathrm{p}}}
\newcommand{\tdepsilonp}{\tenseur{\dot{\varepsilon}}^{\mathrm{p}}}
\newcommand{\epsilontozz}{\varepsilon^{\mathrm{to}}_{zz}}
\newcommand{\tsigma}{\underline{\sigma}}
\newcommand{\trace}[1]{{\mathrm{tr}\paren{#1}}}
\newcommand{\sigmaH}{\sigma_{H}}
\newcommand{\Frac}[2]{{{\displaystyle \frac{\displaystyle #1}{\displaystyle #2}}}}
\newcommand{\derivtot}[2]{{\displaystyle \frac{\displaystyle \mathrm{d} #1}{\displaystyle \mathrm{d} #2}}}
\newcommand{\deriv}[2]{{\displaystyle \frac{\displaystyle \partial #1}{\displaystyle \partial #2}}}
\newcommand{\sderiv}[2]{{\displaystyle \frac{\displaystyle \partial^{2} #1}{\displaystyle \partial^{2} #2}}}
\newcommand{\sigmaeq}{\sigma_{\mathrm{eq}}}
\newcommand{\bts}[1]{{\left.#1\right|_{t}}}
\newcommand{\mts}[1]{{\left.#1\right|_{t+\theta\,\Delta\,t}}}
\newcommand{\ets}[1]{{\left.#1\right|_{t+\Delta\,t}}}
\newcommand{\nvp}{\tenseur{n}_{\mathrm{vp}}}
\newcommand{\avpa}{\tenseur{\alpha}_{\mathrm{vp}}^{(1)}}
\newcommand{\avpb}{\tenseur{\alpha}_{\mathrm{vp}}^{(2)}}
\newcommand{\davpa}{\tenseur{\dot{\alpha}}_{\mathrm{vp}}^{(1)}}
\newcommand{\davpb}{\tenseur{\dot{\alpha}}_{\mathrm{vp}}^{(2)}}
\newcommand{\Xvpa}{\tenseur{X}_{\mathrm{vp}}^{(1)}}
\newcommand{\Xvpb}{\tenseur{X}_{\mathrm{vp}}^{(2)}}
\newcommand{\Cvpa}{C_{\mathrm{vp}}^{(1)}}
\newcommand{\Cvpb}{C_{\mathrm{vp}}^{(2)}}
\newcommand{\Dvpa}{D_{\mathrm{vp}}^{(1)}}
\newcommand{\Dvpb}{D_{\mathrm{vp}}^{(2)}}
\newcommand{\pvp}{p_{\mathrm{vp}}}
\newcommand{\Evp}{E_{\mathrm{vp}}}
\newcommand{\Kvp}{K_{\mathrm{vp}}}
\newcommand{\dpvp}{\dot{p}_{\mathrm{vp}}}
\newcommand{\AEvp}{A_{E_{\mathrm{vp}}}}
\newcommand{\BEvp}{B_{E_{\mathrm{vp}}}}
\newcommand{\CEvp}{C_{E_{\mathrm{vp}}}}
\newcommand{\AKvp}{A_{K_{\mathrm{vp}}}}
\newcommand{\BKvp}{B_{K_{\mathrm{vp}}}}

\newcommand{\np}{\tenseur{n}_{\mathrm{p}}}
\newcommand{\apa}{\tenseur{\alpha}_{\mathrm{p}}^{(1)}}
\newcommand{\apb}{\tenseur{\alpha}_{\mathrm{p}}^{(2)}}
\newcommand{\dapa}{\tenseur{\dot{\alpha}}_{\mathrm{p}}^{(1)}}
\newcommand{\dapb}{\tenseur{\dot{\alpha}}_{\mathrm{p}}^{(2)}}
\newcommand{\Xpa}{\tenseur{X}_{\mathrm{p}}^{(1)}}
\newcommand{\Xpb}{\tenseur{X}_{\mathrm{p}}^{(2)}}
\newcommand{\Cpa}{C_{\mathrm{p}}^{(1)}}
\newcommand{\Cpb}{C_{\mathrm{p}}^{(2)}}
\newcommand{\Dpa}{D_{\mathrm{p}}^{(1)}}
\newcommand{\Dpb}{D_{\mathrm{p}}^{(2)}}
\newcommand{\pp}{p_{\mathrm{p}}}
\newcommand{\dpp}{\dot{p}_{\mathrm{p}}}

<!--
pandoc -f markdown+tex_math_single_backslash --pdf-engine=xelatex -F pandoc-crossref --citeproc --highlight-style=tango --default-image-extension=pdf Implementation.md -o Implementation.pdf
-->

# Introduction {.unnumbered}

This tutorial shows how to implement the behaviour proposed by Aimery
Assire in his PhD thesis [@assire_amorcage_2000] using an implicit
scheme.

In this tutorial, plasticity and viscoplasticity will be considered as
uncoupled.

# Some introductory material

This document is meant to help new users to find their ways in the
existing documentation of `MFront` and summarize the most important
points.

> **Material properties and models**
> 
> As most users are focused on mechanical behaviours, this document
> will not discuss
> [*material properties*](http://tfel.sourceforge.net/material-properties.html)
> and
> [*models*](http://tfel.sourceforge.net/models.html).

`MFront` has been designed to help the user writing complex
mechanical behaviours using a domain specific language that are:

- numerically efficient.
- portable from one finite element to another.
- easy to read (by your colleagues) and maintain.

Before writing your own behaviour, a few advices are helpful:

- You must have some knowledge of this behaviour and of its physical
  meaning. See
  [@doghri_mechanics_2000;@besson_non-linear_2009;@forest_mecanique_2013]
  for a good introduction. **If you are new to finite strain single
  crystal plasticity, do not try to directly study the examples found in
  the `MFront` tests base**. It highly recommended to have some basic
  understanding of the classical algorithms used to integrate
  constitutive equations (see
  [@besson_numerical_2004;@lemaitre_engineering_2005;@besson_non-linear_2009]).
- You must develop your behaviour *step by step*. Do not neglect unit
  testing. This is what [`MTest`](.md) has been made for.
- You must have some knowledge about your finite element solver, in
  particular when using "advanced" features (finite strain, non local
  models).

The final advice is the more important: as every open-source project,
`MFront` has its community of users. **Do not hesitate to ask
questions**:

- [on the forum](https://github.com/thelfer/tfel/discussions).
- [on the mailing lists](https://sourceforge.net/p/tfel/tfel/).
- [to the authors](mailto:tfel-contact@cea.fr).

## Understand how your mechanical solver works and the role of the mechanical behaviour in the resolution of the mechanical equilibrium

For the sake of simplicity, we consider in this paragraph a behaviour
written in the realm of infinitesimal transformations.

### Main role of mechanical behaviour


![Main role of the mechanical behaviour](img/MechanicalBehaviour){#fig:mfront:assire:mechanical_behaviour width=75%}

Given a strain increment \(\Delta\,\tepsilonto\) over a time step
\(\Delta\,t\), a mechanical behaviour must compute (see Figure
@fig:mfront:assire:mechanical_behaviour):

- The value of the stress \(\ets{\tsigma}\) at the end of the time step.
- The value of internal state variables \(\ets{\vec{Y}}\) at the end of
  the time step. Those internal state variables describe the
  microstructural evolution of the material.
- The consistent tangent operator, for many solvers (see below), which
  is defined as the derivative of the stress at the end of the time step
  with respect to the total strain increment
  \(\deriv{\ets{\tsigma}}{\Delta\,\tepsilonto}\).
- For specific cases, the mechanical behaviour shall also provide:
  - The speed of sound
  - A prediction operator (`code_aster`)
  - The elastic operator (`Abaqus-Explicit`, `Europlexus`)
  - The estimation of the stored and dissipated energies (`Abaqus-Explicit`)

#### Implicit element solvers

#### Explicit finite element solvers

#### Fast-Fourier Transform solvers

[@moulinec_numerical_1998]

Lippman-Schwinger equation

#### Other solvers

### Advanced features

- Provide a estimation of the next time step for time step automatic adaptation
- Check bounds:
  - Physical bounds
  - Standard bounds
- Clear error messages
- Parameters
    - It is all about AQ!
    - Parametric studies, identification, etc…
- Generate `MTest` files on integration failures
- Generated example of usage:
  - Generation of MODELISER/MATERIAU instructions (Cast3M)
  - Input file for Abaqus, Ansys
- Provide information for dynamic resolution of inputs (MGIS/MTest/Aster/Europlexus):
  - Numbers Types (scalar, tensors, symmetric tensors)
  - Entry names /Glossary names…

## Understand how `MFront` works

![Illustration of `MFront` code generation: from an unique
implementation file, `MFront` generates `C++` code which is specific to
the targeted
solver.](img/MFrontPrinciple){#fig:mfront:assire:cdoe_generation
width=75%}

`MFront` translates a set of input files into `C++` sources and compiles
them as depicted in Figure @fig:mfront:assire:cdoe_generation. A basic
knowledge of the `C` and `C++` syntax is then advisable.

`MFront` first introduces the notion of `Domain Specific Language`
(`DSL`), which gives a context about what the input file describes,
mainly the type of the material knowledge treated (material property,
model, finite strain behaviour, strain based behaviour, cohesive zone
moel) and/or the algorithm used to integrate the state variables'
evolution. 

The list of available DSLs can be retrieved as follows:

~~~~{.bash}
$  mfront --list-dsl
available dsl: 
- DefaultCZMDSL                 : this parser is the most generic one as
it does not make any restriction on the behaviour or the integration method
that may be used.
- DefaultDSL                    : this parser is the most generic one as 
it does not make any restriction on the behaviour or the integration method
that may be used.
....
~~~~

> **The `Implicit` DSL**
> 
> For most mechanical behaviours, the `Implicit` DSL is generally
> recommended for moderate to complex behaviours: the behaviour integration
> is performed using a generalised mid-point rule
> [@besson_numerical_2004;@lemaitre_engineering_2005;@besson_non-linear_2009]
> and allows the computation of the 
> consistent tangent operator [@simo_consistent_1985], which is required
> by most finite element solvers to have decent numerical performances.
>
> The `Implicit` DSL is documented on
> [this page](http://tfel.sourceforge.net/implicit-dsl.html)

Each `DSL` has its own conventions, keywords and automatically declared
variables. `MFront` specific keywords begins with the `@` letter.

Once the appropriate `DSL` choosen, the list of available keywords can
be retrieved from the command line:

~~~~{.bash}
$ mfront --help-keywords-list=Implicit
~~~~

where `Implicit` is the name of the `DSL`.

The help of for a specific keyword can be retrieved as follows:

~~~~{.bash}
$ mfront --help-keywords-list=Implicit:@Author
~~~~

where `Implicit` is the name of the `DSL` and `@Author` the name of the
keyword.

A good way to get familiar with a specific DSL is to look in the
[`MFront` gallery](http://sourceforge.net/gallery.html) if some examples
describe its usage.

Otherwise, one may want to look at the tests distributed with `MFront`
in the `mfront/tests/behaviours` directory (online access on github
[here](https://github.com/thelfer/tfel/tree/master/mfront/tests/behaviours)).

> **About the tests cases**
> 
> Beware that the tests may not reflect the best way to
> implement a specific behaviour (`MFront` evolves a lot and each
> version introduces new features): they can be kept outdated to ensure
> backward compatibility.

> **Detailed documentation**
> 
> This [page](http://tfel.sourceforge.net/documentations.html) references most of the available
> documentations.
> 
> For example, the complete description of the `DSL`s available can
> be found [here](http://tfel.sourceforge.net/documents/mfront/mfront.pdf) and
> [here](http://tfel.sourceforge.net/documents/mfront/behaviours.pdf), in French.
> 
> However, such detailed documentation is not required for most users.
> We even consider that beginning with `MFront` from these documents
> can be overwhelmingly difficult. Reading the detailed example of the
> gallery is much easier: `Longum iter est per praecepta, breve et
> efficax per exempla` (It's a long way by the rules, but short and
> efficient with examples).
> 
> For French users, a detailed tutorial can be found
> [here](http://tfel.sourceforge.net/documents/tutoriel/tutoriel.pdf)

## Before writing your first behaviour

### Look if your behaviour can be implemented using the `StandardElastoViscoPlasticity` brick

The [`StandardElastoViscoPlasticity`
brick](http://tfel.sourceforge.net/StandardElastoViscoPlasticityBrick.html) can be used to implement
a large class of strain based elasto-visco-plastic behaviours
implementations.

It is worth to look if your behaviour falls in that category, or a least
if part of your behaviour falls in that category:

- In the first case, implementing the behaviour using the
  `StandardElastoViscoPlasticity` brick can be straightforward. Even if
  you do not want to use the `StandardElastoViscoPlasticity` brick, it
  is worth to have a reference implementation to which one can compare
  its own implementation in terms of performance, robustness, etc.
- In the second case, implementing a complex behaviour is only tractable
  step by step. So it is worth to implement first the part which can be
  implemented using the `StandardElastoViscoPlasticity` brick and
  validate this part.

### Have a look at the `MFront` gallery

The [`MFront` gallery](http://tfel.sourceforge.net/gallery.html) is meant to document how to
describe in depth and step by step the implementation of some
behaviours. If one of the described behaviour is close to the one you
plan to implement, it is highly recommend to carefully read the
associated page and start by modifying the one described in the gallery.

### Write your constitutive equations as a system of ordinary differential equations using tensorial notations

Constitutive equations can be expressed as a system of ordinary
differential equations which allows to determine the evolution of the
state variables of the material. As the state variables are tensorial
objects, those differential equations can be written using tensorial
operations: this allows to implement the integration of the behaviour
for all supported modelling hypothesis. Do not write tensorial
operations using indices and loops.

### Select your integration algorithm

`MFront` DSLs provides built-in support for various integration algorithms:

- DSLs of the `Default` family let the user write its own integration
  algorithm from scratch.
- DSLs of the `Runge-Kutta` family is based on explicit Runge-Kutta
  algorithms to integrate the constitutive equations. Thoses algorithms
  are seldom used as they do not provide a simple way to compute the
  consistent tangent operator.
- DSLs of the `Implicit` family perform the behaviour integration using
  a generalised mid-point rule
  [@besson_numerical_2004;@lemaitre_engineering_2005;@besson_non-linear_2009].

### Write your discretized equations by hand using tensorial notations

Apart from DSLs of the `Runge-Kutta` family which allow a direct
transcription of the system of ordinary differential equations driving
the state variable evolution, the other algorithms requires to
discretize the constitutive equations.

### Look at the documentation of the tensor operations

Second and fourth orders tensors are the basic mathematical tools used
to describe mechanical behaviours. `MFront` is based on the `TFEL/Math`
library which provides many standards operations and functions to handle
them. See [here](http://tfel.sourceforge.net/tensors.html) for an introduction.

# Constitutive equations of the plastic-viscoplastic behaviour of Aimery Assire

## Additive split of the total strain

The total strain \(\tepsilonto\) is assumed to be the sum of an elastic
strain \(\tepsilonel\), a plastic strain \(\tepsilonp\) and a
viscoplastic strain \(\tepsilonvp\) as follows:

\[ \tepsilonto = \tepsilonel+\tepsilonp+\tepsilonvp \]{#eq:mfront:assire:split_strain}

##  Hooke law

The elasticity is assumed linear and isotropic, i.e. given by the
Hooke law:

\[
\tsigma=\lambda\,\trace{\tepsilonel}\,\tenseur{I}+2\,\mu\,\tepsilonel 
\]{#eq:mfront:assire:hooke_law}

where \(\lambda\) and \(\mu\) are the first and second Lamé parameters.


## Viscoplastic part

The viscoplastic part of the the strain follows a modified associated
Norton behaviour where the exponent and the coefficient depend on the
equivalent viscoplastic strain \(\pvp\).

Both isotropic hardening rule and kinematic hardening are taken into
account:

- Isotropic hardening is described using a Voce law. The size of the
  elastic domain \(R_{v}\) is thus given by the following function of
  the equivalent viscoplastic strain:
  \[
  R_{v}\paren{\pvp} = R_{v_{0}}+\paren{R_{v_{\infty}}-R_{v_{0}}}\,\paren{1-\exp\paren{-b_{v}\,\pvp}}
  \]
- Kinematic hardening is described using two back-stresses denoted
  respectively \(\Xvpa\) and \(\Xvpb\).

The stress criterion is given by the von Mises equivalent stress
\(\sigmaeq\) of the effective stress \(\tsigma-\Xvpa-\Xvpb\):

\[
\sigmaeq = J_{2}\paren{\tsigma-\Xvpa-\Xvpb}
\]

> **The von Mises stress and its derivatives**
>
> Let \(\tenseur{v}\) be an arbitrary symmetric second order tensor.
>
> \(J_{2}\paren{\tenseur{v}}\) can be written as:
> 
> \[
> J_{2}\paren{\tenseur{v}}=\sqrt{\tenseur{v}\,\colon\,\tenseurq{M}\,\colon\,\tenseur{v}}
> \]
>
> where \(\tenseurq{M}\) is the projector on the deviatoric space
> multiplied by \(\Frac{3}{2}\), i.e.:
>
> \[
> \tenseurq{M}=\Frac{3}{2}\paren{\tenseurq{I}-\Frac{1}{3}\tenseur{I}\,\otimes\tenseur{I}}
> \]
>
> The first derivative of the \(J_{2}\paren{\tenseur{v}}\) with respect
> to \(\tenseur{v}\) is normal to the isovalue of
> \(J_{2}\paren{\tenseur{v}}\). Its value is given by:
>
> \[
> \deriv{J_{2}}{\tenseur{v}} =
> \Frac{1}{J_{2}\paren{\tenseur{v}}}\tenseurq{M}\,\colon\,\tenseur{v} = 
> \Frac{3}{2\,J_{2}\paren{\tenseur{v}}}\tenseur{\tilde{v}}
> \]
>
> where \(\tenseur{\tilde{v}}\) is the deviatoric part of \(\tenseur{v}\).
> 
> In the following, the second derivative of
> \(J_{2}\paren{\tenseur{v}}\) with respect to \(\tenseur{v}\)
> will be useful. It can readily be computed as follows:
>
> \[
> \sderiv{J_{2}}{\tenseur{v}} = 
> \Frac{1}{J_{2}\paren{\tenseur{v}}}\,
> \paren{\tenseurq{M}-\deriv{J_{2}}{\tenseur{v}}\,\otimes\,\deriv{J_{2}}{\tenseur{v}}}
> \]{#eq:mfront:assire:d2J2_ds2}

The yield function \(f_{\mathrm{vp}}\) is thus given by:

\[
f_{\mathrm{vp}} = \sigmaeq-R_{v}\paren{\pvp}
\]

As the behaviour is associated, the flow direction \(\nvp\) is given by:

\[
\nvp = \deriv{\sigmaeq}{\tsigma} = \Frac{3}{2\,\sigmaeq}\,\tenseur{s}
\]

where \(\tenseur{s}\) is the deviatoric part of the effective stress
\(\tsigma-\Xvpa-\Xvpb\).

### Evolution of the equivalent viscoplastic strain

The rate of viscoplastic deformation is finally given by:

\[
\tdepsilonvp = \dpvp\,\nvp
\]{#eq:mfront:assire:devp}

where the viscoplastic equivalent strain rate is given by:

\[
\dpvp=\paren{\Frac{f_{v}}{\Kvp\paren{\pvp}}}^{\Evp\paren{\pvp}}=\paren{\Frac{\sigmaeq-R_{v}\paren{\pvp}}{\Kvp\paren{\pvp}}}^{\Evp\paren{\pvp}}
\]{#eq:mfront:assire:dpvp}

with:

- \(\Evp\paren{\pvp}=\AEvp\,\pvp^{\CEvp} + \BEvp\)
- \(\Kvp\paren{\pvp}=\AKvp\,\pvp + \BKvp\)

### Evolution of the back strains

Let \(\avpa\) and \(\avpb\) be the back-strains associated respectively
with the back-stresses \(\Xvpa\) and \(\Xvpb\) as follows:

\[
\left\{
\begin{aligned}
 \Xvpa &= \Frac{2}{3}\,\Cvpa\,\avpa \\
 \Xvpb &= \Frac{2}{3}\,\Cvpb\,\avpb \\
\end{aligned}
\right.
\]

The evolution of the first back-strain follows a modified
Armstrong-Frederik law as follows [@armstrong_mathematical_1966]:

\[
\davpa = \dpvp\,\nvp-\Dvpa\, \dpvp\, \avpa - \paren{\Frac{\left|\Xvpa\right|}{M}}^{m}\,\Frac{\Xvpa}{\left|\Xvpa\right|}
\]{#eq:mfront:assire:davpa}

The evolution of the second back-strain follows the classical
Armstrong-Frederik law as follows:

\[
\davpb = \dpvp\,\nvp-\Dvpb\, \dpvp\, \avpb
\]{#eq:mfront:assire:davpb}

## Plastic part

The plastic part of the strain takes into account both isotropic
hardening and kinematic hardening:

- Isotropic hardening is described using as the sum of two Voce laws. The size of the
  elastic domain \(R_{p}\) is thus given by the following function of
  the equivalent plastic strain:
  \[
  R_{p}\paren{\pp} = R_{p_{0}}+\paren{R_{p_{\infty}}^{(1)}-R_{p_{0}}}\,\paren{1-\exp\paren{-b_{p}^{(1)}\,\pp}}+\paren{R_{p_{\infty}}^{(2)}-R_{p_{0}}}\,\paren{1-\exp\paren{-b_{p}^{(2)}\,\pp}}
  \]
- Kinematic hardening is described using two back-stresses denoted
  respectively \(\Xpa\) and \(\Xpb\).

The stress criterion is given by the von Mises equivalent stress
\(\sigmaeq\) of the effective stress \(\tsigma-\Xpa-\Xpb\):

\[
\sigmaeq = J_{2}\paren{\tsigma-\Xpa-\Xpb}
\]

The yield function \(f_{\mathrm{p}}\) is thus given by:

\[
f_{\mathrm{p}}\paren{\tsigma,\Xpa,\Xpb, \pp} = \sigmaeq-R_{p}\paren{\pp}
\]

As the behaviour is associated, the flow direction \(\np\) is given by:

\[
\np = \deriv{\sigmaeq}{\tsigma} = \Frac{3}{2\,\sigmaeq}\,\tenseur{s}
\]

where \(\tenseur{s}\) is the deviatoric part of the effective stress
\(\tsigma-\Xpa-\Xpb\).

Finally, the plastic strain rate is given by:

\[
\tdepsilonp = \dpp \,\np
\]{#eq:mfront:assire:dep}

### Evolution of the equivalent plastic strain

The evolution of the equivalent plastic strain follows the classic
Karush-Kuhn-Tucker (KKT) conditions:

\[
\left\{
\begin{aligned}
\dpp \,f_{\mathrm{p}} &= 0\\
\dpp  &\geq 0\\
f_{\mathrm{p}}  &\leq 0\\
\end{aligned}
\right.
\]{#eq:mfront:assire:dpp}

### Evolution of the back strains

Let \(\apa\) and \(\apb\) be the back-strains associated respectively
with the back-stresses \(\Xpa\) and \(\Xpb\) as follows:

\[
\left\{
\begin{aligned}
 \Xpa &= \Frac{2}{3}\,\Cpa\,\apa \\
 \Xpb &= \Frac{2}{3}\,\Cpb\,\phi_{c}\paren{\pp}\,\apb \quad\text{with}\quad \phi_{c}\paren{\pp} = \phi^{(c)}_{p_{0}}+\paren{\phi_{p_{\infty}}^{(c)}-\phi^{(c)}_{p_{0}}}\,\paren{1-\exp\paren{-\omega_{p}^{(c)}\,\pp}}\\
\end{aligned}
\right.
\]

The evolution of the first back-strain follows the classical
Armstrong-Frederik law as follows:

\[
\dapa = \dpp\,\np-\Dpa\, \dpp\, \apa
\]{#eq:mfront:assire:dapa}

The evolution of the second back-strain follows a modified
Armstrong-Frederik law as follows:

\[
\dapb = \dpp\,\np-\Dpb\,\phi_{d}\paren{\pp}\, \dpp\, \apb\quad\text{with}\quad \phi_{d}\paren{\pp} = \phi^{(d)}_{p_{0}}+\paren{\phi_{p_{\infty}}^{(d)}-\phi^{(d)}_{p_{0}}}\,\paren{1-\exp\paren{-\omega_{p}^{(d)}\,\pp}}\\
\]{#eq:mfront:assire:dapb}

## Evolution of the elastic strain

The evolution of the elastic strain is simply obtained by the time
derivative of the split of the strains (Equation
@eq:mfront:assire:split_strain) as follows:

\[ \tdepsilonel = \tdepsilonto - \tdepsilonp - \tdepsilonvp = \tdepsilonto - \dpvp\,\nvp - \dpp\,\np\]{#eq:mfront:assire:deel}

where Equations @eq:mfront:assire:devp and @eq:mfront:assire:dep were
used.

# Implicit scheme

## Choice of the state variables

In this tutorial, the following set of state variables is selected:

\[
\vec{Y} =
\begin{pmatrix}
\tepsilonel\\ \pvp \\ \avpa\\ \avpb\\ \pp \\ \apa\\ \apb
\end{pmatrix}
\]{#eq:mfront:assire:Y}

## Behaviour integration

With a little work, Equations @eq:mfront:assire:deel,
@eq:mfront:assire:dpvp @eq:mfront:assire:davpa, @eq:mfront:assire:davpb,
@eq:mfront:assire:dpp @eq:mfront:assire:dapa and @eq:mfront:assire:dapb
can be used to express the evolution of the state variables as a system
of ordinary differential equations:

\[
\vec{\dot{Y}}=G\paren{\vec{Y}}
\]{#eq:mfront:assire:dY}

> **Treatment of the plastic part**
> 
> In this paragraph, we will ignore that plasticity deserves a special
> treatment. Of course, you may use the consistency equation to derive
> a ordinary differential equation satisfied by the plastic multiplier,
> but this is in practice a very bad idea. As we will see later, it is
> much better to replace this differential equation by an implicit
> equation stating that the system must be on the yield surface at
> the end of the time step.

Many algorithms exist to solve this ordinary differential equations over
a finite time step \(\Delta\,t\).

## Implicit scheme. Definition of the residual {#sec:mfront:assire:implicit_scheme}

An implicit scheme turns this ordinary system of differential equations
into a system of non linear equations whose unknown is the increment
\(\Delta\,\vec{Y}\) of the state variables \(\vec{Y}\) over the time
step \(\Delta\,t\).

Let us introduce the following variables and notations:

- \(t\) denotes the time at the beginning of the time step.
- \(\theta\) denotes a numerical parameter between 0 and 1.
- \(\bts{\vec{Y}}\) denotes the value of the state variables at
  the beginning of the time step.
- \(\mts{\vec{Y}}\) denotes the value of the state variables at
  an intermediate time \(t+\theta\,\Delta\,t\):
  \[
  \ets{\vec{Y}}=\bts{\vec{Y}}+\theta\,\Delta\,\vec{Y}
  \]
- \(\ets{\vec{Y}}\) denotes the value of the state variables at
  the end of the time step:
  \[
  \ets{\vec{Y}}=\bts{\vec{Y}}+\Delta\,\vec{Y}
  \]

Using those notations, we can straightforwardly express the implicit
system derived from Equation @eq:mfront:assire:dY as follows:

\[
\Delta\,\vec{Y}-G\paren{\bts{\vec{Y}}+\theta\,\Delta\,\vec{Y}}\,\Delta\,t=\vec{0}
\]{#eq:mfront:assire:DeltaY}

Finally, the increment \(\Delta\,\vec{Y}\) of the state variables satisfies the
following implicit system of non linear equations:

\[
\vec{F}\paren{\Delta\,\vec{Y}}=\vec{0} 
\]{#eq:mfront:assire:implicit_system}

with \(\vec{F}\paren{\Delta\,\vec{Y}}=\Delta\,\vec{Y}-G\paren{\bts{\vec{Y}}+\theta\,\Delta\,\vec{Y}}\,\Delta\,t\).

\(\vec{F}\) is called the residual of the implicit system.

> **The `zeros` and `fzeros` variables. Views**
> 
> Vector \(\Delta\,\vec{Y}\) can be accessed through a variable called
> `zeros` and vector \(\vec{F}\) can be accessed through a variable
> called `fzeros`.
>
> Those variables are seldom used done in practice, expect for
> debugging.
>
> It is much easier to manipulate directly the increments of the state
> variables and to compute the blocks resulting from the
> decomposition of \(\vec{F}\), as detailled in the Section
> @sec:mfront:assire:block_decomposition.
>
> Internally, the increments of the state variables are views to blocks
> in the `zeros` variable and the blocks resulting from the
> decomposition of \(\vec{F}\) are view to the `fzeros` variables.
> More details about views are given in the [documentation of the
> `TFEL/Math` library](http://tfel.sourceforge.net/tfel-math.html`).

### Initialisation of the residual{#sec:mfront:assire:residual_initialisation}

Equation @eq:mfront:assire:DeltaY shows that the residual \(\vec{F}\)
can naturally be initialized to \(\Delta\,\vec{Y}\). This is done
automatically by `MFront` before each evaluation of the residual.

### Block decomposition of the residual{#sec:mfront:assire:block_decomposition}

Following Equation @eq:mfront:assire:Y, the residual can be decomposed as follows

\[
\vec{F} =
\begin{pmatrix}
f_{\tepsilonel}\\ f_{\pvp}\\ f_{\avpa}\\ f_{\avpb}\\ f_{\pp}\\ f_{\apa}\\ f_{\apb}
\end{pmatrix}
\]{#eq:mfront:assire:F}

Each block is associated with a state variable and can be deduced from
the ordinary differential equation associated with this state variable.

For example, the block associated with the elastic strain is associated
with the slip of the strain associated written in incremental form:

\[
f_{\tepsilonel} = \Delta\,\tepsilonel - \Delta\,\tepsilonto + \Delta\,\pvp\,\mts{\nvp} + \Delta\,\pp\,\mts{\np}
\]{#eq:mfront:assire:feel}

### Newton-Raphson algorithm and jacobian matrix

Equation @eq:mfront:assire:implicit_system is solved using an iterative
algorithm. In this tutorial, the standard Newton-Raphson algorithm will
be used.

Let \(\Delta\,\vec{Y}^{(n)}\) be the estimation at the
\(n^{\mathrm{th}}\) iteration of the Newton algorithm.
\(\Delta\,\vec{Y}^{(n+1)}\) is then given by:

\[
\Delta\,\vec{Y}^{(n+1)}=\Delta\,\vec{Y}^{(n)}+\delta\,\Delta\,\vec{Y}^{(n)}
\quad\text{with}\quad
\delta\,\Delta\,\vec{Y}^{(n)}=-J^{-1}\paren{\Delta\,\vec{Y}^{(n)}}\,\vec{F}\paren{\Delta\,\vec{Y}^{(n)}}
\]

where \(J\) is the jacobian matrix given by:

\[
J\paren{\Delta\,\vec{Y}^{(n)}} = \deriv{\vec{F}}{\Delta\,\vec{Y}}\paren{\Delta\,\vec{Y}^{(n)}}
\]

In `MFront`, the jacobian matrix can be computed analytically or
evaluated numerically using a centered finite difference scheme.

> **The `jacobian` variable**
>
> The jacobian matrix is stored internally in a matrix called
> `jacobian`. This variable is seldom used in pratice, except
> for debuggin.

### Initialisation of the jacobian{#sec:mfront:assire:jacobian_initialisation}

Equation @eq:mfront:assire:DeltaY shows that the jacobian \(J\) can
naturally be initialized to the identity matrix \(I_{d}\) as it can be
written:

\[
J = I_{d} + \Delta\,t\,\deriv{\vec{G}}{\vec{\Delta\,Y}}
\]

This is done automatically by `MFront` before each evaluation of the
residual.

> **Small time steps**
>
> It is interesting to note that the jacobian matrix naturally tends to
> the identity matrix \(I_{d}\) as the time step tends toward zero.
>
> A bad evaluation of the derivative \(\deriv{\vec{G}}{\vec{\Delta\,Y}}\)
> can thus be compensated by using (very) small time steps.
>
> *A contrario*, if an implicit behaviour implementation only converges
> for very small  time steps, it may indicate that the jacobian matrix
> is incorrect (or very approximate).

### Block decomposition of the jacobian

Using a block decomposition of the increment of the state variables
derived from Equation @eq:mfront:assire:F, and the Block Decomposition
@eq:mfront:assire:F, the jacobian matrix can also be decomposed by
blocks as follows:

\[
J = 
\begin{pmatrix}
  \deriv{f_{\tepsilonel}}{\Delta\,\tepsilonel} & \ldots & \ldots & \ldots & \deriv{f_{\tepsilonel}}{\Delta\,\apb} \\
  \vdots & \ddots & \cdots & \ddots & \vdots \\
  \vdots & \vdots & \deriv{f_{y_{i}}}{\Delta\,y_{j}} & \vdots & \vdots \\
  \vdots & \ddots & \cdots & \ddots & \vdots \\
  \deriv{f_{\apb}}{\Delta\,\tepsilonel} & \ldots & \ldots & \ldots & \deriv{f_{\apb}}{\Delta\,\apb} \\
\end{pmatrix}
\]

### Nonlinear solvers delivered with `MFront`

By default, the standard Newton-Raphson algorithm is used. Other
algorithms can be selected using the `@Algorithm` keyword.

~~~~{.bash}
$ mfront --help-keyword=Implicit:@Algorithm
The `@Algorithm` keyword is used to select a numerical algorithm.

The set of available algorithms depends on the domain specific
language. As the time of writting this notice, the following
algorithms are available:

- `euler`, `rk2`, `rk4`, `rk42` , `rk54` and `rkCastem` for the
  `Runge-Kutta` dsl.
- `NewtonRaphson`, `NewtonRaphson_NumericalJacobian`,
  `PowellDogLeg_NewtonRaphson`,
  `PowellDogLeg_NewtonRaphson_NumericalJacobian`, `Broyden`,
  `PowellDogLeg_Broyden`, `Broyden2`, `LevenbergMarquardt`,
  `LevenbergMarquardt_NumericalJacobian` for implicit dsls.
~~~~

Those nonlinear solvers are implemented in the `TFEL/Math` library. The
documentation of this library provides a [comprehensive description of
the structure of those
algorithms](http://tfel.sourceforge.net/tfel-math.html#sec:tfel_math:non_linear_solvers).

![Description of the main steps of the implicit scheme and the associated code blocks.](img/ImplicitDSL ""){#fig:mfront:assire:resolution_steps width=75%}

In practice, as illustrated on Figure
@fig:mfront:assire:resolution_steps, `MFront` allows the user to write
the most important steps of the non linear algorithm, which are:


- The computation of a prediction of the increment of the state
  variables in the `Predictor` code blocks. By default, thoses
  increments are initialized to zero.
- The update of the stress at \(t+\theta\,\Delta\,t\) in the
  `@ComputeStress` code block. As discussed in the next paragraph, this
  part can be generated automatically by `MFront` by the
  `StandardElasticity` brick (see Section
  @sec:mfront:assire:StandardElasticity). This brick also handles the
  computation of the stress at the end of the time step that is normally
  deduced implicitly from the code given in the `@ComputeStress` code
  block or explicitely given in the `@ComputeFinalStress` code block.
- The computation of the residual \(\vec{F}\) and jacobian matrix \(J\)
  in the `@Integrator` code block.
- The definition of additional convergence checks in the
  `@AdditionalConvergenceChecks` code block. This code block is very
  useful in multi-surface plasticity, or combining laws with thresholds.

After the resolution of the implicit system, the user may compute the
consistent tangent operator in the `@TangentOperator` code block. In
this document, this code block will be generated automatically by the
`StandardElasticity` brick (see Section
@sec:mfront:assire:StandardElasticity). This brick will also handle the
computation of the prediction operator that is normally introduced by
the `@PredictionOperator` keyword.

The other keywords appearing in Figure
@fig:mfront:assire:resolution_steps (such as `@InitLocalVariables`,
`@UpdateAuxiliaryStateVariables`) will be detailled later.

#### Default stopping criterion

By default, `MFront` will stop the iterations of the non linear solver
if the norm of the residual is below a given threshold:

\[
\norm{\vec{F}\paren{\Delta\,\vec{Y}^{(n)}}}<\varepsilon_{\mathrm{NR}}
\]

The default value for \(\varepsilon_{\mathrm{NR}}\) is \(10^{-8}\) which
can be loose. We strongly advice to change this default value to a more
strictier value using the `@Epsilon` keyword, as follows:

~~~~{.cxx}
@Epsilon 1.e-14;
~~~~

> **Examples from the `MFront` gallery**
>
> Many examples in the `MFront` gallery sets the default value of the
> stopping criterion at \(10^{-16}\) which is a very strigent value in
> practice.
>
> Such low value is however very important in unit testing and non
> regression tests.

The value of the \(\varepsilon_{\mathrm{NR}}\) can be changed at runtime
using the `epsilon` parameter.

Note that the convergence criterion can be changed or extended by
defining an `@AdditionalConvergenceChecks` code block.

#### Default value of implicit parameter

The default value of the implicit parameter \(\theta\) is \(0.5\). This
value has been recommended by various authors for viscoplastic behaviour
as it leads to a quadratic convergence in time.

In practice, a fully implicit resolution is generally more satisfying,
so we strongly recommend to change this default value using the `@Theta`
keyword as follows:

~~~~{.cxx}
@Theta 1;
~~~~

The value of the implicit parameter can be changed at runtime using the
`theta` parameter.

#### Maximum number of iterations

The algorithms must fail if they did not converge after a maximum number
of iterations.

The default value of the maximum number of iterations is \(100\). This
default value can be changed using the `@IterMax` keyword as follows:

~~~~{.cxx}
@IterMax 50;
~~~~

The value of the maximum number of iterations can be changed at runtime
using the `itermax` parameters.

#### Handling failures to the residual evaluation

The evaluation of the residual may fail for various reasons.

For example, an incorrect prediction of the stress may lead to infinite
viscoplastic rate.

In this case, as described in the [documentation of the `TFEL/Math`
library](http://tfel.sourceforge.net/tfel-math.html#sec:tfel_math:non_linear_solvers),
`MFront` will take the latest correction computed by the solver and
divide it by two. This leaves the search direction unchanged but
decrease its intensity by two. This can be though as a sort of basic
line-search.

The user may indicate a failure in the evaluation of the jacobian by
returning `false` in the `@Integrator` code block based on some physical
criteria:

- for a plastic behaviour, one may want to reject a correction leading
  to a stress estimate well above the yield surface.
- for a viscoplastic plastic behaviour, one may want to reject stress
  estimates leading to unphysical viscoplastic strain rate.

This can lead to very powerful algorithms.

## Some helpful features of `MFront`

### The `StandardElasticity` brick {#sec:mfront:assire:StandardElasticity}

The plastic-viscoplastic behaviour of Aimery Assire falls in the realms
of the so-called `StandardElasticity` brick which automatically: 

- Generates the code for the computation of the stress at
  \(t+\theta\,\Delta\,t\) (i.e. the code normally generated by the
  `@ComputeStress` code block) and the computation of the stress at
  \(t+\Delta\,t\) (i.e. the code normally generated by the
  `@ComputeFinalStress` code block).
- Generates the code for the computation of the prediction operator,
  i.e. the code that is normally generated by the `@PredictionOperator`
  code block.
- Generates the code for the computation of the consistent tangent
  operator, i.e. the code that is normally generated by the
  `@TangentOperator` code block.
- Generates the code for the support for plane stress and generalized
  plane stress modelling hypotheses.

This brick assumes that:

- The total strain increment \(\tepsilonto\) only appears in the
  implicit system inside the split of strain (see Equation
  @eq:mfront:assire:split_strain).
- The stress is computed by the Hooke law (see Equation
  @eq:mfront:assire:hooke_law).
- The elastic strain increment is stored at the beginning of \(\vec{Y}\)
  vector and the implicit equation \(f_{\tepsilonel}\) associated with
  the elastic strain, is stored at the beginning of the residual
  \(\vec{F}\).

The usage of the `StandardElasticity` brick is declared as follows:

~~~~{.cxx}
@Brick StandardElasticity;
~~~~

#### Automatic support of the plane stress and axisymmetrical generalised plane stress hypotheses {#sec:mfront:assire:StandardElasticity:plane_stress}

The axial strain \(\epsilontozz\) is defined as an additional state
variable and the associated equation in the implicit system is added to
enforce the plane stress condition.

\[
f_{\epsilontozz}=\Frac{1}{E}\ets{\sigma_{zz}}
\]{#eq:mfront:planestress:fetozz}

where \(E\) is the Young modulus.

\(\Delta\,\epsilontozz\) is added to the implicit equation
\(f_{\tepsilonel}\) (see Equation @eq:mfront:assire:feel) as follows:

\[
f_{\tepsilonel} -= \Delta\,\epsilontozz\,\vec{e}_{z}\,\otimes\,\vec{e}_{z}
\]

#### Automatic derivation of the consistent tangent operator {#sec:mfront:assire:StandardElasticity:tangent_operator}

The consistent tangent operator is by definition the derivative
\(\deriv{\ets{\tsigma}}{\Delta\,\tepsilonto}\) of the stress at the end
of the time step with respect to the total strain increment.

According to the Hooke law @eq:mfront:assire:hooke_law, this derivative
can be expressed as:

\[
\deriv{\ets{\tsigma}}{\Delta\,\tepsilonto}=\underbrace{\deriv{\ets{\tsigma}}{\ets{\tepsilonel}}}_{\ets{\tenseurq{D}}}\,\colon\,\underbrace{\deriv{\ets{\tepsilonel}}{\Delta\,\tepsilonel}}_{\tenseurq{I}}\,\colon\,\deriv{\Delta\,\tepsilonel}{\Delta\,\tepsilonto}=\ets{\tenseurq{D}}\,\colon\,\deriv{\Delta\,\tepsilonel}{\Delta\,\tepsilonto}
\]{#eq:mfront:assire:consistent_tangent_operator}

with \(\ets{\tenseurq{D}}=\ets{\lambda}\,\tenseur{I}\,\otimes\,\tenseur{I}+2\,\ets{\mu}\,\tenseurq{I}\).

##### Formal derivation of \(\deriv{\Delta\,\tepsilonel}{\Delta\,\tepsilonto}\)

The Implicit System @eq:mfront:assire:F can be differentiated with
respect to the total strain by virtue of the implicit function theorem:

\[
\derivtot{\vec{F}}{\Delta\,\tepsilonto}=
\deriv{\vec{F}}{\Delta\,\vec{Y}}\,\derivtot{\Delta\,\vec{Y}}{\Delta\,\tepsilonto}+
\deriv{\vec{F}}{\Delta\,\tepsilonto}=0
\]

The derivative \(\deriv{\vec{F}}{\Delta\,\vec{Y}}\) is the jacobian
\(J\) of the implicit system, so the previous equation can be rewritten as:

\[
\derivtot{\Delta\,\vec{Y}}{\Delta\,\tepsilonto}
=-J^{-1}\,\deriv{\vec{F}}{\Delta\,\tepsilonto}
\]

Under the assumptions of the `StandardElasticity` framework, the
derivative \(\deriv{\vec{F}}{\Delta\,\tepsilonto}\) has the form:

\[
\deriv{\vec{F}}{\Delta\,\tepsilonto}=
\begin{pmatrix}
  1& 0 & 0 & 0& 0 &0 \\
  0& 1& 0 & 0& 0 &0 \\
  0& 0 & 1 & 0& 0 &0 \\
  0& 0 & 0 & 1& 0 &0 \\
  0& 0 & 0 & 0& 1 &0 \\
  0& 0 & 0 & 0& 0 &1\\
  0& 0 & 0 & 0& 0 &0 \\
  \vdots & \vdots & \vdots & \vdots & \vdots & \vdots\\
  0& 0 & 0 & 0& 0 &0 \\
\end{pmatrix}
\]{#eq:mfront:assire:dF_ddeto}

Thanks to Equation @eq:mfront:assire:dF_ddeto, one sees
that the product \(-J^{-1}\,\deriv{\vec{F}}{\Delta\,\tepsilonto}\) only
contains the the \(6\) first columns of the \(J^{-1}\). This allows
identifying \(\derivtot{\Delta\,\tepsilonel}{\Delta\,\tepsilonto}\) with
the \(6\times 6\) upper-left submatrix of \(J^{-1}\). Let
\(J_{\tepsilonel}^{-1}\) this submatrix:

\[
  J_{\tepsilonel}^{-1}=\derivtot{\Delta\,\tepsilonel}{\Delta\,\tepsilonto}
\]

Thanks to Equation
@eq:mfront:assire:consistent_tangent_operator,
the consistent tangent operator finally reads:

\[
\derivtot{\tsigma}{\Delta\,\tepsilonto}=\ets{\tenseurq{D}}\,\cdot\,J_{\tepsilonel}^{-1}
\]{#eq:mfront:assire:consistent_tangent_operator2}

> **Extension to more complex behaviours**
>
> The derivation of the consistent tangent operator presented here can
> be extended to more complex situations, when the assumptions of the
> `StandardElasticity` framework don't hold. See the documentation of
> `MFront`' `Implicit` DSLs for details:
> <http://tfel.sourceforge.net/implicit-dsl.html>

### Numerical computation of the jacobian

Most nonlinear algorithms delivered with `MFront` can use a numerical
jacobian. For example, the `NewtonRaphson` algorithm with a numerical
jacobian can be used using the following line:

~~~~{.cxx}
@Algorithm NewtonRaphson_NumericalJacobian;
~~~~

The jacobian matrix is computed using a centered finite difference
scheme. This may have a significant impact on the numerical performances
of the implementation.

In the case of the behaviour of A. Assire, the number of state variables
is, i.e. the size of \(\vec{Y}\) vector is \(32\) in \(3D\) (\(5\)
symmetric tensors with \(6\) components in \(3D\) and \(2\) scalars).
This means that the residual will be evaluated \(65\) times if a
numerical jacobian is used:

- \(1\) for the computation of the residual.
- \(2\,\times\,32\) for the computation of the numerical approximation of
  the jacobian matrix.

The perturbation value used to compute the numerical jacobian also have
a strong numerical impact. By default, the perturbation value is chosen
a one tenth of the default stopping value of the convergence criterion.
We recommend to change this value using the
`@PerturbationValueForNumericalJacobianComputation` keyword as follows:

~~~~{.cxx}
@PerturbationValueForNumericalJacobianComputation 1.e-7;
~~~~

Values between \(10^{-8}\) and \(10^{-6}\) usually give satisfactory
results.

### Numerical computation of blocks of the jacobian

For algorithms based on the analytical definition of the jacobian,
`MFront` can evaluate only some blocks numerically using the
`@NumericallyComputedJacobianBlocks` as follows:

~~~~{.cxx}
@NumericallyComputedJacobianBlocks {dfpp_ddeel,dfeel_ddeel};
~~~~

All the other blocks shall be computed analytically.

### Numerical verification of the jacobian

The `@CompareToNumericalJacobian` keyword can be used to request a
comparison of the jacobian blocks computed analytically to a numerical
approximation.

If the norm of the difference between the analytical and numerical value
is beyond a given threshold, `MFront` displays on the terminal the value
of the analytical block, the values of the numerical block and the value
of their difference.

The default value of this threshold can be changed using the
`@JacobianComparisonCriterion` value.

### A step by step implementation of the jacobian

Combining the ability to compute numerically certain blocks of the
jacobian matrix and the comparison of the analytical jacobian matrix to
a numerical approximation allows to gradually implement an analytical
jacobian.

First, one may declare all the jacobian blocks to be computed
numerically. Then, one may start by the most obvious blocks and remove
them from the list of blocks computed numerically.

Then, one may consider a given block, try to implement it, remove it
from the list of the blocks computed numerically and see of the
analytical value matches the numerical approximation. If the analytical
value is correct, one may consider another code block. If not, one must
correct the implementation of this term.

# Implementation of the viscoplastic part

The implementation can be decomposed into various steps of growing
complexity:

- As a first step, one may consider a simple Norton behaviour without
  isotropic and kinematic hardenings with constant normalisation factor
  \(\Kvp\) and exponent \(\Evp\). This is done in Section
  @sec:mfront:assire:norton.
- As a second step, one may consider to add isotropic hardening.
- As a third step, one may consider to add kinematic hardenings.
- As a fourth step, one may implement the whole viscoplastic part by
  adding the dependency of the normalisation factor \(\Kvp\) and
  exponent \(\Evp\) to the equivalent viscoplastic strain \(\pvp\).

One shall always consider comparing the prediction of its implementation
to the results of a reference one. In many cases, the
`StandardElastoViscoplasticity` brick may provide such a reference
implementation.

## Simple Norton behaviour{#sec:mfront:assire:norton}

### Constitutive equations

It is always worth rewritting the constitutive equations, even in simple
case.

The state variables are the elastic strain \(\tepsilonel\) and the
equivalent viscoplastic strain \(\pvp\).

The constitutive equation driving the evolution of the elastic strain is
derived from the split of the strain:

\[
\tdepsilonel=\tdepsilonto-\dpvp\,\nvp
\]{#eq:mfront:assire:norton:deel}

The constitutive equation driving the evolution of the equivalent
viscoplastic strain is:

\[
\dpvp = \paren{\Frac{\sigma}{\Kvp}}^{\Evp}
\]{#eq:mfront:assire:norton:dpvp}

where the stress \(\tsigma\) is related to the elastic strain by the
Hooke Law @eq:mfront:assire:hooke_law.

### Implementation using the `StandardElastoViscoplasticity` brick

~~~~{#lst:mfront:assire:Norton:StandardElastoViscoplasticity .cxx caption="Implementation of the Norton behaviour using the StandardElastoViscoplasticity brick."}
@DSL Implicit;
@Behaviour ImplicitNorton2;
@Author Thomas Helfer;
@Date 04 / 10 / 2021;

@Algorithm NewtonRaphson;
@Epsilon 1.e-14;
@Theta 1;

@Brick StandardElastoViscoPlasticity{
  stress_potential : "Hooke" {young_modulus : 150e9, poisson_ratio : 0.3},
  inelastic_flow : "Norton" {
    criterion : "Mises",
    K : 100e6,  // Stress normalisation factor
    n : 4.5     // Norton exponent
  }
};
~~~~

As illustrated by Listing
@lst:mfront:assire:Norton:StandardElastoViscoplasticity, the
implementation of the Norton behaviour using the
`StandardElastoViscoplasticity` brick is straightforward. It can be
decomposed in three parts:

1. The meta-data which declare:
  - the type of DSL used,
  - the name of the behaviour,
  - the author of the implementation
  - and the date.
  Those meta-data may also contain a description using the
  `@Description` keyword.
2. The numerical parameters, including:
  - the choice of the non linear solver,
  - the default value of the criterion value,
  - the default value of the implicit parameter \(\theta\).
3. The call to the `StandardElastoViscoPlasticity` brick. The reader can
  refer to the [documentation of the
  brick](http://tfel.sourceforge.net/StandardElastoViscoPlasticityBrick.html)
  for an in-depth explantations.

A few remark can be made at this stage:

- The Newton-Raphson solver being the default, its declaration is
  optional.
- The declaration of the elastic properties in the brick is optional.
  They can also be declared using the `@ElasticMaterialProperties`
  keyword. If they are not declared, two material properties with the
  external names `YoungModulus` and `PoissonRatio` are automatically
  declared because the material is isotropic (the default).

  Since the elastic properties are defined using values, two parameters
  named with the external names `YoungModulus` and `PoissonRatio` are
  automatically declared. Rather than using a value, a formula or an
  external `MFront` file could have been used.
- No state variable is explicitly declared. The `Implicit` DSL
  automatically declares the elastic strain as the first state variable.
  The name of this variable inside `MFront` is eel and its external name
  is `ElasticStrain`. The `StandardElastoViscoPlasticityBrick`
  automatically declares the equivalent plastic strain with the
  `EquivalentViscoplasticStrain` external name.
- The stress normalisation factor `K` and value of the Norton exponent
  `n` are declared using numerical values. This automatically declares
  two parameters named respectively `K` and `E` (`n` is used internally
  to define the normal). Implicitly, the Norton coefficient `A` is also
  defined to \(1\). Rather than a value, a formula or an external
  `MFront` file could also have been used.
- The consistent tangent operator is computed automatically using the
  technique described in Section
  @sec:mfront:assire:StandardElasticity:tangent_operator.

> **External names**
>
> External names are an important concept in `MFront`.
>
> The external name of a variable is the name of this variable from the
> calling solver.
>
> The user may associate an external name to a variable in two ways:
>
> - using the `setGlossaryName` method. In this case, the name passed to
>   the `setGlossaryName` method must be declared in the [`TFEL`'
>   glossary](http://tfel.sourceforge.net/glossary.html)
> - using the `setEntryName` method. In this case, any name can be used.

> **An example of the declaration of the Norton coefficient as a function of the temperature**
>
> The following listing uses formulae to define the viscoplastic
> properties using previously declared parameters:
> 
> ~~~~{.cxx}
> //! activation temperature for the Norton behaviour
> @Parameter temperature Ta = 273.15;
> Ta.setEntryName("NortonActivationTemperature");
> //! activation temperature for the Norton behaviour
> @Parameter temperature A0 = 2e-36;
> A0.setEntryName("NortonCoeffcient");
> 
> @Brick StandardElastoViscoPlasticity{
>   stress_potential : "Hooke" {young_modulus : 150e9, poisson_ratio : 0.3},
>   inelastic_flow : "Norton" {
>     criterion : "Mises",
>     A : "A0 * exp(-Ta / T)", //
>     n : 4.5,                 //
>     K : 1
>   }
> };
> ~~~~
>
> Note the `doxygen` like comments above the declaration of the
> parameters `Ta` and `A0` that can be used by the `mfront-doc`
> and `mfront-query` tools (See Appendix @sec:mfront:assire:useful_tools).

#### Compilation

The compilation of the `MFront` file in a shared library can be done as
follows:

~~~~{.bash}
$ mfront --obuild --interface=aster ImplicitNorton2.mfront
Treating target : all
The following library has been built :
- libAsterBehaviour.so :  asterimplicitnorton2
~~~~

#### First test using the `MTest`

`MTest` allows to study the behaviour on a single material point by
imposing for a given component either the strain history or the stress
history. 

~~~~{#lst:mfront:assire:Norton:StandardElastoViscoplasticity:mtest .cxx caption="Uniaxial tensile test on the simple Norton behaviour implemented using the StandardElastoViscoplasticity brick."}
@Author Thomas Helfer;
@Date   17/09/2021;

@ModellingHypothesis "Tridimensional";
@Behaviour<aster> "src/libAsterBehaviour.so" "asterimplicitnorton2";
@ExternalStateVariable "Temperature" 293.15;
@ImposedStrain "EXX" {0 : 0, 1 : 1e-2};
@Times{0, 1 in 10};
~~~~

#### Adding support for plane stress hypotheses

By default, plane stress hypotheses, i.e. the standard plane stress
hypothesis and the axisymmetrical generalized plane stress hypothesis
used by `Cyrano` fuel performance code, are not supported.

Support for those hypotheses is be added automatically by the
`StandardElastoViscoPlasticity` brick following the technique described
in Section @sec:mfront:assire:StandardElasticity:plane_stress if we
explicitly declare that those modelling hypotheses shall be supported.
The most straightforward way to do this is to use the following
declaration (generally at the beginning of the `MFront` implementation,
by convention):

~~~~{.cxx}
@ModellingHypotheses {".+"};
~~~~

#### Integration in `code_aster` with the `mtest` python module

<http://tfel.sourceforge.net/mtest-python.html>

~~~~{.python}
>>> b = mtest.Behaviour('src/libAsterBehaviours.so', 'asterimplicitnorton', 'Tridimensional')
>>> print(b.getInternalStateVariablesNames())
['ElasticStrain']
>>> b.expandInternalStateVariablesNames()
['ElasticStrainXX', 'ElasticStrainYY', 'ElasticStrainZZ', 'ElasticStrainXY', 'ElasticStrainXZ',
 'ElasticStrainYZ']
~~~~

### Implementation with the `StandardElasticity` brick

As the whole plastic-viscoplastic behaviour of Aimery Assire can't be
implemented using the `StandardElastoViscoPlasticity`, one shall
explicitly implement the computation of the residual and the computation
of the jacobian matrix. By chance, this behaviour respects the
assumpations of the `StandardElasticity` brick, described in Section
@sec:mfront:assire:StandardElasticity), which considerably eases its
implementation.

We now show how to use this brick to implement the simple Norton
behaviour. We first start using a numerical jacobian and then implement
the computation of this jacobian.

#### Variables automatically declared by the `Implicit` DSL

At this stage, it is worth detailling the variables automatically
declared by the `Implicit` DSL:

- As every DSL defining a strain based behaviour, the total strain
  \(\tepsilonto\) and its increment \(\Delta\,\tepsilonto\) are
  automatically declared as the `eto` and `deto` variables respectively.
- As every DSL defining a strain based behaviour, the variable `sig` is
  also automatically declared.
  - This variable is initialized with the value of the stress at the
    beginning of the time step \(\bts{\tsigma}\). This value can be used
    in the `@InitLocalVariables` and `@Predictor` code blocks.
  - After the behaviour integration, this variable must contain the
    value of the stress at the end of the time step \(\ets{\tsigma}\).
    This is generally done in the `@ComputeFinalStress` code block.
  - During the Newton algorithm, this variable is generally used to
    store the current estimate of the stress at the middle of the time
    step \(\mts{\tsigma}\). This estimate is generally computed in the
    `ComputeStress` code block.
- As in every DSL, the temperature is defined as an external state
  variable with the external name `Temperature`:
  - The variable `T` contains the value of the temperature at the
    beginning of the time step.
  - The variable `dT` contains the value of the increment of the
    temperature over the time step.
- The convergence threshold \(\varepsilon_{\mathrm{NR}}\) is associated
  with the `epsilon` parameter. The default value of the parameter can
  be changed using the `@Epsilon` keyword.
- The implicit parameter \(\theta\) is associated with the `theta`
  parameter. The default value of the parameter can be changed using the
  `@Theta` keyword.
- As in every DSL, the tangent operator is associated with a variable
  named `Dt`. This variable is only accessible in the `@TangentOperator`
  and `@PredictionOperator` code blocks.
- As in every DSL, the variable `D` is reserved for the stiffness
  matrix. This variable is only defined if one of the keywords
  `@ComputeStiffnessTensor` or the `@RequireStiffnessTensor` is used.

> **Variables in `MFront`**
>
> A variable in `MFront` is characterised by:
>
> - its category (i.e. its role). The main categories of variables are:
>   - state variable (see `@StateVariable` keyword),
>   - auxiliary state variable (see `@AuxiliaryStateVariable` keyword),
>   - local variable (see `@LocalVariable` keyword),
>   - parameter (see `@Parameter` keyword),
>   - integration variable (see `@IntegrationVariable` keyword),
>   - material property (see `@MaterialProperty` keyword).
> - its type.
> - its name inside `MFront`.
> - its symbolic name. This is used for [unicode
>   support](http://tfel.sourceforge.net/unicode.html).
> - its external name.

#### Implementation using a numerical jacobian

Following Section @sec:mfront:assire:implicit_scheme, the residual of
the implicit system to be solved can be obtained by discretizing
Equations @eq:mfront:assire:norton:deel and
@eq:mfront:assire:norton:dpvp.

This residual is readily given by:

\[
\left\{
\begin{aligned}
f_{\tepsilonel} &= \Delta\,\tepsilonel-\Delta\,\tepsilonto+\Delta\,\pvp\,\mts{\nvp} \\
f_{p} &= \Delta\,p-\Delta\,t\,\paren{\Frac{\mts{\sigma}}{\Kvp}}^{\Evp}
\end{aligned}
\right.
\]

~~~~{#lst:mfront:assire:Norton:StandardElasticity:NumericalJacobian .cxx caption="Implementation of the simple Norton behaviour implemented using the StandardElasticity brick with a numerical jacoabian."}
@DSL Implicit;
@Behaviour ImplicitNorton_NumericalJacobian;
@Author Thomas Helfer;
@Date 17 / 09 / 2021;

@Algorithm NewtonRaphson_NumericalJacobian;
@PerturbationValueForNumericalJacobianComputation 1.e-8;
@Epsilon 1.e-14;
@Theta 1;

@Brick StandardElasticity{young_modulus : 150e9, poisson_ratio : 0.3};

@StateVariable strain pvp;
pvp.setGlossaryName("EquivalentViscoplasticStrain");

@Parameter stress Kv = 100e6;
Kv.setEntryName("NortonNormalisationFactor");
@Parameter strainrate de0 = 1;
de0.setEntryName("NortonReferenceStrainRate");
@Parameter real Evp = 4.5;
Evp.setEntryName("NortonExponent");

@Integrator {
  constexpr auto eeps = 1.e-14;
  const auto seps = young * eeps;
  const auto seq = sigmaeq(sig);
  const auto iseq = 1 / (max(seq, seps));
  const auto n = 3 * deviator(sig) * (iseq / 2);
  const auto vp = de0 * pow(seq / Kv, Evp);
  // implicit equation associated with elasticity
  feel += dpvp * n;
  // implicit equation associated with the equivalent plastic strain
  fpvp -= dt * vp;
}
~~~~

The implementation of the residual must be done in the `@Integrator`
code block. The whole implementation of the behaviour is reported on
Listing @lst:mfront:assire:Norton:StandardElasticity:NumericalJacobian.

A few remarks can be made:

- As for the use of the `StandardElastoViscoplasticity` brick, the
  elastic properties can be defined by the options `young_modulus` and
  `poisson_ratio` options passed to the `StandardElasticity` brick.
- The normalisation factor \(\Kvp\) and exponent \(\Evp\) are
  automatically declared as parameters named respectively `Kvp` and
  `Evp`. The `setEntryName` method is used to associate the external
  names `NortonNormalisationFactor` and `NortonExponent` to those
  parameters.
- The `StandardElasticity` automatically computes the current estimation
  of stress \(\mts{\tsigma}\) in the variable `sig` before each
  evalution of the residual in the `@Integrator` code block. If the
  `StandardElasticity` brick is not used, the user may use the
  `@ComputeStress` code block to perform this computation.
- After convergence of the local Newton algorithm, the
  `StandardElasticity` automatically computes the stress
  \(\ets{\tsigma}\) at the end of the step. If the `StandardElasticity`
  brick is not used, the user may use the `@ComputeFinalStress` code
  block to perform this computation, although this is seldom required
  for strain based behaviour as the code in `@ComputeFinalStress` is
  generally automatically deduced from the code defined by
  `@ComputeStress`.
- The computation of `feel` takes into account the fact that this
  variable has been initialized to
  \(\Delta\,\tepsilonel-\Delta\,\tepsilonto\) by the
  `StandardElasticity` brick.
- The computation of `fpvp` takes into account the fact that this variable
  has been initialized to \(\Delta\,\pvp\) by the `Implicit` DSL as
  explained in Section @sec:mfront:assire:residual_initialisation.

> **Material properties vs parameters**
>
> In most MFront tutorials, we usually prefer to use parameters, which
> are declared using the `@Parameter` keyword.
>
> Contrary to material properties, declared using the
> `@MaterialProperty` keyword, parameters have default values.
> Parameters are thus useful to build behaviours specific to one
> particular material. Such behaviours are self-contained, which
> considerably eases building a strict material knowledge management
> policy.
>
> Parameters are also stored in a global structure. This means that
> parameters are uniform. In some codes, material properties may
> be defined on a per integration point basis.

#### Using quantities

A quantity in the `TFEL/Math` library are a mathematical object
associated with an unit type. Quantities are meant to allow dimensional
analysis and prevent illegal operations.

Quantities are enable by the `@UseQt` as follows:

~~~~{.cxx}
@UseQt true;
~~~~

#### Elimination of the implicit equation associated with the equivalent plastic strain

The increment \(\Delta\,\pvp\) can easily be eliminated from the
implicit system since there is no isotropic hardening. However, its
value is generally required for post-processings.

Auxiliary state variables are saved from one time step to the other but
are not part of the implicit system: there is no implicit equations
associated with those variables and their increments are not declared.

Auxiliary state variables are generally updated after the convergence of
the implicit scheme in the `@UpdateAuxiliaryStateVariables` code block.
At this stage of the behaviour integration (i.e. in the
`@UpdateAuxiliaryStateVariables` code block), the stress have been
updated to their values at the end of the time step and the state
variables have been updated. Note that the gradients (the total strain
for strain based behaviours) and the external state variables are never
updated.

Listing @lst:mfront:assire:Norton:StandardElasticity:NumericalJacobian2
shows how to modify Implementation
@lst:mfront:assire:Norton:StandardElasticity:NumericalJacobian to
eliminate the implicit equation associated with the equivalent
viscoplastic strain and updating it in the
`@UpdateAuxiliaryStateVariables` code block.

~~~~{#lst:mfront:assire:Norton:StandardElasticity:NumericalJacobian2 .cxx caption="Implementation of the simple Norton behaviour implemented using the StandardElasticity brick with a numerical jacoabian with the equivalent viscoplastic strain as an auxiliary state variable."}
@DSL Implicit;
@Behaviour ImplicitNorton_NumericalJacobian2;
@Author Thomas Helfer;
@Date 17 / 09 / 2021;
@UseQt true;

@Algorithm NewtonRaphson_NumericalJacobian;
@PerturbationValueForNumericalJacobianComputation 1.e-8;
@Epsilon 1.e-14;
@Theta 1;

@Brick StandardElasticity{young_modulus : 150e9, poisson_ratio : 0.3};

@AuxiliaryStateVariable strain pvp;
pvp.setGlossaryName("EquivalentViscoplasticStrain");

@Parameter stress Kv = 100e6;
Kv.setEntryName("NortonNormalisationFactor");
@Parameter strainrate de0 = 1;
de0.setEntryName("NortonReferenceStrainRate");
@Parameter real Evp = 4.5;
Evp.setEntryName("NortonExponent");

@LocalVariable strainrate vp;

@Integrator {
  constexpr auto eeps = 1.e-14;
  const auto seps = young * eeps;
  const auto seq = sigmaeq(sig);
  const auto iseq = 1 / (max(seq, seps));
  const auto n = 3 * deviator(sig) * (iseq / 2);
  vp = de0 * pow(seq / Kv, Evp);
  // implicit equation associated with elasticity
  feel += dt * vp * n;
}

@UpdateAuxiliaryStateVariables{
  pvp += dt * vp;
}
~~~~

#### Implementation using a analytical jacobian

The jacobian matrix only contains the
\(\deriv{f_{\tepsilonel}}{\Delta\,\tepsilonel}\) derivative which can be
computed as follows:

\[
\deriv{f_{\tepsilonel}}{\Delta\,\tepsilonel} =
\tenseurq{I}-\Delta\,t\,\mts{\tenseur{n}}\,\otimes\,\deriv{v_{\pvp}}{\Delta\,\tepsilonel}-\Delta\,t\,v_{\pvp}\,\deriv{\mts{\nvp}}{\Delta\,\tepsilonel}
\]
with \(v_{\pvp}=\paren{\Frac{\mts{\sigma}}{\Kvp}}^{\Evp}\).

\(\deriv{v_{\pvp}}{\Delta\,\tepsilonel}\) can be computed by the chain rule as follows:

\[
\deriv{v_{\pvp}}{\Delta\,\tepsilonel} =
\underbrace{\deriv{v_{\pvp}}{\mts{\sigmaeq}}}_{\Frac{\Evp}{\Kvp}\,\paren{\Frac{\mts{\sigma}}{\Kvp}}^{\Evp-1}}\,\colon\,
\underbrace{\deriv{\mts{\sigmaeq}}{\mts{\tsigma}}}_{\mts{\nvp}}\,\colon\,
\underbrace{\deriv{\mts{\tsigma}}{\mts{\tepsilonel}}}_{\mts{\lambda}\,\tenseur{I}\,\otimes\,\tenseur{I}+2\,\mts{\mu}\,\tenseurq{I}}\,\colon\,
\underbrace{\deriv{\mts{\tepsilonel}}{\Delta\,\tepsilonel}}_{\theta\,\tenseurq{I}}
\]

Finally, using the fact that \(\mts{\nvp}\) is a deviatoric tensor to
eliminate the term proportional to \(\mts{\lambda}\),
\(\deriv{v_{\pvp}}{\Delta\,\tepsilonel}\) is given by:

\[
\deriv{v_{\pvp}}{\Delta\,\tepsilonel} =
2\,\mts{\mu}\,\theta\,\left[\Frac{\Evp}{\Kvp}\,\paren{\Frac{\mts{\sigma}}{\Kvp}}^{\Evp-1}\right]\,\mts{\nvp}
\]

\(\deriv{\mts{\nvp}}{\Delta\,\tepsilonel}\) can also be computed by
chain rule in a similar way:

\[
\deriv{\mts{\nvp}}{\Delta\,\tepsilonel}=
\underbrace{\deriv{\mts{\nvp}}{\mts{\tsigma}}}_{\Frac{1}{\mts{\sigmaeq}}\paren{\tenseurq{M}-\mts{\nvp}\,\otimes\,\mts{\nvp}}}\,\colon\,
\underbrace{\deriv{\mts{\tsigma}}{\mts{\tepsilonel}}}_{\mts{\lambda}\,\tenseur{I}\,\otimes\,\tenseur{I}+2\,\mts{\mu}\,\tenseurq{I}}\,\colon\,
\underbrace{\deriv{\mts{\tepsilonel}}{\Delta\,\tepsilonel}}_{\theta\,\tenseurq{I}}
\]

where we used Equation @eq:mfront:assire:d2J2_ds2.

Finally,

\[
\deriv{\mts{\nvp}}{\Delta\,\tepsilonel}=
\Frac{2\,\mts{\mu}\,\theta}{\mts{\sigmaeq}}\paren{\tenseurq{M}-\mts{\nvp}\,\otimes\,\mts{\nvp}}
\]

The implementation of the Norton behaviour with an analytical jacobian
is reported in Listing
@lst:mfront:assire:Norton:StandardElasticity:AnalyticalJacobian. If we
compare Listing
@lst:mfront:assire:Norton:StandardElasticity:AnalyticalJacobian and
Listing @lst:mfront:assire:Norton:StandardElasticity:NumericalJacobian2,
we may notice that:

- The name of the algorithm was changed from
  `NewtonRaphson_NumericalJacobian` to `NewtonRaphson`.
- The definition of the default value for the perturbation value used
  for the computation of the numerical jacobian was removed.
- The computation of the jacobian has been added. As discussed in
  Section @sec:mfront:assire:jacobian_initialisation, we took into
  account the fact that `dfeel_ddeel` has automatically been initialized
  to the identity before the call to the `@Integrator` code block.`

> **Priority of operator `^`**
>
> In the `TFEL/Math` library, the tensorial product is associated to the
> `^` operator. In `C++`, this operator has a low priority contrary to
> its mathematical counterpart. Use of parenthesis is strongly adviced.

~~~~{#lst:mfront:assire:Norton:StandardElasticity:AnalyticalJacobian .cxx caption="Implementation of the simple Norton behaviour implemented using the StandardElasticity brick with a numerical jacoabian."}
@DSL Implicit;
@Behaviour ImplicitNorton;
@Author Thomas Helfer;
@Date 17 / 09 / 2021;
@UseQt true;

@Algorithm NewtonRaphson;
@Epsilon 1.e-14;
@Theta 1;

@Brick StandardElasticity{young_modulus : 150e9, poisson_ratio : 0.3};

@AuxiliaryStateVariable strain pvp;
pvp.setGlossaryName("EquivalentViscoplasticStrain");

@Parameter stress Kv = 100e6;
Kv.setEntryName("NortonNormalisationFactor");
@Parameter strainrate de0 = 1;
de0.setEntryName("NortonReferenceStrainRate");
@Parameter real Evp = 4.5;
Evp.setEntryName("NortonExponent");

@LocalVariable strainrate vp;

@Integrator {
  constexpr auto eeps = 1.e-14;
  const auto seps = young * eeps;
  const auto seq = sigmaeq(sig);
  const auto iseq = 1 / (max(seq, seps));
  const auto n = 3 * deviator(sig) * (iseq / 2);
  vp = de0 * pow(seq / Kv, Evp);
  // implicit equation associated with elasticity
  feel += dt * vp * n;
  // jacobian blocks
  const auto dvp_dseq = Evp * vp * iseq;
  const auto dvp_ddeel = 2 * mu * theta * dvp_dseq * n;
  const auto dn_ddeel = 2 * mu * theta * iseq * (Stensor4::M() - (n ^ n));
  dfeel_ddeel += dt * (n ^ dvp_ddeel) + dt * vp * dn_ddeel;
}

@UpdateAuxiliaryStateVariables{
  pvp += dt * vp;
}
~~~~

## Viscoplastic behaviour with isotropic hardening



## Viscoplastic behaviour with isotropic hardening and variable coefficients



# Implementation of the plastic part

# Implementation of the plastic-viscoplastic behaviour

\appendix
# Some useful tools {#sec:mfront:assire:useful_tools}

## The `mfront-doc` tool

The `mfront-doc` tool can be used to generate a markdown file describing
the behaviour as follows:

~~~~
$ mfront-doc ImplicitNorton2.mfront -o ImplicitNorton2.md
~~~~

The `ImplicitNorton2.md` markdown file can be parsed by
[`pandoc`](https://pandoc.org/) to a generate a PDF file, a word file or
an web page.

## The `mfront-query` tool

`mfront-query` is a powerful tool to query information about a
behaviour. For example, the following query retrieves the list of the
parameters defined by the behaviour:

~~~~{.bash}
$ mfront-query --parameters ImplicitNorton2.mfront 
- ε: value used to stop the iteration of the implicit algorithm
- θ: theta value used by the implicit scheme
- YoungModulus (young): The Young modulus of an isotropic material
- PoissonRatio (nu): The Poisson ratio of an isotropic material
- RelativeValueForTheEquivalentStressLowerBoundDefinition
  (relative_value_for_the_equivalent_stress_lower_bound): Relative value
  used to define a lower bound for the equivalent stress. For isotropic
  parameters, this lower bound will be equal to this value multiplied by
  the Young modulus. For orthotropic materials, this lower bound will be
  this value multiplied by the first component of the stiffness tensor.
- K
- E
- A
- minimal_time_step_scaling_factor: minimal value for the time step
  scaling factor
- maximal_time_step_scaling_factor: maximal value for the time step
  scaling factor
- numerical_jacobian_epsilon: perturbation value used to compute a
  numerical approximation of the jacobian
- iterMax: maximum number of iterations allowed
~~~~

The list of available queries can be retrieved using the
`--help-behaviour-queries-list` command line option as follows:

~~~~{.cxx}
$ mfront-query --help-behaviour-queries-list
Usage: mfront-query [options] [files]

Available options are : 
--attribute-type                : display an attribute type
--attribute-value               : display an attribute value
--attributes                    : show the list of attributes of the behaviour description
--author                        : show the author name
--auxiliary-state-variables     : show the auxiliary state variables properties for the selected
  modelling hypothesis
--behaviour                     : specify a behaviour identifier (can be a regular expression)
~~~~

# References

