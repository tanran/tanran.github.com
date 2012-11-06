--- 
layout: post
title: Advanced Drawing Techniques
date: 2012-11-06 13:23:02
categories:
    - 
tags:
    -
---

1. Adding Shadows to Drawn Paths
2. Creating Gradient Fills
3. Drawing to the Screen
4. Using NSTimer for Animated Content
5. Using Cocoa Animation Objects
6. Optimizing Your Drawing Code


Beyond the basic drawing of paths and images in views, there are other ways to create more complex imagery for your application.


##Adding Shadows to Drawn Paths

Cocoa provides support for shadows through the *NSShadow* class. A shadow mimics a light source cast on the object, making paths appear as if they’re floating above the surface of the view. 

A shadow effect consists of horizontal and vertical offset values, a blur value, and the shadow color. 

Shadow positions always use the base coordinate system of the view, ignoring any transforms you apply to the shapes in your view. This means that no matter how you manipulate the shapes in a view, the apparent position of the light generating the shadows never changes.

Shadow effects are stored as part of the graphics state, so once set, they affect all subsequent rendering commands in the current context. This is an important thing to remember because it might force you to think about the order in which you draw your content.

**[NOTE]** Another way to a single shadow for multiple paths is using a Quartz transparency layer. 


##Creating Gradient Fills

A gradient fill (also referred to as a shading in Quartz) is a pattern that gradually changes from one color to another.

This uses a mathematical function to **compute the color at individual points along the gradient**. Because they are mathematical by nature, gradients are resolution independent and scale readily to any device.

In OS X v10.5 and later, Cocoa provides support for drawing gradients using the *NSGradient* class. If your software runs on earlier versions of OS X, you must use **Quartz** or **Core Image** to perform gradient fills.


###Using the NSGradient Class

Gradient objects are immutable objects that store information about the colors in the gradient and provide an interface for drawing those colors to the current context. 

The NSGradient class supports both high-level and primitive drawing methods. If you need additional control over the final shape and appearance of the gradient fill, you can set up the clipping path your self and use the primitive drawing methods of NSGradient to do your drawing.


###Configuring the Colors of a Gradient Object

The NSGradient class uses **color stops** to determine the position of color changes in its gradient fill. A color stop is a combination of an NSColor object and a floating-point number in the range 0.0 to 1.0.

By definition, gradients must have at least two color stops. 

The *NSGradient* class defines the following methods for configuring the colors and color stops of a gradient.

* initWithStartingColor:endingColor:
* initWithColors:
* initWithColorsAndLocations:
* initWithColors:atLocations:colorSpace:


###Drawing to a High-Level Path

The NSGradient class defines several convenience methods for drawing both radial and axial gradients:

* drawInRect:angle:
* drawInRect:relativeCenterPosition:
* drawInBezierPath:angle:
* drawInBezierPath:relativeCenterPosition:


###Using the Primitive Drawing Routines

In addition to the high-level convenience methods, the NSGradient class defines two primitive methods for drawing gradients:

* drawFromPoint:toPoint:options:
* drawFromCenter:radius:toCenter:radius:options:


###Using Quartz Shadings in Cocoa

Quartz supports the creation of both radial and axial gradients in different color spaces using a mathematical computation function you provide. The use of a mathematical function means that the gradients you create using Quartz scale well to any resolution. 

To draw a Quartz shading in your Cocoa program, you would do the following from your drawRect: method:

1. Get a CGContextRef using the graphicsPort method of NSGraphicsContext.
2. Create a CGShadingRef using Quartz; see “Gradients” in Quartz 2D Programming Guide.
3. Configure the current clipping path to the desired shape for your shading; see “Setting the Clipping Region.”
4. Draw the shading using CGContextDrawShading.



##Drawing to the Screen

If you want to take over the entire screen for your drawing, you can do so from a Cocoa application.

Entering full-screen drawing mode is a two-step process:

1. Capture the desired screen (or screens) for drawing.
2. Configure your drawing environment.


###Capturing the Screen

Cocoa does not provide direct support for capturing and releasing screens. 

The NSScreen class provides read-only access to information about the available screens. To capture or manipulate a screen, you must use the functions found in Quartz Services.


###Full-Screen Drawing in OpenGL

Applications that do full-screen drawing tend to be graphics intensive and thus use OpenGL to improve rendering speed.


###Full-Screen Drawing in Cocoa

All Cocoa drawing occurs in a window, but for full screen drawing, the window you create is a little different. 

Instead of a bordered window with a title bar, you need to create a borderless window that spans the entire screen area.

Although you create a full-screen window using Cocoa classes, you still have to use Quartz Services to capture the display and configure the window properly. 


###Disabling Screen Updates

You can disable and reenable all screen flushes using the *NSDisableScreenUpdates* and *NSEnableScreenUpdates* functions.

In OS X v10.4 and later, you can also use the *disableScreenUpdatesUntilFlush* method of *NSWindow*.



##Using NSTimer for Animated Content

By default, Cocoa sends a drawRect: message to your views only when a user action causes something to change.

If your view contains animated content, you probably want to update that content at more regular intervals. For both indeterminate-length and finite-length animations, you can do this using timers.



##Using Cocoa Animation Objects

The NSAnimation and NSViewAnimation classes provide sophisticated behavior for animations that occur over a finite length of time. 

Unlike NSTimer, animation notifications can occur at irregular intervals, allowing you to create animations that appear to speed up or slow down.



##Optimizing Your Drawing Code

The following sections provide some basic guidance for improving the overall performance of your drawing code. 


###Draw Minimally

Even with modern graphics hardware, drawing is still an expensive operation. The best way to reduce the amount of time spent in your drawing code is to draw only what is needed in the first place.


###Avoid Forcing Synchronous Updates

When invalidating portions of your views, you should avoid using the display family of methods to force an immediate update. These methods cause the system to send a drawRect: message to the affected view (and potentially other views in the hierarchy) immediately rather than wait until the next regular update cycle. 

If there are several areas to update, this may result in a lot of extra work for your drawing code.

Instead, you should use the setNeedsDisplay: and setNeedsDisplayInRect: methods to mark areas as needing an update.


###Reuse Your Objects

If you have objects that you plan to use more than once, consider caching them for later use.

Caching saves time by eliminating the need to recreate objects each time you want to draw them. Of course, caching requires more memory, so be judicious about what you cache. It is faster to recreate an object in memory than page it in from disk.


###Minimize State Changes

Every time you save the graphics state, you incur a small performance penalty.

Whenever you have objects with the same rendering attributes, try to draw them all at the same time.

With Cocoa, methods and functions that draw right away usually involve a change in graphics state. 