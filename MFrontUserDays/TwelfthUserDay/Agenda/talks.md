---
title: Abstracts of the twelfth `MFront` User Meeting
author: Thomas Helfer
date: 05/09/2026
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
header-includes: |
     \usepackage{caption}
     \captionsetup[figure]{
        name=,
        labelsep=none,
        labelformat=empty}
---

\newcommand{\Frac}[2]{{{\displaystyle \frac{\displaystyle #1}{\displaystyle #2}}}}
\newcommand{\deriv}[2]{{\displaystyle \frac{\displaystyle \partial #1}{\displaystyle \partial #2}}}
\newcommand{\derivtot}[2]{{\displaystyle \frac{\displaystyle \mathrm{d} #1}{\displaystyle \mathrm{d} #2}}}

<!--
pandoc -f markdown+tex_math_single_backslash --filter pandoc-crossref --citeproc talks.md -o talks.pdf
-->

# Open-source implementation of a flexible nonlocal scheme using MFront/OpenGeoSys

- Ammar Airoud Basmaji
- Mehran Ghasabeh
- Dmitri Naumov
- Thomas Nagel

Institut für Geotechnik, TU Bergakademie Freiberg, Gustav-Zeuner-Str. 1, 09599 Freiberg, Germany

![](img/Nonlocal_OGS_MFront.png)

Nonlocal integration is one possible regularization strategy used in the
modeling of softening geomaterials
[@Bazant2002; @Manica2018; @Parisio2018]. Compared to gradient-enhanced
formulations, it has the advantage of requiring no additional degrees of
freedom and being compatible with different coupling strategies.
Previous work has pointed out various limitations of different
types of nonlocal models [@rolshoven2003nonlocal], which has led to the
adoption of over-nonlocal models in some studies based on one-dimensional 
considerations.

We have implemented a scheme that gives the user flexibility in choosing 
nonlocal or over-nonlocal models of different types using `MFront` and `OpenGeoSys`.
The implementation is geared towards easy and robust use, a flexible
choice of weighting functions, and availability within all coupled
mechanical processes. We demonstrate the application of the scheme to
models that achieve softening via damage evolution or softening laws
related to plastic state variables in 1D and 2D settings. Comparisons are made
to a previous implementation without MFront interaction.

The final application case is a transversely isotropic clay model
accounting for damage as well as elastic, viscous and plastic
deformation mechanisms.

# References {.unnumbered}
