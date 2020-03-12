---
layout: post
published: true
title: Bezier Curves
image: assets/bezier.png
date: 3/11/2020
---
### Quadratic Bezier Curves

See <https://pomax.github.io/bezierinfo/> for details.

The quadratic bezier curve is defined by three control points `$P_1$`, `$P_2$` and `$P_3$` and takes the form:

`$$
\quad \textbf{x}(t) = (1-t)^2 P_1 + 2 t (1-t) P_2 + t^2 P_3
\quad t \in [0, 1]
$$`

![](assets/bezier1.png)
![](assets/bezier2.png)

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

### Arc Length

I took a quick look around the internet and while there is a closed form solution for the arc length of a quadratic Bezier curve, it seems like most people prefer to evaluate the arc length integral numerically. Furthermore, for higher order Bezier curves, there is no general closed form solution and so the arc length must be evaluated numerically. 

The arc length integral is:

`$$
L = \int_0^1 \lVert \textbf{x}'(t) \rVert dt
$$`

I evaluated this numerically by transforming it into a sum:

`$$
L = \frac{1}{N} \sum_{n=0}^{N-1} \lVert \textbf{x}'(\frac{n}{N}) \rVert
$$`

Finally I wanted to take the derivative of the arc length with respect to the control points. 

`$$
\begin{aligned}
    \nabla_{P_i} L 
    &= \nabla_{P_i} \int_0^1 \lVert \textbf{x}'(t) \rVert dt \\
    &= \int_0^1 \nabla_{P_i} \lVert \textbf{x}'(t) \rVert dt \\
	&= \int_0^1 \frac{\textbf{x}'(t)}{\lVert \textbf{x}'(t) \rVert} \nabla_{P_i} \textbf{x}'(t) dt \\
\end{aligned}
$$`

and

`$$
\begin{aligned}
    & \nabla_{P_1} \textbf{x}(t) = 
    \begin{bmatrix} 1 \\ 1 \end{bmatrix}
    \begin{bmatrix} 1 & -2 & 1 \end{bmatrix}
    \begin{bmatrix} 0 \\ 1 \\ 2t \end{bmatrix}
    = 
    (-2 + 2t) \begin{bmatrix} 1 \\ 1 \end{bmatrix} \\
    & \nabla_{P_2} \textbf{x}(t) = 
    \begin{bmatrix} 1 \\ 1 \end{bmatrix}
    \begin{bmatrix} 0 & 2 & -2 \end{bmatrix}
    \begin{bmatrix} 0 \\ 1 \\ 2t \end{bmatrix}
    = 
    (2 - 4t) \begin{bmatrix} 1 \\ 1 \end{bmatrix} \\
    & \nabla_{P_3} \textbf{x}(t) = 
    \begin{bmatrix} 1 \\ 1 \end{bmatrix}
    \begin{bmatrix} 0 & 0 & 1 \end{bmatrix}
    \begin{bmatrix} 0 \\ 1 \\ 2t \end{bmatrix}
    = 
    2t \begin{bmatrix} 1 \\ 1 \end{bmatrix} \\
\end{aligned}
$$`

![](assets/bezier3.png)
![](assets/bezier4.png)

I originally thought that the gradient vector at the endpoints would point straight out so I was a little suprised to find that they don't.