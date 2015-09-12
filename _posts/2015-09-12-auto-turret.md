---
layout: post
title:  "Programming an Auto-Aiming Turret"
date:   2015-09-12 2:53:00
tags: programming math
---

**Note:** This post isn't finished yet, but I want to see how it looks published.

Whether it's for a space shooting game or if you've been tasked with defending a secure location, it's possible you need to learn how to program a turret that can shoot a moving target. If that's the case, and you're somehow stuck in the math, this short article is here to help!

First let's visualize the situation. We'll have a turret that can rotate in any direction to hit its target, which is moving with a constant arbitrary velocity.

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

You may possibly be inclined to use polar coordinates to write the distances and directions. While the problem can be solved using angles and trigonometric functions, I find it simpler to use cartesian coordinates. Not to mention trigonometric functions are relatively slow to compute.

For simplicity, we will assume the turret is located at the origin and does not move. It is likely this assumption does not hold for your situation. In that case you can simply subtract the turret's position from the target's position, and likewise subtract the turret's velocity from the target's velocity. That is, we only care about the _relative_ position and velocity of the target with respect to the turret.

With that out of the way, let's write down what we know.

The target's position as a function of time is:

x = v<sub>x</sub> t + x<sub>0</sub>  
y = v<sub>y</sub> t + y<sub>0</sub>
{:.text-center}

We can calculate the target's position at any time by knowing its initial position and velocity (which we know).

The bullet we will shoot out of our turret will follow the same equations we wrote above for the target. However, we do not know the v<sub>x</sub> or v<sub>y</sub> of our bullet, since those depend on which direction we fire our turret. _However_, what we do know is the absolute velocity (or speed) of our bullet, which will be denoted \|v<sub>bullet</sub>\|.

Ahah! Then, even though we cannot yet say where our bullet will be at a given time, we _can_ say **how far away** it will be.

The bullet's trajectory/constraint is:

x<sup>2</sup> + y<sup>2</sup> = r<sup>2</sup>
{:.text-center}

In order for the bullet to hit the target, we need that

x<sub>target</sub> = x<sub>bullet</sub>  
y<sub>target</sub> = y<sub>bullet</sub>
{:.text-center}

The combined equation is:

(v<sub>x</sub> t + x<sub>0</sub>)<sup>2</sup> + (v<sub>y</sub> t + y<sub>0</sub>)<sup>2</sup> = (\|v<sub>bullet</sub>\| t)<sup>2</sup>
{:.text-center}

Which can be rearranged into:

(v<sub>x</sub><sup>2</sup> + v<sub>y</sub><sup>2</sup> - v<sub>bullet</sub><sup>2</sup>) t<sup>2</sup> + 2(v<sub>x</sub> x<sub>0</sub> + v<sub>y</sub> y<sub>0</sub>) t + x<sup>2</sup> + y<sup>2</sup> = 0
{:.text-center}

Which, if you look at it long enough, has the form:

at<sup>2</sup> + bt + c = 0
{:.text-center}

You likely learned how to solve this in high school using the quadratic equation:

t = (-b +- sqrt(b<sup>2</sup> - 4 a c)) / (2 a)
{:.text-center}

{% highlight js %}
function aim(thing) {
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
}
{% endhighlight %}
