---
layout: post
title: Lego Nonlinear Oscillator
author: K. E. Claytor
tags: physics, lego, project, tinkering
---

[Read the final report.](/assets/docs/Claytor_NonlinearOscillator.pdf)

One of the coolest things that I like about physics is how accessible it can be with some fairly simple experiments.
Here I've built a driven pendulum out of Legos.
The drive arm is connected to the oscillator via a rubber band.
The oscillator arm itself is damped through the friction of the LEGOs.

Despite its relative simplicity, we can observe some fairly complicated behavior, such as bifurcation (different behavior for the same conditions).
For small displacements of the drive arm, the rubber band applies a linear force and the oscillator arm follows it exactly.
But when the drive amplitude increases the rubber band provides a nonlinear force and more complicated behavior ensues.
First, it goes through resonance, where the amplitude of the oscillator arm increases with the frequency of the drive arm as the frequency increases up to resonance.
After this point, although the frequency of the drive arm continues to increase the oscillator arm amplitude does not.
Additionally, it exhbiits bifurcation:
At a frequency near resonance the amplitude of the driven oscillator can be different depending if you have reached that point by;
increasing or decreasing the frequency.

Here's a video of it running at a specific frequency.

<iframe
  width="600"
  height="400"
  src="http://www.youtube.com/embed/c9dRzkTv5jc"
  frameborder="0"
  allowfullscreen>
</iframe>

In the meantime you can download the [analysis code](mfiles/nonlinear/VideoAnalysis.m).
And a pair of Mathematica analysis files ([analysis](mfiles/nonlinear/Analysis.nb) and
[Simulations](mfiles/nonlinear/ProjectSimulations.nb)).

Here are some processing images so you can see how it is done (thrsholding and following the green dot).

![Isolating the region of interest](/assets/images/nonlinear/processing1.jpg)

We isolate the region of interest (ROI).

![Using just the green channel](/assets/images/nonlinear/processing2.jpg)

Just the green channel of the ROI (I later learned that [RGB -0.5*R -0.5*B] is a better way of obtaining green color information).

![Adjusting the dynamic range](/assets/images/nonlinear/processing3.jpg)

Adjusting for full dynamic range.

![Thresholding isolates the marker](/assets/images/nonlinear/processing4.jpg)

A threshold allows us to isolate the marker.

![Identified the location of the marker](/assets/images/nonlinear/processing5.jpg)

Finally we have the location of the markers in all the frames.

![Damped data](/assets/images/nonlinear/dampeddata.jpg)

Here's an example of the quality of the data you can extract from this simple setup.
