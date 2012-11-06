--- 
layout: post
title: Coordinate Systems and Transforms
date: 2012-11-05 16:35:08
categories:
    - 
tags:
    -
---

Coordinate spaces simplify the drawing code required to create complex interfaces.

In a standard Mac app, the window represents the **base coordinate system** for drawing, and all content must eventually be specified in that coordinate space when it is sent to the window server.

Each Cocoa view you add to a window maintains its own local coordinate system for drawing. Rather than convert coordinate values to window coordinates, you simply draw using the local coordinate system, ignoring any changes to the position of the view.

Even with the presence of local coordinate spaces, it is often necessary to change the coordinate space temporarily to affect certain behaviors. Changing the coordinate space is done using mathematical transformations (also known as transforms).


##Coordinate Systems Basics

**Cocoa** and **Quartz** use the same base coordinate system model. 


###Local Coordinate Systems

Cocoa uses a **Cartesian coordinate system** as its basic model for specifying coordinates. The origin in this system is located in the lower-left corner of the current drawing space, with positive values extending along the axes up and to the right of the origin point.

If you were forced to draw all your content in screen coordinates—the coordinate system whose origin is located at the lower-left corner of the computer’s primary screen—your code would be quite complex. 

To simplify things, Cocoa sets up a **local coordinate system** whose origin is equal to the origin of the window or view that is about to draw. Subsequent drawing calls inside the window or view take place relative to this local coordinate system. Once the code finishes drawing, Cocoa and the underlying graphics system convert coordinates in the local coordinates back to screen coordinates so that the content can be composited with content from other applications and sent to the graphics hardware.

Mapping from screen coordinates to local window or view coordinates takes place in the current transformation matrix (CTM) of the Cocoa graphics context object.


###Points Versus Pixels

The drawing system in OS X is based on a PDF drawing model, which is a **vector-based drawing model**. 

Compared to a raster-based drawing model, where drawing commands operate on individual pixels, drawing commands in OS X are specified using **a fixed-scale drawing space**, known as the **user coordinate space**.


####User Space

The user coordinate space in Cocoa is the environment you use for all your drawing commands. It represents **a fixed scale coordinate space**, which means that the drawing commands you issue in this space result in graphics whose size is consistent regardless of the resolution of the underlying device.

Units in the user space are based on the **printer's point**, which was used in the publishing industry to measure the size of content on the printed page. **A single point is equivalent to 1/72 of an inch**.


####Device Space

The device coordinate space refers to the native coordinate space used by the target device, whether it be a screen, printer, file, or some other device. Units in the device coordinate space are specified using **pixels** and the resolution of this space is device dependent. 


####Resolution-Independent User Interface

In OS X v10.4 and earlier, Quartz and Cocoa always treated screen devices as if their resolution were always 72 dpi, regardless of their actual resolution. This meant that for screen-based drawing, one point in user space was always equal to one pixel in device space.



##Transform Basics

A transform is two-dimensional mathematical array used to map points from one coordinate space to another. Using transforms, you can **scale**, **rotate**, and **translate** content freely in two-dimensional space using only a few methods and undo your changes just as quickly. 


###The Identity Transform

An identity transform maps any point to itself—that is, it does not transform the point at all. You always start with an identity transform and add transformations to it. Starting with the identity transform guarantees that you start from a known state. 

To create an identity transform, you would use the following code:

*NSAffineTransform identityXform = [NSAffineTransform transform];*


###Transformation Operations

For two-dimensional drawing, you can transform content in several different ways, including translating, scaling, and rotating.

The following sections describe each type of transformation and how it affects rendered content.


####Translation

Translation involves **shifting** the origin of the current coordinate system horizontally and vertically by a specific amount.


####Scaling

Scaling lets you **stretch or shrink** the units of the user space along the x and y axes independently. Normally, one unit in user space is equal to 1/72 of an inch. 


####Rotation

Rotation changes the orientation of the coordinate axes by rotating them around the current origin.



###Transform Mathematics

The *NSAffineTransform* class uses a 3 x 3 matrix to store the transform values.


Once applied, a transform affects all subsequent drawing calls in the current context. To undo a set of transformations, you can either restore a previous graphics state or apply an inverse transform.

Events sent to your view by the operating system are sent using the coordinate system of the window. Before your view can use any coordinate values included with the event, it must convert those coordinates to its own local coordinate space. 



