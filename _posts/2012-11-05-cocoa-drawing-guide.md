--- 
layout: post
title: Cocoa Drawing Guide
date: 2012-11-05 14:46:24
categories:
    - 
tags:
    -
---


In fact, most Cocoa classes use Quartz extensively in their implementations to do the actual drawing.


##The Painter’s Model

Like Quartz, Cocoa drawing uses the painter’s model for imaging. In the painter’s model, each successive drawing operation applies a layer of “paint” to an output “canvas.”


##The Drawing Environment

The drawing environment encompasses the digital canvas and graphics settings that determine the final look of your content. The canvas determines where your content is drawn, while the graphics settings control every aspect of drawing, including the size, color, quality, and orientation of your content.


###The Graphics Context

You can think of a graphics context as a **drawing destination**. A graphics context encapsulates all of the information needed to draw to an underlying canvas, including the current drawing attributes and a device-specific representation of the digital paint on the canvas. 


NSGraphicsContext, represent the following drawing destinations:

* Windows (and their views)
* Images (including bitmaps of all kinds)
* Printers
* Files (PDF, EPS)
* OpenGL surfaces

By far, the most common drawing destination is your application's windows, and by extension its views. Cocoa maintains graphics context objects on a per-window, per-thread basis for your application.


###The Graphics State

In addition to managing the drawing destination, an NSGraphicsContext object also manages the graphics state associated with the current drawing destination. The graphics state consists of attributes that affect the way content is drawn, such as the line width, stroke color, and fill color. 

The current graphics state can be saved on a stack that is maintained by the current graphics context object. Any subsequent changes to the graphics state can then be undone quickly by simply restoring the previous graphics state.


###The Coordinate System

The coordinate system supported by Cocoa is identical to the one used in Quartz-based applications. All coordinates are specified using floating-point values instead of integers. Your code draws in the **user coordinate space**. Your drawing commands are converted to the **device coordinate space** where they are then rendered to the target device.

The user coordinate space uses a fixed **scale** for coordinate values. In this coordinate space, one unit is effectively equal to 1/72 of an inch. 

As the name implies, the device coordinate space refers to the native coordinate space used by the target device, usually a monitor or printer. 

Unlike the user coordinate space, whose units are effectively fixed, the units of the device coordinate space are tied to the **resolution of the target device**, which can vary. Cocoa handles the conversion of coordinates from user space to the device space **automatically during rendering**, so you rarely need to work with device coordinates directly.


###Transforms

A transform is a mathematical construct used to manipulate coordinates in two-dimensional space. 

NSAffineTransform implements the transform behavior:

* Translation
* Scaling
* Rotation


###Color and Color Spaces

Color is an important part of drawing. Before drawing any element, you must choose the colors to use when rendering that element.

Transparency is another factor that influences the appearance of colors.


##Basic Drawing Elements

