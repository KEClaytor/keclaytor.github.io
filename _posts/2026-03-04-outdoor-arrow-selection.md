---
layout: post
title: Arrow Selection Math
subtitle: Do thin arrows really drift less in the wind?
author: K. E. Claytor, Claude.AI
tags: ["archery", "arrows", "claude.ai", "ai-experiments"]
hero_image: /assets/images/archery.jpg
---

I'm a recurve shooter preparing for the outdoor archery season again and it's time to do some arrow selection.
Do I want thick arrows or thin arrows?
Thick arrows may gain you more points (although unlikely with the large target).
And thin arrows are rumored to be less susceptible to wind effects.
But let's see --- let's do a bit of math!

## Assumptions

- Recurve bow, so we can approximate it as applying a force linear with draw length
- Full-draw length equals arrow length
- The arrow takes a parabolic trajectory from shooter to target
- Wind is blowing perpendicular to the line from shooter to target

## Variables

- $F_b$ = maximum force the bow applies at full draw
- $m_a$ = arrow mass
- $v_a$ = arrow velocity when traveling downrange
- $l_a$ = length of arrow
- $\phi_a$ = diameter of arrow
- $d$ = distance from shooter to target
- $t$ = time for the arrow to travel downrange
- $v_w$ = wind velocity
- $F_d$ = drag force from the wind

## An aside - Claude and Math

It's been awhile since I've asked one of the LLM's to do math for me.
The last time it didn't work out so well, but I thought I would try again with the new agent models.
Here's [the chat](https://claude.ai/share/391db119-0bea-4f73-ab02-dd3dd9374abf).

Basically, I prompted it with nearly exactly what was above (minus a couple of variables).
It identified all of the correct equations, did the correct substitutions, identified other variables it needed and came to the correct conclusion.
Basically what I had done on a napkin at the range last summer, but never got around to writing it up.

Anyway, here's the solution:

## Stage 1: Arrow Velocity

Since the bow force is linear with draw length, the work done on the arrow is the area under a triangle — half the maximum force times the full draw length $l_a$:

$$W = \frac{1}{2} F_b l_a$$

Setting this equal to the arrow's kinetic energy:

$$\frac{1}{2} F_b l_a = \frac{1}{2} m_a v_a^2$$

Solving for $v_a$:

$$v_a = \sqrt{\frac{F_b l_a}{m_a}}$$

## Stage 2: Time to Travel Downrange

Assuming the horizontal velocity component is roughly constant (flat trajectory), the time to reach the target is simply:

$$t = \frac{d}{v_a}$$

Note that since we're comparing arrows of the same mass, they'll have the same $v_a$ and hence the same $t$ — so any difference in wind drift must come purely from the drag force.

## Stage 3: Wind Displacement

The drag force on a cylindrical arrow from a crosswind is:

$$F_d = \frac{1}{2} \rho C_d A v_w^2$$

where the lateral area of the arrow is $A = l_a \cdot \phi_a$. The resulting lateral acceleration is $a_w = F_d / m_a$, giving a displacement of:

$$x = \frac{1}{2} a_w t^2 = \frac{F_d}{2 m_a} \left(\frac{d}{v_a}\right)^2$$

Substituting in $A = l_a \cdot \phi_a$:

$$x = \frac{\rho C_d l_a \phi_a v_w^2}{4 m_a} \left(\frac{d}{v_a}\right)^2$$

Now substituting $v_a = \sqrt{F_b l_a / m_a}$:

$$x = \frac{\rho C_d l_a \phi_a v_w^2}{4 m_a} \cdot \frac{d^2 m_a}{F_b l_a}$$

The $m_a$ and $l_a$ terms cancel, and we're left with:

$$x = \frac{\rho C_d \phi_a v_w^2 d^2}{4 F_b}$$

## Result & Takeaways

The wind displacement depends only on arrow **diameter** $\phi_a$ and bow **draw weight** $F_b$ — not on arrow mass or length. This gives us three clean takeaways:

- **Thinner arrows drift less** — displacement is directly proportional to $\phi_a$
- **Higher draw weight reduces drift** — $F_b$ in the denominator means stiffer bows shoot faster arrows that spend less time in the wind
- **Arrow mass and length don't matter** — perhaps counterintuitively, heavier arrows resist wind no better than lighter ones; the extra inertia is exactly offset by the slower velocity

So the rumor is true: all else being equal, go thin.