##Flipped Coordinate Systems

By default, Cocoa uses a standard Cartesian coordinate system, where positive values extend up and to the right of the origin.

Flipping the coordinate system can make drawing easier in some situations. Text systems in particular use flipped coordinates to simplify the placement of text lines, which flow from top to bottom in most writing systems.

Configuring a view to use flipped coordinates affects only the content you draw directly in that view. Flipped coordinate systems are not inherited by child views. 


###Configuring Your View to Use Flipped Coordinates

If you prefer to use flipped coordinates, there are two ways to configure your view’s coordinate system prior to drawing:

1. Override your view’s *isFlipped* method and return YES.
2. Apply a flip transform to your content immediately prior to rendering.

The most noticeable change is that Cocoa adds the appropriate conversion transform to the CTM before calling your view’s *drawRect:* method.


###Drawing Content in a Flipped Coordinate System

* Drawing Shape Primitives
* Drawing With Application Kit Functions
* Drawing Images
* Drawing Text

The text rendering facilities in Cocoa take their cues for text orientation from the current view.


###Creating a Flip Transform

If you want to flip the coordinate system of your view temporarily, you can create a flip transform and apply it to the current graphics context.

A flip transform is an NSAffineTransform object configured with two transformations: a scale transformation and a translate transformation.


- (void)drawRect:(NSRect)rect {
    NSRect frameRect = [self bounds];
    NSAffineTransform* xform = [NSAffineTransform transform];
    [xform translateXBy:0.0 yBy:frameRect.size.height];
    [xform scaleXBy:1.0 yBy:-1.0];
    [xform concat];
 		
    // Draw flipped content.
}


###Cocoa Use of Flipped Coordinates

The following controls and views currently use flipped coordinates by default:

* NSButton
* NSMatrix
* NSProgressIndicator
* NSScrollView
* NSSlider
* NSSplitView
* NSTabView
* NSTableHeaderView
* NSTableView
* NSTextField
* NSTextView


Some Cocoa classes support flipped coordinates but do not use them all the time. The following list includes the known cases where flipped-coordinate support depends on other mitigating factors.

* Images do not use flipped coordinates by default; however, you can flip the image’s internal coordinate system manually using the *setFlipped:* method of *NSImage*. 

* The Cocoa text system takes cues from the current context to determine whether text should be flipped.

* An *NSClipView* object determines whether to use flipped coordinates by looking at the coordinate system of its document view.

* Graphics convenience functions, such as those declared in *NSGraphics.h*, take flipped coordinate systems into account when drawing.



##Doing Pixel-Exact Drawing

Although Cocoa provides default behavior for laying out custom content, there are many times when you may want to adjust the position of individual views or images to avoid visual artifacts. This is especially true when tiling or drawing bitmap images on high-resolution devices (such as printers) or devices where resolution independent scale factors are in effect.

###Tips for Resolution Independent Drawing in Cocoa

Cocoa applications provide a tremendous amount of support for rendering to high-resolution devices.

The following list includes some approaches to take when designing your interface:

* Use high-resolution images.
* During layout, make sure views and images are positioned on integral pixel boundaries.
* When creating tiled background images for custom controls, use the *NSDrawThreePartImage* and *NSDrawNinePartImage* methods to draw your background rather than trying to draw it yourself.
* Use **antialiased text rendering modes** for non-integral scale factors and be sure to lay out your text views on pixel boundaries.
* Test your applications with non-integral scale factors such as 1.25 and 1.5. These factors tend to generate odd numbers of pixels, which can reveal potential pixel cracks.


###Accessing the Current Scale Factor

The *NSWindow* and *NSScreen* classes both include a *userSpaceScaleFactor* method that you can call to obtain the current scale factor, if any, for your application. 


###Adjusting the Layout of Your Content

The following steps are not required if the current scale factor is 1.0 but would be required for other scale factors.

* Convert the user-space point, size, or rectangle value to device space coordinates.
* Normalize the value in device space so that it is aligned to the appropriate pixel boundary.
* Convert the normalized value back to user space.
* Draw your content using the adjusted value.


###Converting Coordinate Values

In OS X v10.5, several methods were added to NSView to simplify the conversion between user space and device space coordinates:

* convertPointToBase:
* convertSizeToBase:
* convertRectToBase:
* convertPointFromBase:
* convertSizeFromBase:
* convertRectFromBase: