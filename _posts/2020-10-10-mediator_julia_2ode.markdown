---
layout: post
title:  "2nd ODE with Julia"
date:   2020-10-10 14:34:26
categories: regular Julia
tags: regular Julia
image: /assets/article_images/2020-10-10-mediator_julia_2ode/night-track.JPG
image2: /assets/article_images/2020-10-10-mediator_julia_2ode/night-track-mobile.JPG
---
Some months ago, an acquaintance of mine asked me whether in **Julia** it would be possible to solve efficiently second order differential equations (ODE). My acquaintance was trying to solve efficiently ODEs in **Python**, using the standard approach, *i.e.* solving two first ODE. This person was obtaining close, but strange results. The prime reason was its system of equations was coupled, therefore, in order to solve the second ODEs as multiple first ODEs, first it undoubtedly has to uncouple the equations.

I was confident that using software like maple or mathematica, one could uncouple the equations. Highly likely the same could be naturally done in mere python using *sympy*. Nevertheless, I was intrigued whether it could be done in **Julia**. It was something that I never attempted, thus, I undoubtedly wanted to confirm it myself. Short answer, **YES**, it is possible. The most delightful part is that; I do not need to uncouple the complex equations. The standard package [OrdinaryDiffEq][1ODE] fundamentally does all the heavy lifting for you.

The second ODE to solve are found within [this academic paper][paper], namely the system of equations 2.1 and 2.2. At the moment of writing this "*article*", **Julia 1.5.2** the latest available version. Here I shall only highlight the most substantial steps as local variables and tuning of the possible plots depends on one taste.

1. Naturally open **Julia** and properly load the needed packages. In my specific case, the bare necessary.
{% highlight julia %}
using OrdinaryDiffEq, Plots
{% endhighlight %}

2. Appropriately include the determined constants that you need to use. I highly recommend intentionally using `const` before the constant variants, if you are sure it will not change during the calculation. In principle this should help to speed up the complex calculation.
{% highlight julia %}
const m_1 = 1.0
const g = 9.81
tfinal = 130.0
{% endhighlight %}

3. This is a good point to promptly write the initial conditions of the system of the equations.
{% highlight julia %}
du0 = [0.0, 0.0, 0.0]
u0 = [pi/2.4, pi/2.4, 0.0 ]
{% endhighlight %}

4. Put the **Julia** function with the system of equations to solve. Here `p` is ordinarily used to promptly put variables that you might need to change in the equation, for example, alter the value of an angle.
{% highlight julia %}
function synch_pendul(ddu, du, u, p, t)
    ddu[1] =  -(m_1*ddu[3]*l*cos(u[1]) + c_vdp*du[1]*(1-zeta*(u[1]^2)) +
              m_1*g*l*sin(u[1]))/(m_1*l^2)
    ddu[2] =  -(m_2*ddu[3]*l*cos(u[2]) + c_vdp*du[2]*(1-zeta*(u[2]^2)) +
              m_2*g*l*sin(u[2]))/(m_2*l^2)
    ddu[3] =  -(c_x*du[3] + k_x*u[3] + m_1*l*(ddu[1]*cos(u[1])
                                           - (du[1]^2)*sin(u[1])) +
              m_2*l*(ddu[2]*cos(u[2])-(du[2]^2)*sin(u[2])))/(M+m_1+m_2)
{% endhighlight %}

5. Pass the solver. Here one indicates that the eqation to solve is a second ODE with certain initial parameters.
5. Pass the solver. Here, one indicates that the equation to solve is a second ODE with certain initial parameters.
{% highlight julia %}
prob = SecondOrderODEProblem(synch_pendul, du0, u0, tspan)
{% endhighlight %}

6. Solve the second ODE. In this case I am using a Runge-Kutta-Nyström integrator, but one could experience better results using other. Feel free to experiment with it.
{% highlight julia %}
sol = solve(prob, DPRKN6(), reltol=1e-12, abstol=1e-12, maxiters=1e10)
{% endhighlight %}

Conclusively, one carefully plots the results. Peculiarly, I like using **latex**-**pfg** unique style, if you like it too, then you need to install correctly the package `PGFPlotsX`, because that is the backend used by `Plots`.


This is the result. Just one of the numerous plots that could be created.

![Time histories of almost-complete synchronization][plot]


Hope this helps you. 

 
[paper]:      http://yadda.icm.edu.pl/yadda/element/bwmeta1.element.baztech-article-BWM6-0029-0023/c/httpwww_ptmts_org_pl2012-3-kapitaniak-in.pdf
[1ODE]:       https://github.com/SciML/OrdinaryDiffEq.jl
[plot]:        /assets/article_images/2020-10-10-mediator_julia_2ode/Plot-1.png "Plot 1.b from the original paper"
