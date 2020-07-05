---
layout: article
title: Small tips on plotting figures in the paper through matplotlib
---


## Small tips

- Use ```matplotlib.rcParams['pdf.fonttype'] = 42``` to avoid the syntax checker of your conference complains about the font
- Add grids for 2d graphs
- Stick to the same toolchain (due to COVID-19, I have to switch the toolchain and spend tons of my time to align the new figures with original figures
- Read the submission guide carefully
- Set the figure size, tight the layout
- It might due to the xticks and yticks "prutudes" out if multiple figures are not aligned
- Try to add a "better" arrow while demonstrating the trade-off
- While hatching the bar plot, ```hatch='///'``` will be denser than ```hatch='/'```
