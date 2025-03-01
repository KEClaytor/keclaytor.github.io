---
layout: post
title: Single Molecule Tracker
author: K. E. Claytor
tags: programming, optics
---

SMS VIS is a program developed to track and identify nanocars using a optical scanning microscope.

## Download and Installation

1. MCR Installer - The MATLAB component runtime that must be installed before running the program installer.
2. [SMS_Tracker_pkg.exe](/assets/exe/SMS_Tracker_pkg.exe) - The installer for the tracking program.
3. [Sample Data](/assets/exe/SampleData.zip) - Some sample data to get you started.

## Documentation

The [manual and documentation](/assets/docs/SMS_VIS_Manual.doc) is available for download.
You can also [download the poster](/assets/docs/Claytor_RQI.pdf).

![Main application screen](/assets/images/sms/mainscreen.jpg)

From the main screen you can load up the individual images for analysis.
Once images have been loaded, they are analized for intensity, allowing the user to set cut-offs for target molecules against random noise.
This screen allows for an overview of the key points of the data.
While the data is being processed, it shows the actual image files with an overlay of molecules that will be kept for further analysis.

![Search radius plot](/assets/images/sms/sr.jpg)

This is the search radius plot.
This proved to be one of the more useful plots.
It plots points associated as a function of search radius.
If there were immobile particles, there would be a large peak within a few pixels of zero, however, for more mobile samples the peak would shift out, and the distribution would change.
See the poster for more information.

![Radius step per frame](/assets/images/sms/radius.jpg)

The histogram of distance the particles have moved from frame to frame.
This is an indicator of movement - bead samples (no movement) have a much higher peak around zero, while mobile samples show a distribution that tends off of zero.

![X-Y step per frame](/assets/images/sms/xystep.jpg)

This plot is similar, however it is in the x and y directions.
This allows one to perform analysis for sample or image drift.
In this image, there is no drift as the mean lies on the origin.

![Randomly traveling trajectory](/assets/images/sms/random.jpg)

An image of a randomly traveling particle, one can see that it stays mainly in a 1-2 pixel radius, and has no preferred direction.

![Possible nanocar trajectory](/assets/images/sms/straight.jpg)

This trajectory is from a molecule that could be a nanocar - it has a larger movement magnitude (a few pixels) and is oriented along a preferential direction.

Revision history;
- Version 1.0 - Working molecule identification, partially functional diffusion calculator
