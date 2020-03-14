---
layout: post
published: true
title: Bezier Curves
image: assets/bezier.png
date: 3/11/2020
---
### Motivation
The motivation of this started because I am working on autorouting for 3D printed electrical circuits. This is where you have the locations and orientations of a set of electrical components and you want to determine routes for the wires to connect everything together. More generally, you are given a set of points and a set of connections between those points and you want to figure out the paths which should connect the points while being subject to various constraints. This is a complicated problem and there are many ways to go about it. In general, the paths I select I want to be optimal with respect to some objective function that I will define. My objective function will resemble the following:

`$$
\text{Cost} = -\sum_{i \neq j \in W}\log F(i, j) + \lambda \sum_{i \in W} L(i)
$$`

where `$W$` is the set of routes, `$F(i, j)$` is some measure of the distance between the `$i$`th and the `$j$`th route and `$L(i)$` is some measure of the length of the `$i$`th route. Minimizing this objective function will produce routes which are generally as short as possible but don't come too close to one another. In order to begin minimizing this sort of function, we first need to mathematically define the routes. 

The first way of defining the routes I am considering is to create a set of nodes corresponding to points fairly evenly distributed in 3D space. Then each route could be an ordered list of these nodes. The actual function defining the trajectory would be a piecewise linear function with line segments connecting the various nodes in the list. This is a promising way to do this because I think there are already fairly well established methods for optimizing over this kind of data structure. Search for "network optimization", "maximum flow" or "minimum cost flow". 

Another option, and the one I will focus on here, is to use some sort of parametric curve, like a Bezier curve. 

### Quadratic Bezier Curves

The quadratic bezier curve is a parametric curve defined by three control points `$P_1$`, `$P_2$` and `$P_3$`. It takes the form:

`$$
\quad \textbf{x}(t) = (x_1(t), x_2(t), x_3(t))' = (1-t)^2 P_1 + 2 t (1-t) P_2 + t^2 P_3
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

I won't go into too much detail about the curves themselves because there are already great resources for that. Try: <https://pomax.github.io/bezierinfo/>

Ultimately, Bezier curves might be a good way to define the routes because they are defined by variables. If I can take gradients of my cost function with respect to these variables, then I can directly optimize the routes using gradient based methods. In the following sections, I will work towards taking the gradient of my cost function with respect to the control points.

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

### Projection of Point `$Q$` onto a Bezier Curve

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

The change in `$f$` due to a change in `$P_2$` has two components, `$A + B = C$`. The first component, labeled 'A' in the picture, is due to the fact that EVERY point on `$\textbf{x}$` changes when one of the control points changes. This is decribed by the gradient (Jacobian) of `$\textbf{x}$` with respect to `$P$`. 

`$$
A = \nabla_{P_i} \textbf{x} = M_i \cdot T \begin{bmatrix} 1 & 0 \\0 & 1 \end{bmatrix}
$$`

The way I think of this is that if I move `$P_i$` by a vector `$\textbf{u}$`, then the point `$\textbf{x}(t)$` will move by (approximately) `$(\nabla_{P_i} \textbf{x}) \cdot \textbf{u}$`, so `$\textbf{x}'(t) = \textbf{x}(t) +  (\nabla_{P_i} \textbf{x}) \cdot \textbf{u}$`.

The second component, labeled 'B', comes from the change in `$t$`, `$\Delta t = t^{*'} - t^*$`, required to get to the point on `$\textbf{x}'$` which is closest to `$Q$`. This is where things get a little complicated. On `$\textbf{x}$`, the closest point to `$Q$` will satisfy the condition `$f'(t, P) = 0$`. This equation defines a surface such that if we plug in a value for `$P$`, there is only one possible value for `$t$`. This description basically defines a function who's input is `$P$` and who's output is `$t$`. This is known as an __implicitly defined function__ because we haven't explicitly written a function of the variables `$P$`, we've just defined a relationship between `$P$` and `$t$` using an equation. Even though we may not be able to write the function down, let's pretend we can and call it `$\phi(P)$`. Remember, we want to know, for a small change in `$P$`, what is the change in `$t$` that will get us to the minimizer of `$f$`. This is just the gradient of `$\phi(P)$` (with respect to `$P$`). The [implicit function theorem](https://en.wikipedia.org/wiki/Implicit_function_theorem) tells us how to find `$\nabla_P \phi$`.

`$$
\nabla_P \phi = - (\nabla_P g) (\nabla_t g)^{-1} = - (\nabla_P g) (\frac{\partial g}{\partial t})^{-1}
$$`

where `$g = \frac{\partial f}{\partial t}$`.

In order to turn this into a change in `$\textbf{x}$`, we just need to multiply this by the derivative of `$\textbf{x}$` with respect to `$t$`. This is basically just an application of the chain rule. Overall:

`$$
B = (\nabla_P \phi) \cdot (\frac{\partial \textbf{x}}{\partial t})
$$`

So far what we have is A, B and C which gives us the change in the projection of `$Q$` onto `$\textbf{x}$` due to a change in `$P$`. Using the chain rule agian, we can get the gradient of the minimum distance with repect to `$P$` by multiplying `$C$` by the gradient of `$f$` with respect to `$\textbf{x}$`. 

`$$
\begin{aligned}
    \nabla_{P_i} \min_t f(t, P) 
    &= ((\nabla_{P_i} \phi) \cdot (\frac{\partial \textbf{x}}{\partial t}) + \nabla_{P_i} \textbf{x}) \cdot \nabla_{\textbf{x}} f \\
    &= (\nabla_{P_i} \phi) \cdot (\frac{\partial f}{\partial t}) + \nabla_{P_i} f \\
\end{aligned}
$$`

This looks a lot like the __total derivative__ of `$\min_t f(t, P)$`. Using all this, I can now minimize the function:

`$$
J(P_2) = -\log(F_Q) + \lambda L
$$`

Where `$F_Q$` is the minimum distance from the curve `$\textbf{x}$` to the point `$Q$`, `$L$` is the arc length of `$\textbf{x}$`, and `$\lambda$` is a weighting coefficient. 

![](/assets/bezier_optimization_1.png)
![](/assets/bezier_optimization_2.png)

Dump of all the derivatives:

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