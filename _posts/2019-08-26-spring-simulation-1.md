---
title: Spring Simulation 1
author: gstarr
categories:
  - Numerical Methods
  - Pyglet
date: 2019-08-27T02:20:00.000Z
thumbnail: /assets/uploads/spring-sim-1.png
---
First numerical methods post

### Overview:

This project originated at work while thinking about how to automatically determine a layout for electrical components (resistors, capacitors, microcontrollers, etc.) in **3D** space. Typically electronics devices are laid out in one or more planar layers of components. If the components instead could be placed at arbitrary locations and orientations in 3D space, maybe you could significantly reduce the volume of the device. In this post I will talk about my experiments with determining the component locations automatically. 

My first idea was model the circuit as having the connected components pulled together via springs. Each pad (lead / connection / terminal / etc.) of each component would be pulled towards the other pads on other components that it is connected to, thereby pulling and twisting the attached component so that it is nearby to other connected components. The rest-length of each spring could be set according to design rules, encouraging the components to not end up _too_ close together. I am hoping that ultimately it will settle down into a tightly packed volume which wouldn't be too difficult to route the traces through.

Overall I think this will be a cool opportunity to learn about numerical methods, simulating differential equations as well as python tools like [Pyglet](http://pyglet.org/) and [Trimesh](https://github.com/mikedh/trimesh). There are a ton of resources online and in books about the differential equation describing simple damped and undamped harmonic motion, so I will not get into that here. I will jump right into 2 massed connected by a spring with a rest length _l_.

### 2 particles connected by spring:

![](/assets/uploads/spring-sim-2.png)

The state vector for this system will have the positions and momentums of both particles. 

`$$ \textbf{s(t)} = 
\begin{bmatrix}
x_1(t) \\
y_1(t) \\
p_{x1}(t) \\
p_{y1}(t) \\
x_2(t) \\
y_2(t) \\
p_{x2}(t) \\
p_{y2}(t)
\end{bmatrix} $$`

The system can be described with a system of first order non-linear differential equations:

`$$\textbf{s}'(t) =
\begin{bmatrix}
\frac{1}{m_1} p_{x1}(t) \\
\frac{1}{m_1} p_{y1}(t) \\
-k (1 - \frac{1}{d}) (x_1(t) - x_2(t)) - b p_{x1}(t) \\
-k (1 - \frac{1}{d}) (y_1(t) - y_2(t)) - b p_{y1}(t) \\
\frac{1}{m_2} p_{x2}(t) \\
\frac{1}{m_2} p_{y2}(t) \\
-k (1 - \frac{1}{d}) (x_2(t) - x_1(t)) - b p_{x2}(t) \\
-k (1 - \frac{1}{d}) (y_2(t) - y_1(t)) - b p_{y2}(t)
\end{bmatrix}$$`

`$$\begin{align}
& m_i                             && \text{ : mass of particle } i \\
& \textbf{x}_i = (x_i, y_i)       && \text{ : x and y coordinates of particle } i \\ 
& \textbf{p}_i = (p_{xi}, p_{yi}) && \text{ : momentum in the x and y directions of particle } i \\
& k                               && \text{ : spring constant} \\
& b                               && \text{ : damping coefficient} \\
& d = \rVert \textbf{x}_1 - \textbf{x}_2 \lVert
\end{align}$$`

#### Euler Method:

To simulate the above differential equation I will use the Euler method, which is just a first-order forward approximation at each time step. There are better methods for simulating differential equations, but the Euler method is extremely simple and won't suffer from instability because of the damping on the system. 

The Euler method comes from Taylor's equation:

`$$y(t_{k+1}) = y(t_k) + hy'(t_k) + \frac{h^2}{2}y''(\xi_k) = y(t_k) + hf(t_k, y(t_k)) + \frac{h^2}{2}y''(\xi_k)$$`

where _h_ is the spacing between time steps and `$$\xi_k \in [t_k, t_{k+1}]$$`

It works the same for systems of equations, so my time step equations will look like: 

`$$\textbf{s}(t_{k+1}) = \textbf{s}(t_k) + h\textbf{s}'(t_k)$$`

#### Result:

![](/assets/uploads/ezgif.com-video-to-gif-3-.gif)

### Network of particles and springs:

#### Software Plan:

Inputs:
- a list of masses *n* long
- a list of spring constants *m* long
- a list of pairs (*i*, *j*), *m* long defining which masses are connected by the corresponding spring constant
- a list of rest lengths *m* long
- initial state vector *n* x 2 x *d*, where *d* is number of dimensions. The two counts the position and momentum / velocity of the mass

From this need to create the vector of first derivatives which will be 2 x *n* x *d* long. Each mass is indexed with *i* starting with zero going up to *n*. The *n* masses have 4 corresponding terms in the derivative vector (in 2d). The 4*i*th and (4*i* + 1)th term in the derivative vector are the positions and are just equal to the (4*i* + 2) and (4*i* + 3) terms respectively. The (4*i* + 2) and (4*i* + 3) terms are the momenta and these are a sum of the forces resulting from each connection. 


`$$p_{xi} = \sum_{(i, j)} -k_{ij} (1 - \frac{1}{d_{ij}}) (x_i - x_j) - b p_{xi}$$`

for each pair *(i, j)* which the *i*th mass is part of. 

I suppose each iteration I will initialize I will initialize the derivative vector to zeros, assign the position terms in two lines using array slicing, then I will iterate over the *m* connections each time step and add to the corresponding entries of the derivative vector. I need to think more about whether there is a way to minimize iteration or reuse values. I will try this in 2D with a few small networks before trying to make it 3D and adding boxes to represent components. 

### Notes:

* I made the animation using [pyglet](http://pyglet.org/), more specifically the open GL primitives
* I have no idea why the green line is solid and the circles are dashed
* saved pngs from every frame [Stackoverflow link](https://stackoverflow.com/questions/30315496/save-image-or-animation-in-pyglet)
* Used Blender to combine the pngs into an AVI
* [Gif Maker](https://ezgif.com/)
