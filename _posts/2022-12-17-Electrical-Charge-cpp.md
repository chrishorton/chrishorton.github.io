---
layout: post
title: A simple simulation for the interactions of charged particles
category: physics
---
#Introduction
This simulation is my basic attempt at an N-body simulation. There are no fancy algorithms to speed up the calculations, just a basic iterative simulation.

#Getting right to it...
I use sfml for graphics, a basic C++ library for displaying graphics to the screen. The 'Simulation' class will be the driver for the application, managing the 'Charge' class.

The main driver code is here. Momentum of the particle still needs to be considered, for now it's implementing a direct change in velocity (essentially assuming the particle has no momentum, and thereby a mass of 0)

```c++
for (int i = 0; i < this->charges.size(); ++i) {

    sf::CircleShape currCircle = charges[i]->getCircle();
    Charge *currCharge = charges[i];


    currCharge->calculateTotalForce(charges, i);

    if (currCharge->x + currCharge->radius > this->screenX || currCharge->x < 0) {
        currCharge->xVelocity = -currCharge->xVelocity;
    }
    if (currCharge->y + currCharge->radius > this->screenY || currCharge->y < 0) {
        currCharge->yVelocity = -currCharge->yVelocity;
    }

    currCharge->x += currCharge->xVelocity;
    currCharge->y += currCharge->yVelocity;

    currCircle.move(currCharge->xVelocity, currCharge->yVelocity);
    
    window.draw(currCircle);
}
```

Great. Now let's jump into the code of calculating the total force on the particle.

```c++
std::vector<double> Charge::calculateForce(Charge *c2) {
    double chargeProducts = (this->chargeSize * c2->chargeSize) / (4 * M_PI);

    double xDistance = (this->x - c2->x);
    double yDistance = (this->y - c2->y);

    double xResult = (xDistance)/abs(xDistance*xDistance*xDistance);
    double yResult = (yDistance)/abs(yDistance*yDistance*yDistance);

    // returns the directional unit vector multiplied by charge products scalar to give force vector on charge
    std::vector<double> result = {chargeProducts * xResult, chargeProducts * yResult};
    return result;
}
```

This function takes in an argument, a charge to calculate the absolute distance, and then applies the inverse square law to that distance and finally reaches the force between the two. Through each iteration the individual Charge's velocity vector gets 'pulled' until it reaches a final dx/dy value, and the driver function shown above takes a timestep according to that dx/dy.
