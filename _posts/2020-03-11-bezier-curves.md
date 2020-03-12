---
layout: post
published: true
title: Bezier Curves
image: assets/bezier.png
date: 3/11/2020
---
### Quadratic Bezier Curves

See <https://pomax.github.io/bezierinfo/> for details.

The quadratic bezier curve is defined by three control points `$P_1$`, `$\P_2$` and `$P_3$` and takes the form:

`$$
\quad \textbf{x}(t) = (1-t)^2 P_1 + 2 t (1-t) P_2 + t^2 \P_3
\quad t \in [0, 1]
$$`

It can also be written in matrix form as:

`$$
\textbf{x}(t) = 
\begin{bmatrix} P_1 & P_2 & P_3 \end{bmatrix}
\begin{bmatrix} 1 & -2 & 1 \\ 0 & 2 & -2 \\ 0 & 0 & 1 \end{bmatrix}
\begin{bmatrix} 1 \\ t \\ t^2 \end{bmatrix}
$$`

Its derivative with respect to `$t$`:

`$$
\textbf{x}(t) = 
\begin{bmatrix} P_1 & P_2 & P_3 \end{bmatrix}
\begin{bmatrix} 1 & -2 & 1 \\ 0 & 2 & -2 \\ 0 & 0 & 1 \end{bmatrix}
\begin{bmatrix} 0 \\ 1 \\ 2 t \end{bmatrix}
$$`

