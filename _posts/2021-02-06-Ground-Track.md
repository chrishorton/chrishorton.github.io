---
layout: post
title: Ground Track of Satelite with orbital elements
---
One of the most valuable characteristics of an artificial satellite orbiting earth is it’s ability to pass over large portions of the earth’s surface in a short amount of time. Satellites perform tasks such as global surveillance and mapping, GPS, television, weather, and more, that are vitally important to our modern lives. 

Determining the “ground track” or the path that the satellite traces as it orbits the earth is one of the most useful and important calculations that must be done when working with a satellite. 

When given a set of classical orbital elements, we can determine the parameters necessary to draw a basic ground track of our satellite. 

In future posts in this series I plan to go into determining these orbital elements from a single radar observation event, propagating an orbit, orbital transfers and other astrodynamics topics. 

Jumping right into it. If we have the following elements; semi-major axis, eccentricity, inclination, longitude of the ascending node, and argument of periapsis. These 5 elements are enough to describe an unperturbed orbit of any satellite, artificial or otherwise. Using a 6th element, we can determine where the satellite is on its orbital path.

The equation $$ P = 2\pi\sqrt\frac{a^3}{\mu} $$ represents the period of our satellite.

```python
import math
period = 2 * math.pi * math.sqrt(a^3/mu)
p_hours = period / 3600
```
This now gives us our period in seconds. For perspective, we will divide by 3600 to get the answer into the amount of hours our orbit will take. 

Now we can calculate our $$ \Delta n $$. This value will tell us where by the end of the sidereal day, 23 hours, 56 minutes, our satellite will be on the earth. The longitude of the ascending node gives us the longitude that we begin our sinusoidal wave from, and $$ \Delta n $$, tells us where to stop that line on the end of the first day. 

Where $$ P = \frac{\Delta n}{15\circ} $$ . So, $$ \Delta n = P \cdot 15\circ $$


The inclination value becomes the highest and lowest latitude that the satellite will see. A 0 degree inclination (and sufficient altitude above the earth’s surface) will be geostationary in nature, keeping pace with the earth rotation. In more practical terms to this exercise, it is the highest and lowest that we will draw our curve onto the earth’s 2d projection.

Let’s get our hands dirty with some code now. First, calculate our $$ \Delta n $$.
 
```python
delta_n = period * 15
```
