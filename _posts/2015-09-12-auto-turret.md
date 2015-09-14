---
layout: post
title:  "Programming an Auto-Aiming Turret"
date:   2015-09-12 2:53:00
tags: programming math
---

Whether it's for a space shooting game or if you've been tasked with defending a secure location, it's possible you need to learn how to program a turret that can shoot a moving target. If that's the case, and you're somehow stuck in the math, this short article is here to help!

First let's visualize the situation. We'll have a turret that can rotate in any direction to shoot the bullet to hit its target, which is moving with a constant arbitrary velocity.

<svg width="300" height="300" class="figure-center">
  <path stroke="#333" stroke-width="1" fill="transparent"
    d="M200 100 l-50 -50 l2 5 m-2 -5 l5 2"
  />
  <circle cx="200" cy="100" r="5" stroke="transparent" fill="red" />
  <path stroke="blue" stroke-width="2" fill="transparent"
    d="M100 200 l0 -14"
  />
  <rect x="95" y="195" width="10" height="10" fill="blue" />
</svg>

Our Turret (blue square) and its moving target (red circle).
{:.caption}

You may possibly be inclined to use polar coordinates to write the distances and directions. While the problem can be solved using angles and trigonometric functions, I find it simpler to use cartesian coordinates. Not to mention trigonometric functions are usually slower to compute.

For simplicity, we will assume the turret is located at the origin and does not move. It is likely this assumption does not hold for your situation. In that case you can simply subtract the turret's position from the target's position, and likewise subtract the turret's velocity from the target's velocity. That is, we only care about the _relative_ position and velocity of the target with respect to the turret.

With that out of the way, let's write down what we know.

The target's position as a function of time is:

x<sub>target</sub> = v<sub>x,target</sub> t + x<sub>0,target</sub>  
y<sub>target</sub> = v<sub>y,target</sub> t + y<sub>0,target</sub>
{:.text-center}
The target's path
{:.caption}

We can calculate the target's position at any time by knowing its initial position (x<sub>0</sub>, y<sub>0</sub>) and velocity (v<sub>x</sub>, v<sub>y</sub>), both of which we should already know.

The bullet we will shoot out of our turret will follow the same equations we wrote above for the target. However, we do not know the velocity (v<sub>x</sub>, v<sub>y</sub>) of our bullet, since those depend on which direction we fire our turret. _However_, what we do know is the absolute velocity (or speed) of our bullet, which will be denoted \|v<sub>bullet</sub>\|.

Ahah! Then, even though we cannot yet say which direction our bullet will go, we _can_ say **how far away** it will be at a given time.

The distance, *r*, of the bullet away from the turret is

r<sub>bullet</sub> = \|v<sub>bullet</sub>\| t
{:.text-center}

Additionally, we can write the distance of the bullet from the turret in terms of its position, x and y. Simply recall the formula for a circle:

r<sup>2</sup> = x<sup>2</sup> + y<sup>2</sup>
{:.text-center}

We can put these two formulas together to get an equation for the bullet's distance from the turret in terms of its position (x, y):

(\|v<sub>bullet</sub>\| t)<sup>2</sup> = x<sub>bullet</sub><sup>2</sup> + y<sub>bullet</sub><sup>2</sup>
{:.text-center}
A constraint for the bullet's path. Nice!
{:.caption}

Now we have equations to describe the path of the target and the path of the bullet. Let's write them below:

x<sub>target</sub> = v<sub>x,target</sub> t + x<sub>0,target</sub>  
y<sub>target</sub> = v<sub>y,target</sub> t + y<sub>0,target</sub>  
x<sub>bullet</sub><sup>2</sup> + y<sub>bullet</sub><sup>2</sup> = (\|v<sub>bullet</sub>\| t)<sup>2</sup>
{:.text-center}

In order for the bullet to hit the target, we need to find a solution to these equations in which the target's position and the bullet's position are the same. In math terms, we want the following constraint:

x<sub>target</sub> = x<sub>bullet</sub>  
y<sub>target</sub> = y<sub>bullet</sub>
{:.text-center}

That means our system of equations gets simplified to

x = v<sub>x,target</sub> t + x<sub>0,target</sub>  
y = v<sub>y,target</sub> t + y<sub>0,target</sub>  
x<sup>2</sup> + y<sup>2</sup> = (\|v<sub>bullet</sub>\| t)<sup>2</sup>
{:.text-center}
Our system of equations
{:.caption}

