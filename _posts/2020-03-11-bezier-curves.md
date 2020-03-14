---
layout: post
published: true
title: Bezier Curves
image: assets/bezier.png
date: 3/11/2020
---
### Motivation
* routing application
* options for defining the paths
* optimization
* bezier curves

### Quadratic Bezier Curves

See <https://pomax.github.io/bezierinfo/> for details.

The quadratic bezier curve is defined by three control points `$P_1$`, `$P_2$` and `$P_3$` and takes the form:

`$$
\quad \textbf{x}(t) = (1-t)^2 P_1 + 2 t (1-t) P_2 + t^2 P_3
\quad t \in [0, 1]
$$`

![](/assets/bezier1.png)
![](/assets/bezier2.png)

It can also be written in matrix form as:

`$$
\textbf{x}(t) = 
\begin{bmatrix} P_1 & P_2 & P_3 \end{bmatrix}
\begin{bmatrix} 1 & -2 & 1 \\ 0 & 2 & -2 \\ 0 & 0 & 1 \end{bmatrix}
\begin{bmatrix} 1 \\ t \\ t^2 \end{bmatrix} = P M T
$$`

Its derivative with respect to `$t$`:

`$$
\textbf{x}(t) = 
\begin{bmatrix} P_1 & P_2 & P_3 \end{bmatrix}
\begin{bmatrix} 1 & -2 & 1 \\ 0 & 2 & -2 \\ 0 & 0 & 1 \end{bmatrix}
\begin{bmatrix} 0 \\ 1 \\ 2 t \end{bmatrix} = P M T'
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
    & \nabla_{P_1} \textbf{x}'(t) = 
    \begin{bmatrix} 1 & -2 & 1 \end{bmatrix}
    \begin{bmatrix} 0 \\ 1 \\ 2t \end{bmatrix}
    \begin{bmatrix} 1 & 0 \\0 & 1 \end{bmatrix}
    = 
    (-2 + 2t) \begin{bmatrix} 1 & 0 \\0 & 1 \end{bmatrix} \\
    & \nabla_{P_2} \textbf{x}'(t) = 
    \begin{bmatrix} 0 & 2 & -2 \end{bmatrix}
    \begin{bmatrix} 0 \\ 1 \\ 2t \end{bmatrix}
    \begin{bmatrix} 1 & 0 \\0 & 1 \end{bmatrix}
    = 
    (2 - 4t) \begin{bmatrix} 1 & 0 \\0 & 1 \end{bmatrix} \\
    & \nabla_{P_3} \textbf{x}'(t) = 
    \begin{bmatrix} 0 & 0 & 1 \end{bmatrix}
    \begin{bmatrix} 0 \\ 1 \\ 2t \end{bmatrix}
    \begin{bmatrix} 1 & 0 \\0 & 1 \end{bmatrix}
    = 
    2t \begin{bmatrix} 1 & 0 \\0 & 1 \end{bmatrix} \\
\end{aligned}
$$`

![](/assets/bezier3.png)
![](/assets/bezier4.png)

I originally thought that the gradient vector at the endpoints would point straight out so I was a little suprised to find that they don't.

### Closest Point on Bezier Curve to Another Point `$Q$`

To find the closest point on the curve to another point `$Q$`, I started with the expression:

`$$
\begin{aligned}
    \arg \min_t \lVert \textbf{x}(t) - Q \rVert 
    &= \arg \min_t \frac{1}{2} \lVert \textbf{x}(t) - Q \rVert^2 \\
    &= \arg \min_t \frac{1}{2} (\textbf{x}(t) - Q) \cdot (\textbf{x}(t) - Q)
\end{aligned}
$$`

I decided to estimate this using Newton's method, which has the following iteration:

`$$
t^{k+1} = t^k - \alpha f''(t^k)^{-1} f'(t^k)
$$`

where `$\alpha$` is the step size, often set to 1.

Taking two derivatives of the cost function:

`$$
\begin{aligned}
    & f(t) = \frac{1}{2} (\textbf{x}(t) - Q) \cdot (\textbf{x}(t) - Q) \\
    & f'(t) = (\textbf{x}(t) - Q) \cdot \textbf{x}'(t) \\
    & f''(t) = \textbf{x}'(t) \cdot \textbf{x}'(t) + (\textbf{x}(t) - Q) \cdot \textbf{x}''(t) \\
\end{aligned}
$$`

This is basically everything we need to solve the problem. The final detail has to do with the fact that we are optimizing over a finite set `$t \in [0, 1]$`. This is very easy to deal with because the problem is 1-dimensional. All I do is check after each iteration if `$t$` is above 1 or below 0, and if it is, I just return 1 or 0 respectively. This is basically a 'gradient projection method' because projecting `$t$` onto `$[0, 1]$` is just a clamping operation. Finally, because the cost function is a 4th order polynomial, there are potentially several local minima. To deal with this, I perform the minimization starting once at `$t = 0$` and once at `$t = 1$`. If the results are the same then that is the answer I keep, if they are different, I calculate the cost at each of the two points as well as at the two boundary points and keep the lowest of the 4. 

