---
title: "Creating beautiful Hasse diagrams with little effort"
date: 2023-04-21
layout: post
usemathjax: true
---

The Hasse diagram of a partially ordered set (poset) is an extremely useful
graph to help understand the poset. It is also a pain in the keister to
create them in \\(\LaTeX\\) using Tikz. Here's how to do it painlessly
using Sage.

# Prerequisites

First, make sure that you have `graphviz` and `dot2tex` installed. (Thanks to [this ask.sagemath post](https://ask.sagemath.org/question/63200/latex-for-hasse-diagram-of-poset-not-properly-laid-out/) for pointing this out.)
You can do this by calling
```
sage --pip install graphviz
sage --pip install dot2tex
```
in your terminal.

# Making the diagram

First, create your poset in Sage. For example
```
sage: P = posets.SetPartitions(4)
```
Of course, you could also make your own custom poset, but that is [well-documented](https://doc.sagemath.org/html/en/reference/combinat/sage/combinat/posets/posets.html#sage.combinat.posets.posets.Poset), so I won't rehash that.

Now, calling `view(P)` will create a PDF of the Hasse diagram. For me, it automatically opens in my system viewer. I hope it does the same for you. From there I could "save-as" to put the PDF where I want it. I know I could probably choose a custom path yada yada yada... but my file-saving GUI is nice so I use it. If it looks funky and curvy, double check that you installed `graphviz` and `dot2tex`. Sometimes, a PDF is enough, and you can include it into your \\(\LaTeX\\) document using `\includegraphics`. 

# Getting the code

Sometimes, you might want to customize the image to call attention to a specific element (for me, I'd do this when localizing at a specific flat in a matroid). To get the Tikz code
that create the beautiful PDF, just run `view(P, debug=True)`. 

**Edit: 3/20/24** It turns out to be easier to import the `_latex_file_` function after running `from sage.misc.latex import _latex_file_`. Still set `debug=True`. 

This will give you a long output. But the top part is what matters. Heres what I get

```
sage: view(P, debug=True)
\documentclass{article}
\usepackage{amsmath}
\usepackage{amssymb}
\usepackage{amsfonts}
\usepackage{graphicx}
\usepackage{mathrsfs}
\pagestyle{empty}
\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\oddsidemargin 0.0in
\evensidemargin 0.0in
\textwidth 6.45in
\topmargin 0.0in
\headheight 0.0in
\headsep 0.0in
\textheight 9.0in

\usepackage{tikz}
\usepackage{tkz-graph}
\usepackage{tkz-berge}
\usetikzlibrary{arrows,shapes}
\newcommand{\ZZ}{\Bold{Z}}
\newcommand{\NN}{\Bold{N}}
\newcommand{\RR}{\Bold{R}}
\newcommand{\CC}{\Bold{C}}
\newcommand{\QQ}{\Bold{Q}}
\newcommand{\QQbar}{\overline{\QQ}}
\newcommand{\GF}[1]{\Bold{F}_{#1}}
\newcommand{\Zp}[1]{\Bold{Z}_{#1}}
\newcommand{\Qp}[1]{\Bold{Q}_{#1}}
\newcommand{\Zmod}[1]{\ZZ/#1\ZZ}
\newcommand{\CDF}{\Bold{C}}
\newcommand{\CIF}{\Bold{C}}
\newcommand{\CLF}{\Bold{C}}
\newcommand{\RDF}{\Bold{R}}
\newcommand{\RIF}{\Bold{I} \Bold{R}}
\newcommand{\RLF}{\Bold{R}}
\newcommand{\SL}{\mathrm{SL}}
\newcommand{\PSL}{\mathrm{PSL}}
\newcommand{\Bold}[1]{\mathbf{#1}}
\usepackage[tightpage,active]{preview}
\PreviewEnvironment{page}
\begin{document}\begin{page}$\begin{tikzpicture}[>=latex,line join=bevel,]
%%
\node (node_0) at (285.0bp,8.5bp) [draw,draw=none] {$\{\{1\}, \{2\}, \{3\}, \{4\}\}$};
  \node (node_1) at (236.0bp,61.5bp) [draw,draw=none] {$\{\{1\}, \{2\}, \{3, 4\}\}$};
  \node (node_2) at (138.0bp,61.5bp) [draw,draw=none] {$\{\{1\}, \{2, 3\}, \{4\}\}$};
  \node (node_3) at (334.0bp,61.5bp) [draw,draw=none] {$\{\{1\}, \{2, 4\}, \{3\}\}$};
  \node (node_5) at (40.0bp,61.5bp) [draw,draw=none] {$\{\{1, 2\}, \{3\}, \{4\}\}$};
  \node (node_7) at (530.0bp,61.5bp) [draw,draw=none] {$\{\{1, 3\}, \{2\}, \{4\}\}$};
  \node (node_10) at (432.0bp,61.5bp) [draw,draw=none] {$\{\{1, 4\}, \{2\}, \{3\}\}$};
  \node (node_4) at (221.0bp,114.5bp) [draw,draw=none] {$\{\{1\}, \{2, 3, 4\}\}$};
  \node (node_6) at (45.0bp,114.5bp) [draw,draw=none] {$\{\{1, 2\}, \{3, 4\}\}$};
  \node (node_12) at (485.0bp,114.5bp) [draw,draw=none] {$\{\{1, 3, 4\}, \{2\}\}$};
  \node (node_8) at (133.0bp,114.5bp) [draw,draw=none] {$\{\{1, 2, 3\}, \{4\}\}$};
  \node (node_13) at (397.0bp,114.5bp) [draw,draw=none] {$\{\{1, 4\}, \{2, 3\}\}$};
  \node (node_9) at (573.0bp,114.5bp) [draw,draw=none] {$\{\{1, 3\}, \{2, 4\}\}$};
  \node (node_11) at (309.0bp,114.5bp) [draw,draw=none] {$\{\{1, 2, 4\}, \{3\}\}$};
  \node (node_14) at (309.0bp,167.5bp) [draw,draw=none] {$\{\{1, 2, 3, 4\}\}$};
  \draw [black,->] (node_0) ..controls (271.1bp,23.968bp) and (259.79bp,35.744bp)  .. (node_1);
  \draw [black,->] (node_0) ..controls (238.91bp,25.49bp) and (198.0bp,39.683bp)  .. (node_2);
  \draw [black,->] (node_0) ..controls (298.9bp,23.968bp) and (310.21bp,35.744bp)  .. (node_3);
  \draw [black,->] (node_0) ..controls (206.08bp,25.929bp) and (133.85bp,40.965bp)  .. (node_5);
  \draw [black,->] (node_0) ..controls (363.92bp,25.929bp) and (436.15bp,40.965bp)  .. (node_7);
  \draw [black,->] (node_0) ..controls (331.09bp,25.49bp) and (372.0bp,39.683bp)  .. (node_10);
  \draw [black,->] (node_1) ..controls (231.92bp,76.365bp) and (228.84bp,86.851bp)  .. (node_4);
  \draw [black,->] (node_1) ..controls (175.27bp,78.716bp) and (120.14bp,93.438bp)  .. (node_6);
  \draw [black,->] (node_1) ..controls (316.71bp,79.031bp) and (391.31bp,94.31bp)  .. (node_12);
  \draw [black,->] (node_2) ..controls (162.88bp,77.79bp) and (183.95bp,90.737bp)  .. (node_4);
  \draw [black,->] (node_2) ..controls (136.66bp,76.215bp) and (135.66bp,86.386bp)  .. (node_8);
  \draw [black,->] (node_2) ..controls (222.57bp,79.153bp) and (301.64bp,94.722bp)  .. (node_13);
  \draw [black,->] (node_3) ..controls (299.45bp,78.091bp) and (269.25bp,91.721bp)  .. (node_4);
  \draw [black,->] (node_3) ..controls (411.02bp,78.935bp) and (481.56bp,93.987bp)  .. (node_9);
  \draw [black,->] (node_3) ..controls (327.13bp,76.516bp) and (321.83bp,87.318bp)  .. (node_11);
  \draw [black,->] (node_4) ..controls (247.51bp,130.87bp) and (270.15bp,143.98bp)  .. (node_14);
  \draw [black,->] (node_5) ..controls (41.345bp,76.215bp) and (42.342bp,86.386bp)  .. (node_6);
  \draw [black,->] (node_5) ..controls (68.088bp,77.903bp) and (92.161bp,91.105bp)  .. (node_8);
  \draw [black,->] (node_5) ..controls (128.45bp,79.269bp) and (212.04bp,95.117bp)  .. (node_11);
  \draw [black,->] (node_6) ..controls (128.44bp,131.62bp) and (216.64bp,148.66bp)  .. (node_14);
  \draw [black,->] (node_7) ..controls (487.07bp,69.16bp) and (483.98bp,69.595bp)  .. (481.0bp,70.0bp) .. controls (349.6bp,87.831bp) and (313.48bp,86.638bp)  .. (node_8);
  \draw [black,->] (node_7) ..controls (542.13bp,76.892bp) and (551.93bp,88.504bp)  .. (node_9);
  \draw [black,->] (node_7) ..controls (517.3bp,76.892bp) and (507.06bp,88.504bp)  .. (node_12);
  \draw [black,->] (node_8) ..controls (188.7bp,131.64bp) and (238.89bp,146.19bp)  .. (node_14);
  \draw [black,->] (node_9) ..controls (489.56bp,131.62bp) and (401.36bp,148.66bp)  .. (node_14);
  \draw [black,->] (node_10) ..controls (393.89bp,78.301bp) and (360.71bp,92.061bp)  .. (node_11);
  \draw [black,->] (node_10) ..controls (447.11bp,77.043bp) and (459.52bp,88.985bp)  .. (node_12);
  \draw [black,->] (node_10) ..controls (422.28bp,76.666bp) and (414.64bp,87.79bp)  .. (node_13);
  \draw [black,->] (node_11) ..controls (309.0bp,129.21bp) and (309.0bp,139.39bp)  .. (node_14);
  \draw [black,->] (node_12) ..controls (429.3bp,131.64bp) and (379.11bp,146.19bp)  .. (node_14);
  \draw [black,->] (node_13) ..controls (370.49bp,130.87bp) and (347.85bp,143.98bp)  .. (node_14);
%
\end{tikzpicture}$\end{page}
\end{document}
['pdflatex', '\\nonstopmode', '\\input{sage.tex}']
This is pdfTeX, Version 3.141592653-2.6-1.40.25 (TeX Live 2023) (preloaded format=pdflatex)
 restricted \write18 enabled.
entering extended mode
LaTeX2e <2022-11-01> patch level 1
...
(see the transcript file for additional information)</usr/local/texlive/2023/te
xmf-dist/fonts/type1/public/amsfonts/cm/cmmi10.pfb></usr/local/texlive/2023/tex
mf-dist/fonts/type1/public/amsfonts/cm/cmr10.pfb></usr/local/texlive/2023/texmf
-dist/fonts/type1/public/amsfonts/cm/cmsy10.pfb>
Output written on sage.pdf (1 page, 29017 bytes).
Transcript written on sage.log.
viewer: "open"
```
The `...` eight lines from the bottom represents over a hundred lines of output. Don't worry about those to get your Tikz output. All you need is
```
\begin{tikzpicture}[>=latex,line join=bevel,]
%%
\node (node_0) at (285.0bp,8.5bp) [draw,draw=none] {$\{\{1\}, \{2\}, \{3\}, \{4\}\}$};
  \node (node_1) at (236.0bp,61.5bp) [draw,draw=none] {$\{\{1\}, \{2\}, \{3, 4\}\}$};
  \node (node_2) at (138.0bp,61.5bp) [draw,draw=none] {$\{\{1\}, \{2, 3\}, \{4\}\}$};
  \node (node_3) at (334.0bp,61.5bp) [draw,draw=none] {$\{\{1\}, \{2, 4\}, \{3\}\}$};
  \node (node_5) at (40.0bp,61.5bp) [draw,draw=none] {$\{\{1, 2\}, \{3\}, \{4\}\}$};
  \node (node_7) at (530.0bp,61.5bp) [draw,draw=none] {$\{\{1, 3\}, \{2\}, \{4\}\}$};
  \node (node_10) at (432.0bp,61.5bp) [draw,draw=none] {$\{\{1, 4\}, \{2\}, \{3\}\}$};
  \node (node_4) at (221.0bp,114.5bp) [draw,draw=none] {$\{\{1\}, \{2, 3, 4\}\}$};
  \node (node_6) at (45.0bp,114.5bp) [draw,draw=none] {$\{\{1, 2\}, \{3, 4\}\}$};
  \node (node_12) at (485.0bp,114.5bp) [draw,draw=none] {$\{\{1, 3, 4\}, \{2\}\}$};
  \node (node_8) at (133.0bp,114.5bp) [draw,draw=none] {$\{\{1, 2, 3\}, \{4\}\}$};
  \node (node_13) at (397.0bp,114.5bp) [draw,draw=none] {$\{\{1, 4\}, \{2, 3\}\}$};
  \node (node_9) at (573.0bp,114.5bp) [draw,draw=none] {$\{\{1, 3\}, \{2, 4\}\}$};
  \node (node_11) at (309.0bp,114.5bp) [draw,draw=none] {$\{\{1, 2, 4\}, \{3\}\}$};
  \node (node_14) at (309.0bp,167.5bp) [draw,draw=none] {$\{\{1, 2, 3, 4\}\}$};
  \draw [black,->] (node_0) ..controls (271.1bp,23.968bp) and (259.79bp,35.744bp)  .. (node_1);
  \draw [black,->] (node_0) ..controls (238.91bp,25.49bp) and (198.0bp,39.683bp)  .. (node_2);
  \draw [black,->] (node_0) ..controls (298.9bp,23.968bp) and (310.21bp,35.744bp)  .. (node_3);
  \draw [black,->] (node_0) ..controls (206.08bp,25.929bp) and (133.85bp,40.965bp)  .. (node_5);
  \draw [black,->] (node_0) ..controls (363.92bp,25.929bp) and (436.15bp,40.965bp)  .. (node_7);
  \draw [black,->] (node_0) ..controls (331.09bp,25.49bp) and (372.0bp,39.683bp)  .. (node_10);
  \draw [black,->] (node_1) ..controls (231.92bp,76.365bp) and (228.84bp,86.851bp)  .. (node_4);
  \draw [black,->] (node_1) ..controls (175.27bp,78.716bp) and (120.14bp,93.438bp)  .. (node_6);
  \draw [black,->] (node_1) ..controls (316.71bp,79.031bp) and (391.31bp,94.31bp)  .. (node_12);
  \draw [black,->] (node_2) ..controls (162.88bp,77.79bp) and (183.95bp,90.737bp)  .. (node_4);
  \draw [black,->] (node_2) ..controls (136.66bp,76.215bp) and (135.66bp,86.386bp)  .. (node_8);
  \draw [black,->] (node_2) ..controls (222.57bp,79.153bp) and (301.64bp,94.722bp)  .. (node_13);
  \draw [black,->] (node_3) ..controls (299.45bp,78.091bp) and (269.25bp,91.721bp)  .. (node_4);
  \draw [black,->] (node_3) ..controls (411.02bp,78.935bp) and (481.56bp,93.987bp)  .. (node_9);
  \draw [black,->] (node_3) ..controls (327.13bp,76.516bp) and (321.83bp,87.318bp)  .. (node_11);
  \draw [black,->] (node_4) ..controls (247.51bp,130.87bp) and (270.15bp,143.98bp)  .. (node_14);
  \draw [black,->] (node_5) ..controls (41.345bp,76.215bp) and (42.342bp,86.386bp)  .. (node_6);
  \draw [black,->] (node_5) ..controls (68.088bp,77.903bp) and (92.161bp,91.105bp)  .. (node_8);
  \draw [black,->] (node_5) ..controls (128.45bp,79.269bp) and (212.04bp,95.117bp)  .. (node_11);
  \draw [black,->] (node_6) ..controls (128.44bp,131.62bp) and (216.64bp,148.66bp)  .. (node_14);
  \draw [black,->] (node_7) ..controls (487.07bp,69.16bp) and (483.98bp,69.595bp)  .. (481.0bp,70.0bp) .. controls (349.6bp,87.831bp) and (313.48bp,86.638bp)  .. (node_8);
  \draw [black,->] (node_7) ..controls (542.13bp,76.892bp) and (551.93bp,88.504bp)  .. (node_9);
  \draw [black,->] (node_7) ..controls (517.3bp,76.892bp) and (507.06bp,88.504bp)  .. (node_12);
  \draw [black,->] (node_8) ..controls (188.7bp,131.64bp) and (238.89bp,146.19bp)  .. (node_14);
  \draw [black,->] (node_9) ..controls (489.56bp,131.62bp) and (401.36bp,148.66bp)  .. (node_14);
  \draw [black,->] (node_10) ..controls (393.89bp,78.301bp) and (360.71bp,92.061bp)  .. (node_11);
  \draw [black,->] (node_10) ..controls (447.11bp,77.043bp) and (459.52bp,88.985bp)  .. (node_12);
  \draw [black,->] (node_10) ..controls (422.28bp,76.666bp) and (414.64bp,87.79bp)  .. (node_13);
  \draw [black,->] (node_11) ..controls (309.0bp,129.21bp) and (309.0bp,139.39bp)  .. (node_14);
  \draw [black,->] (node_12) ..controls (429.3bp,131.64bp) and (379.11bp,146.19bp)  .. (node_14);
  \draw [black,->] (node_13) ..controls (370.49bp,130.87bp) and (347.85bp,143.98bp)  .. (node_14);
%
\end{tikzpicture}
```

Thats it. Thats the post.
