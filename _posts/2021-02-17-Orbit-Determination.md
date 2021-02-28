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
equatorial_radius = 6378.145 # km
polar_radius = 6356.785 # km
```

Let's go through these one by one and explain. 

- ```wE``` describes the angular rotation that a point on the earth travels. The array ```omega``` represents the rotating coordinates of the earth. In a right hand coordinate system, the z coordinate that rotates. As shown in the image below.

![Right hand coordinate system of the earth](http://chrishorton.github.io/images/coordinate-system_FIGURE_1-300x275.jpg)

Next we have the time epoch. As the earth [precesses](https://en.wikipedia.org/wiki/Axial_precession#Precession_of_the_equinoxes) on it's axis, the measurements for celestial coordinate systems change. Namely; right ascension, and the vernal equinox direction. Defining a standar for the axial 'tilt' of the earth is necessary for precise measurements and scientific reproducibility.

Finally, we have a couple measurements of the earth's elliptical properties. As the earth rotates around it's Z axis, there exists a certain oblateness. Just as your arms naturally want to spread out as you spin, the angular momentum at the equator is sufficient to pull the earth outwards also. We can approximate this 'stretch' with an ellipse fairly accurately. Taking the equatorial radius, the distance around the equator, and then the polar radius, the distance around the poles, we arrive at an eccentricity value using basic ellipse formulas.

Next, let's get down and dirty and determine a topocentric state vector, pointing from the radar station to the tracked object, and velocity from the object, pointing in the direction of travel. Taking all of the radar data, we can determine these vectors fairly simply with the trigonometry of this diagram.

![diagram](https://raw.githubusercontent.com/chrishorton/chrishorton.github.io/master/images/topocentric_coordinat_system.png)

$$ \rho_{S} = - \rho\ cos(El)\ cos(Az) $$

$$ \rho_{E} = \rho\ cos(El)\ sin(Az) $$

$$ \rho_{Z} = \rho\ sin(El) $$

and the velocity relative to the radar observation site:

$$ \dot\rho_{S} = -\dot\rho\ cos(El)\ cos(Az)\ + \rho\ sin(El)\ \dot El\ cos(Az)\ + \rho\ cos(El)\ sin(Az)\ \dot Az $$

$$ \dot\rho_{E} = \dot\rho\ cos(El)\ sin(Az)\ - \rho\ sin(El)\ \dot El\ sin(Az)\ + \rho\ cos(El)\ cos(Az)\ \dot Az $$

$$ \dot\rho_{Z} = \dot\rho\sin(El) + \rho\ cos(El)\dot El $$

```python
    rho_S = -1 * range * math.cos(elevation) * math.cos(azimuth)
    rho_E = range * math.cos(elevation) * math.sin(azimuth)
    rho_Z = range * math.sin(elevation)

    rho = np.array([rho_S, rho_E, rho_Z])

    rho_dot_S = -1 * range_rate * math.cos(elevation) * math.cos(azimuth) + range * math.sin(elevation) * elevation_rate * math.cos(azimuth) + range * math.cos(elevation) * math.sin(azimuth) * azimuth_rate
    rho_dot_E = range_rate * math.cos(elevation) * math.sin(azimuth) - range * math.sin(elevation) * elevation_rate * math.sin(azimuth) + range * math.cos(elevation) * math.cos(azimuth) * azimuth_rate
    rho_dot_Z = range_rate * math.sin(elevation) + range * math.cos(elevation) * elevation_rate
    
    rho_dot = np.array([rho_dot_S, rho_dot_E, rho_dot_Z])

    return rho, rho_dot
```

[1]: https://www.esa.int/Safety_Security/Space_Debris/Scanning_and_observing2

# References

- [Fundamentals of Astrodynamics (text)](https://www.amazon.com/Fundamentals-Astrodynamics-Dover-Aeronautical-Engineering/dp/0486600610)
- [European Space Academy (ESA) Space Debris Radar](https://www.esa.int/Safety_Security/Space_Debris/Scanning_and_observing2)
- [space observation radar TIRA (Tracking and Imaging Radar)](https://www.fhr.fraunhofer.de/en/the-institute/technical-equipment/Space-observation-radar-TIRA.html)
- [Axial Precession Youtube](https://www.youtube.com/watch?v=ty9QSiVC2g0)
