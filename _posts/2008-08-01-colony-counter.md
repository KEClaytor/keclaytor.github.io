---
layout: post
title: Colony counter
author: K. E. Claytor
tags: development, programming, biophysics
---

Development of an automated colony counter using image processing techniques.

## Introduction and Download

This is a program that I wrote for my REU in the summer of 2008 at the [University of Illinois at Urbana Champaign](http://illinois.edu/) for [Prof. Ido Golding](http://www.physics.uiuc.edu/people/golding/).
It interfaces with a webcam attached to the computer, uses it to capture an image of a bacterial plate and counts the number of colonies on the dish.
You will need:
- MCR Installer - The MATLAB component runtime that must be installed <b>before</b> running the program installer.
- [Counter_pkg](/assets/exe/Counter_pkg.exe) - Standalone executable (windows only)
- [Matlab code](/assets/mfiles/colony.zip) - .zip of m file source code

## Installation

If the Matlab Component Runtime has not been installed download and install the Matlab runtime file.
Then download the counter_pkg.exe program and run it.
This will create counter.exe which is the standalone application.
Alternatively you can download the Matlab files in a .zip package and run from the Matlab command prompt.

## Documentation and References

[Final Report](/assets/docs/Claytor_Counter_Paper_2008.pdf) - This documents the successes and failures of the counter software and hardware.
It also contains all references used.

[Manual and Documentation](/assets/docs/Counter_Manual.pdf) - Documentation for the software as well as the hardware used in this project.

[Cell Profiler](http://www.cellprofiler.org/) - A useful (and free) versatile image processing tool for Biological image analysis.
It has a sharp learning code, however, you may find it useful.
The code here was developed independently of this project.


## Screenshots

![The initial program window](/assets/images/colony/cameraexpt.jpg)

The main program that may be used to capture and analyze images.

![Configuration of camera properties and image acquisition](/assets/images/colony/cameraaq.jpg)

The program that interfaces to the camera and acquires the image.

![Processing an image and histogram of results](/assets/images/colony/camerastats.jpg)

Another program that can be launched for applying cutoffs to the parameters and calculating intensity values.

![Example identification of bacterial colonies](/assets/images/colony/counted.jpg)

The output image of the program. Also output is an Excel file containing most of the parameters of each spot (size, eccentricity, etc.).

![The hardware setup](/assets/images/colony/setup.jpg)

A picture of the hardware setup showing the light box with webcam attachment, the source of back lighting and the removable diffuser.

![Deviation between automated and hand counting](/assets/images/colony/deviation1.jpg)

![More deviation between automated and hand counting](/assets/images/colony/deviation2.jpg)

Deviation between automated counting and hand counting for several samples.
