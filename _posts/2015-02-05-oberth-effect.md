---
title: The Oberth effect
description: Or why speed matters
date: February 5, 2015
categories: [space]
image: images/blog/Orbit.jpg
layout: post
toc: false
comments: true
hide: false
search_exclude: false
---




 These notes are about astronautics, a field in which i learn everyday and i might say utterly stupid things!.
 

The Oberth effect is used in astronautics to choose the best time to fire the power engines to modify orbit most efficiently. Basically it says that the faster you go, the more change in kynetic energy (or change of speed, aka Delt-V) you are going to get from your fuel. This is more useful for high power rocket engines (like chemically propelled rockets) than for low thrust engines (e.g. ion drives) as the former concentrate their energy over a lower amount of time, spending it all
at the highest speed.

In more mathematical terms, let's consider the definition of Kynetic Energy:
$$E\_k = \frac{1}{2}mv^2$$
Where \(E\_k\) is the kynetic energy, \(m\) the mass of the object, and \(v\) speed. Hence,
$$\Delta(E\_k) = E\_{k1} - E\_{k0} = \frac{1}{2}m(v\_0 + \Delta v)^2 - \frac{1}{2}mv\_0^2$$
Which leads us to:
$$\Delta(E\_k) = mv\_0(\Delta v) + \frac{m(\Delta v)^2}{2}$$
So at constant \(\Delta v\), the kynetic energy gain is proportional to the speed at the moment when the engine was fired.

And as we know from Kepler's laws, the apoapsis of an orbit trajectory is the point where the distance and the speed are the greatest. So that's the point with the most \(\Delta v\) efficiency.

In the end the Oberth effect is an astronautics basic, allowing fuel-wise cheaper orbit transfers and trajectory modifications in interplanetary travels. It's the fundamentals on which bi-elliptic transfers and gravitational assists techniques are based. [Hermann Oberth](http://en.wikipedia.org/wiki/Hermann_Oberth) was a German scientist who worked on Nazi rocketry projects (among which the V2 rocket weapon), he also worked for rocketry projects all over the world, as for example for NASA with his former student Wherher Von Braun. He is considered as one of the fathers of rocketry and astronautics.

---

Sources:

1. [Wikipedia](http://en.wikipedia.org/wiki/Oberth_effect)