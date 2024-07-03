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

# An implementation of a unilateral constitutive law for a dynamic damage pĥase field modeling owing to a FEniCSx/MFront association

- Lamia Mersel
- Pascal Bouda
  - Université Paris-Saclay, CEA, Service d’Etudes Mécaniques et Thermiques, 91191 Gif-sur-Yvette, France

# No title yet

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
SICRAC, a creep simulation tool for the justification of irradiated fuel rod integrity in back-end conditions

# SICRAC, a creep simulation tool for the justification of irradiated fuel rod integrity in back-end conditions

- A. El Abdi
  - EDF R&D, MMC Department Avenue des Renardières, 77818
    Moret-Sur-Loing, France
- J. Jonnet
  - EDF R&D, MMC Department Avenue des Renardières, 77818
    Moret-Sur-Loing, France
- A. Ambard
  - EDF R&D, MMC Department Avenue des Renardières, 77818
    Moret-Sur-Loing, France
- M. Ton-That
  - EDF, Technical Division Rue Pierre Bourdeix, 69007 Lyon, France
- D. COSTA.
  - EDF, Nuclear Fuel Division, Pl. Pleyel, 93200 Saint-Denis, France
- L. Idoux
  - EDF R&D, PERICLES Department, Bd Gaspard Monge, 91120 Palaiseau, France

Interim or extended storage of spent nuclear fuel assemblies in dry
conditions is primarily used by worldwide nuclear utilities and
countries that do not integrate fuel reprocessing in their fuel cycle.
In France, EDF does not rule out the option of dry storage to alleviate
the potential stresses on wet storage facilities, whether in nuclear
power plant pools or the centralised storage facility.

Cladding creep is identified as the most detrimental mechanism to the
integrity of the first barrier in dry storage conditions. Depending on
the first-order parameters on creep, namely the end-of-life internal
pressure and cladding temperature induced by the storage conditions,
non-rupture of the irradiated fuel rod needs to be demonstrated over the
target interim storage duration.

A dedicated tool has been developed to calculate the creep of an
irradiated fuel rod cladding under back-end conditions. This numerical
tool, named SICRAC, is developed in python and is based on the MFront
and Ptest simulation tools for materials mechanical behaviour. Similarly
to 1.5D fuel rod codes, the rod is discretized in axial segments. The
end-of-irradiation state of the fuel rod (cladding outer diameter, outer
oxide layer thickness, cladding fluence, rod free volumes) can be
directly used as input data for the calculation in back-end conditions.
Most importantly, the present tool includes a feedback loop that takes
into account the increase of the rod free volume due to the cladding
deformation and subsequent decrease of the internal pressure, also
slightly influenced by the differential thermal expansions of the fuel
pellets and cladding. This feedback mechanism is representative of a
leak-free irradiated fuel rod that would creep under internal pressure
with a given temperature axial profile. Finally, for demonstration
purposes, a peer-reviewed creep rupture criterion for Zircaloy-4 fuel
cladding under internal pressure is integrated into the tool.

The above-mentioned basic mechanisms are presented in the paper. By
design, the different modules of the tool can be separately and simply
checked, which makes the code robust when challenged in a safety
demonstration. A sensitivity analysis is also presented, highlighting
the effect of the main parameters likely to affect the rod integrity
under the envisaged dry-storage conditions and accordingly to the
rupture criterion. It shows a strong dependence of the creep deformation
on the temperature and thus the benefit resulting from considering the
temperature decrease due to the decay heat production rate decrease. It
also shows the importance of free volume distribution and of considering
a temperature profile along the fuel rod rather than a uniform
temperature.

<!--
# VTT?
-->

# References