We have 3 equations and 3 unknowns (*x*, *y*, and *t*). Looks like we should be able to solve it!

If we substitute *x* and *y* into the bottom equation we'll only have to solve for *t*, and then we can use *t* to find *x* and *y*. Let's try that!

The combined equation is:

(v<sub>x</sub> t + x<sub>0</sub>)<sup>2</sup> + (v<sub>y</sub> t + y<sub>0</sub>)<sup>2</sup> = (\|v<sub>bullet</sub>\| t)<sup>2</sup>
{:.text-center}

Not so pretty, but we can rearrange it into:

(v<sub>x</sub><sup>2</sup> + v<sub>y</sub><sup>2</sup> - v<sub>bullet</sub><sup>2</sup>) t<sup>2</sup> + 2(v<sub>x</sub> x<sub>0</sub> + v<sub>y</sub> y<sub>0</sub>) t + x<sup>2</sup> + y<sup>2</sup> = 0
{:.text-center}

Which, if you look at it long enough, has the form:

at<sup>2</sup> + bt + c = 0
{:.text-center}

Where

a = v<sub>x</sub><sup>2</sup> + v<sub>y</sub><sup>2</sup> - v<sub>bullet</sub><sup>2</sup>  
b = 2(v<sub>x</sub> x<sub>0</sub> + v<sub>y</sub> y<sub>0</sub>)  
c = x<sup>2</sup> + y<sup>2</sup>
{:.text-center}

You likely learned how to solve this in high school using the quadratic equation:

t = (-b +- sqrt(b<sup>2</sup> - 4 a c)) / (2 a)
{:.text-center}

You might also remember that a quadratic function can have 0, 1, or 2 solutions. In other words, it might be impossible to hit our target, and we might be able to shoot in two different directions and still hit it. Unfortunately, if we can't get a value for *t* (or if the solution is imaginary) we're out of luck!

"How can we have 0 or 2 solutions?" you might ask. If you didn't ask that, you can skip the rest of this paragraph; otherwise read along. Well, if the target is flying away from our turret, and it travels faster than our bullets, then, unless we can bend space-time to our will, the bullet won't be able to catch up and there won't be a valid solution to our problem. We can have 2 solutions if our target travels faster than our bullet AND our target is moving closer to the turret: either 1) we hit the target as it comes towards us (the more likely choice), or 2) we shoot the bullet away from where the target is, but where the target will catch up with it and hit it (the less likely, and more silly, but still possible choice).

If we get two values for *t*, we likely want the smaller one since we'd like to hit our target as soon as possible. However, *t* must not be negative, or else it would mean our bullet would need to travel back in time in order to hit.

Once we have our *t*, though, the rest is a piece of cake. We just plug in *t* to the first two equations to obtain the values of *x* and *y* where the hit occurs, we aim the turret in that direction, and then we fire!

It's over! Below is a Javascript implementation of the steps described.

{% highlight js %}
/*
 * x0 - target's initial x position
 * y0 - target's initial y position
 * vx - target's initial x velocity
 * vy - target's initial y velocity
 * vb - bullet's speed
 */
function shootTarget(x0, y0, vx, vy, vb) {
  // solve quadratic equation
  var a = vx*vx + vy*vy - vb*vb;
  var b = 2 * (x0*vx + y0*vy);
  var c = x0*x0 + y0*y0;
  var determinant = b*b - 4*a*c;

  // if the determinant is negative, the solution is imaginary
  // and we cannot hit our target
  if (determinant < 0) return;

  var t1 = (-b + Math.sqrt(determinant)) / (2*a);
  var t2 = (-b - Math.sqrt(determinant)) / (2*a);

  // Get the smallest t, where t >= 0
  // if t < 0 then the solution is in the past
  var t;
  if (t1 >= 0 && t2 >= 0) {
    t = Math.min(t1, t2);
  } else if (t1 >= 0) {
    t = t1;
  } else if (t2 >= 0) {
    t = t2;
  } else {
    return;
  }

  // find where the target will be at time t
  var x_t = vx*t + x0;
  var y_t = vy*t + y0;

  // now we know which direction to aim!
  // we can specify it as an angle = atan2(y, x), or as a unit vector
  var magnitude = Math.sqrt(x_t*x_t + y_t*y_t);
  var ux = x_t / magnitude;
  var uy = y_t / magnitude;
  // fire the bullet in the calculated direction with the given speed
  fireBullet(ux*vb, uy*vb);
}
{% endhighlight %}
