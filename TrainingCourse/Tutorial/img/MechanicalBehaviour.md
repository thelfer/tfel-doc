\newcommand{\paren}[1]{\left(#1\right)}
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
pandoc -f markdown+tex_math_single_backslash -F pandoc-crossref --citeproc --highlight-style=tango --default-image-extension=pdf Implementation.md -o Implementation.pdf
-->

\[
  \paren{
    \bts{\tepsilonto} \,,
    \bts{\vec{Y}} \,,
    \Delta\,\tepsilonto \,,
    \Delta\,t
  }
  \underbrace{\Longrightarrow}_{behaviour}
  \paren{
    \ets{\tsigma} \,,
    \ets{\vec{Y}} \,,
    \deriv{\Delta\,\tsigma}{\Delta\,\tepsilonto}
  }
\]