### Gradient of Minimum Distance WRT the Control Points

This is the cool part. The goal here is to think of `$ f(t) = \frac{1}{2} (\textbf{x}(t) - Q) \cdot (\textbf{x}(t) - Q) $` as a function of the control points as well.

`$$
\quad f(t, P) = \frac{1}{2} (\textbf{x}(t, P) - Q) \cdot (\textbf{x}(t, P) - Q),
\quad P = (P_1, P_2, P_3)
$$`

Then I want to take the gradient:

`$$
\nabla_P \min_t f(t, P)
$$`

I'm pretty sure the following is correct, but email me if I made a mistake. The overall picture looks like this:

![](/assets/bezier_derivation_1.png)

The blue curve is `$\textbf{x}(t)$` and the green curve is `$\textbf{x}'(t)$` where I displaced `$P_2$` by a tiny bit. The black dot is the point `$Q$`. Zooming in:

![](/assets/bezier_derivation_2.png)

`$\textbf{x}(t^*)$` is the closest point on `$\textbf{x}$` to `$Q$`. After I move `$P_2$`, `$t^*$` may not be the closest point to `$Q$` on `$\textbf{x}'$`. The actual closest point to `$Q$` on `$\textbf{x}'$` is `$\textbf{x}'(t^{*'})$`. Zooming in again:

![](/assets/bezier_derivation_3.png)

The change in `$f$` due to a change in `$P_2$` has two components. The first component, labeled 'A' in the picture, is due to the fact that EVERY point on `$\textbf{x}$` changes when one of the control points changes. This is decribed by the gradient (Jacobian) of `$\textbf{x}$` with respect to `$P$`. 

`$$
\nabla_{P_i} \textbf{x} = 
$$`

The second component, labeled 'B', comes from the change in `$t$`, `$\Delta t = t^{*'} - t^*$`, required to get to the point on `$\textbf{x}'$` which is closest to `$Q$`. 

Another way to think about it is that although `$t$` and `$P$` are independent variables for `$f$`, there is a specific combination of `$t$` and `$P$` which minimize `$f$`. This means that when only considering the minimum of `$f$`, you can think of `$t$` as a function of `$P$` and `$f$` as `$f(\phi(P), P)$` where `$t = \phi(P)$`. To take the derivative of a function of this form, you need to use the chain rule to take the __total derivative__. Using the chain rule:

`$$
\nabla_P f(t(P), P) = \nabla_P f + (\nabla_P \phi) (\frac{\partial f}{\partial t})
$$`

Finally, the [implicit function theorem](https://en.wikipedia.org/wiki/Implicit_function_theorem) tells us how to find `$\nabla_P \phi$`.

`$$
\nabla_P \phi = - (\nabla_P g) (\nabla_t g)^{-1} = - (\nabla_P g) (\frac{\partial g}{\partial t})^{-1}
$$`

At last, a dump of all the derivatives needed to put it all together:

`$$
\begin{aligned}
    &P = \begin{bmatrix} P_1 & P_2 & P_3 \end{bmatrix} \\
    &M = \begin{bmatrix} 1 & -2 & 1 \\ 0 & 2 & -2 \\ 0 & 0 & 1 \end{bmatrix} \\
    &T = \begin{bmatrix} 1 & t & t^2 \end{bmatrix} \\
    &T' = \begin{bmatrix} 0 & 1 & 2t \end{bmatrix} \\
    &T'' = \begin{bmatrix} 0 & 0 & 2 \end{bmatrix} \\
    &\textbf{x} = PMT  \\
    &\frac{\partial \textbf{x}}{\partial t} =  PMT' \\
    &f = \frac{1}{2} (\textbf{x} - Q) \cdot (\textbf{x} - Q) \\
    &g = \frac{\partial f}{\partial t} = (\textbf{x} - Q) \cdot (\frac{\partial \textbf{x}}{\partial t}) \\
    &\nabla_P f = (\textbf{x} - Q) \cdot \nabla_P \textbf{x} \\
    &\nabla_P g = \nabla_P (\frac{\partial f}{\partial t}) = (\nabla_P \textbf{x}) \cdot (\frac{\partial \textbf{x}}{\partial t}) + (\textbf{x} - Q) \cdot \nabla_P (\frac{\partial \textbf{x}}{\partial t}) \\
    &\frac{\partial g}{\partial t} = \frac{\partial^2 f}{\partial t^2} = (\frac{\partial \textbf{x}}{\partial t}) \cdot (\frac{\partial \textbf{x}}{\partial t}) + (\textbf{x} - Q) \cdot (\frac{\partial^2 \textbf{x}}{\partial t^2})
\end{aligned}
$$`

Using all this, I can now minimize the function:

`$$
J(P_2) = -\log(F_Q) + \lambda L
$$`

Where `$F_Q$` is the minimum distance from the curve `$\textbf{x}$` to the point `$Q$`, `$L$` is the arc length of `$\textbf{x}$`, and `$\lambda$` is a weighting coefficient. 

![](/assets/bezier_optimization_1.png)
![](/assets/bezier_optimization_2.png)
