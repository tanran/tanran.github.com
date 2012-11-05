--- 
layout: post
title: The Graphics Context
date: 2012-11-05 15:39:02
categories:
    - 
tags:
    -
---

As the name suggests, a graphics context provides the context for subsequent drawing operations. It identifies the current drawing destination (screen, printer, file, and so on), the coordinate system and boundaries for the underlying canvas, and any graphics attributes associated with the destination.


##Graphics Context Basics

The primary job of any graphics context object is to **maintain information about the current state of the drawing environment**.

Cocoa drawing is view-based.

By the time your view’s *drawRect:* method is called, Cocoa has made sure that any drawing calls you make stay within the confines of your view. Eventually, the content you draw is sent to the **Quartz Compositor**, where it is combined with the content from other views in the window and flushed to the screen or output device. After your *drawRect:* method returns, Cocoa goes through the process of resetting the drawing environment for the next view.


###The Current Context

Each thread in a Cocoa application has its own graphics context object for a given window.

You can access this object from your code using the *currentContext* method of *NSGraphicsContext*.


###Graphics State Information

Each Cocoa graphics context object maintains information about the current state of the drawing environment. This information ranges from the global rendering settings to the attributes used to render the current path and is the same state information saved by **Quartz**.

Graphics state information:

* Current transformation matrix (CTM)
* Clipping area
* Line width
* Line join style
* Line cap style
* Line dash style
* Line miter limit
* Flatness value
* Stroke color
* Fill color
* Shadow
* Rendering intent
* Font name
* Font size
* Font character spacing
* Text drawing mode
* Image interpolation quality
* Compositing operation
* Global alpha
* Anti-aliasing setting


###Screen Canvases and Print Canvases

In a broad sense, Cocoa graphics context objects serve two types of canvases: screen-based canvases and print-based canvases.

For nearly all screen-based and print-based drawing, Cocoa provides an appropriate graphics context object automatically.

You can determine the type of canvas being managed by the current graphics context using the *isDrawingToScreen* instance method or *currentContextDrawingToScreen* class method of *NSGraphicsContext*. 


###Graphics Contexts and Quartz

The *NSGraphicsContext* class in Cocoa is a wrapper for a Quartz graphics context (CGContextRef data type). Both types manage the same basic information, and in fact, many methods of *NSGraphicsContext* simply call their Quartz equivalents.


##Modifying the Current Graphics State

* Setting Colors and Patterns
* Setting Path Attributes
* Setting Text Attributes
* Setting Compositing Options

Compositing options specify how the colors in source content are blended with the existing content in the drawing destination. 

To set the current compositing operation, you use the *setCompositingOperation:* method of *NSGraphicsContext*.

* Setting the Clipping Region

The clipping region is a useful way to limit drawing to a specific portion of your view. Instead of creating complex graphics offscreen and then compositing them precisely in your view, you can **use a clipping region to mask out the portions of your view you do not want modified**. 

Before invoking your view’s *drawRect:* method, Cocoa configures the clipping region of the current graphics context to match the visible area of your view. 

* Setting the Anti-aliasing Options

Anti-aliasing is the process of artificially correcting the jagged (or aliased) edges surrounding text or shapes in bitmap images. These jagged edges occur primarily in lower-resolution bitmaps where it is easier to see individual pixels.

To enable or disable anti-aliasing, use the *setShouldAntialias:* method of *NSGraphicsContext*.


＃＃Creating Graphics Contexts

The type of drawing you do in your application will determine whether you need to create any graphics context objects explicitly or simply use the one Cocoa provides you. 

If all you do is draw in your views, you can probably just use the Cocoa-provided context. This is true both for screen-based and print-based drawing. If your application performs any other type of drawing, however, you may need to create a graphics context yourself.


###Creating a Screen-Based Context

If you want to do any drawing outside of the normal update cycle of your view, you must create a graphics context object explicitly.


###Creating a PDF or PostScript Context

All print-based operations must go through the Cocoa printing system, which handles the work required for setting up the printed pages and running the print job.


##Threading and Graphics Contexts

The Application Kit maintains a unique graphics context for each window and thread combination. Because each thread has its own graphics context object for a given window, it is possible to use secondary threads to draw to that window.

During the normal update cycle for windows, all drawing requests are sent to your application’s main thread for processing. The normal update cycle happens when a user event triggers a change in your user interface. In this situation, you would call the **setNeedsDisplay:** or **setNeedsDisplayInRect:** method (or the display family of methods) from your **application’s main thread** to invalidate the portions of your view that require redrawing. You should not call these methods from any secondary threads.

If you want to update a window or view from a secondary thread, you must manually lock focus on the window or view and initiate drawing yourself.

Creating bitmaps on secondary threads is one way to thread your drawing code. Because bitmaps are self-contained entities, they can be created safely on secondary threads.


**[NOTE]** Although drawing on secondary threads is allowed, you should always handle events and other user-requested actions from your application’s main thread only. 