---
layout: post
title: Determining unique orbits in Python
category: physics
---

Determining an orbit from one radar observation was the chapter in Fundamentals of Astrodynamics by Bates, that I flipped to when I took the book off of my grandpa's shelf. I flipped through the introduction to the section, and was fascinated about the Doppler effect. Being experienced only in High School introductory physics at the time, I only knew about the Doppler shift in the context of red/blue and the sound a F1 car made as it zoomed past. Seeing that we could determine an orbit of a satelite all the way out in space with just these basic data points fascinated me, and I sat down and worked my way through the rest of the book. It's been about 3 years since then and I finally felt like I had the calculus and linear algebra skills to implement the projects in the back of the book. So over a weekend, I hacked on the code and algorithms to turn a radar observation into an orbit. 

I plan to make this the first of a series of 5 posts:

- Determination from a radar observation

- Determination from position vectors

- Determination from optical sightings

  - Possibly turn this into a project post with a raspberry pi, and a DSLR, determining the orbit of satelites that pass overhead.

- Finally, Improving the accuracy of these preliminary orbits.

For future projects, making it easier to solve the same problem with just 3 position vectors called the Gibbsian method or from optical sightings which is the ultimate goal. I plan to set up a raspberry pi and my DSLR to solve for the orbits of satellites that pass overhead.

# The problem


With an introduction out of the way, let's now focus on the problem at hand. There are a series of radar observation sites around the world, they can be found in a variety of locations; scientific observatories, military bases, airports, and [more][1].


## enough with the boring intro that everyone skips, on to the code!

The first step was processing and entering the data, for that I decided that a CSV was the best choice in order to process multiple data points, easier data input, and also use Pandas which handles CSV's with remarkable ease. For this project, Python's built in reader will do the job just fine. The code is long, and not very substantive, but it is in the Github repository if you are curious.

Data comes in many different types, and as a reciever of data it is my priority to make sure that all data is in a usable format, and specifically for this project, the proper units. 

So after multiplication and division by conversion factors, ```if``` statements, and string trimming, I arrive at perfectly clean usable data. 


### The math!

Let's define some constants first to use in our calculations

```python
# setting up an inertial reference frame
wE = .00089 # rad/second
omega = [0,0,wE] # earth rotates on the Z axis

# measurement of the earths axial tilt in 1970, important for ascending node calculations
J1970 = time.Time('J1970', scale='utc')

# To compute earths oblateness factor
eccentricity = 0.08182
L = 0
H = 6.378145 / 6371.0
equatorial_radius = 6378.145 # km
polar_radius = 6356.785 # km
```

Let's go through these one by one and explain. 

- ```wE``` describes the angular rotation that a point on the earth travels. The array ```omega``` represents the rotating coordinates of the earth. In a right hand coordinate system, the z coordinate that rotates. As shown in the image below.

![Right hand coordinate system of the earth](http://chrishorton.github.io/images/coordinate-system_FIGURE_1-300x275.jpg)










[1]: https://www.esa.int/Safety_Security/Space_Debris/Scanning_and_observing2


# References
- [Fundamentals of Astrodynamics (text)](https://www.amazon.com/Fundamentals-Astrodynamics-Dover-Aeronautical-Engineering/dp/0486600610)
- [European Space Academy (ESA) Space Debris Radar](https://www.esa.int/Safety_Security/Space_Debris/Scanning_and_observing2)
- [space observation radar TIRA (Tracking and Imaging Radar)](https://www.fhr.fraunhofer.de/en/the-institute/technical-equipment/Space-observation-radar-TIRA.html)
