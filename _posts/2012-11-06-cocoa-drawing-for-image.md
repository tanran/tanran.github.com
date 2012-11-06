--- 
layout: post
title: Cocoa Drawing for Image
date: 2012-11-06 10:52:45
categories:
    - 
tags:
    -
---


You can use images to render preexisting content or act as a buffer for your application's drawing commands.

At the heart of Cocoa's image manipulation code is the NSImage class. This class manages everything related to image data and is used for the following tasks:

* Loading existing images from disk.
* Drawing image data into your views.
* Creating new images.
* Scaling and resizing images.
* Converting images to any of several different formats.


---


##Image Basics


The *NSImage* class provides the high-level interface for manipulating images in many different formats. 

Because it provides the high-level interface, NSImage knows almost nothing about managing the actual image data. Instead, the NSImage class **manages one or more image representation objects—objects** derived from the *NSImageRep* class. Each image representation object understands the image data for a particular format and is capable of rendering that data to the current context.


###Image Representations

An image representation object represents an image at a specific size, using a specific color space, and in a specific data format. Image representations are used by an *NSImage* object to manage image data.

For file-based images, *NSImage* creates an image representation object for each separate image stored in the file. Although most image formats support only a single image, formats such as TIFF allow multiple images to be stored.


###Image Representation Classes

Every image representation object is based on a subclass of *NSImageRep*. 

Cocoa defines several specific subclasses to handle commonly used formats. 


###How an Image Representation Is Chosen

When you tell an *NSImage* object to draw itself, it searches its list of image representations for the one that best matches the attributes of the destination device. 

In determining which image representation to choose, it follows a set of ordered rules that compare the **color space**, **image resolution**, **bit depth**, and **image size** to the corresponding values in the destination device.

You can change the order in which these rules are applied using the methods of NSImage.


###Images and Caching

The *NSImage* class incorporates an **internal caching scheme** aimed at improving your application’s drawing performance.

This caching scheme is an important part of image management and is **enabled by default for all image objects**; however, you can change the caching options for a particular image using the *setCacheMode:* method of *NSImage*.

Caching is a useful step toward preparing an image for display on the screen. When first loaded, the data for an image representation may not be in a format that can be rendered directly to the screen.

For bitmap image representations, the decision to cache is dependent on the bitmap image data.


**[NOTE]** Caching is aimed at improving **performance during screen updates**. During printing, Cocoa uses the native image data and resolution whenever possible and uses cached versions of the image only as a last resort.


###Caching and Image Data Retention

Because caching can lead to multiple copies of the image data in memory, NSImage usually dismisses the original image data once a cached copy is created. 

Dismissing the original image data saves memory and improves performance and is appropriate in situations where you do not plan on changing the image size or attributes. If you do plan on changing the image size or attributes, you may want to disable this behavior. 

Enabling data retention prevents image degradation by basing changes on the original image data, as opposed to the currently cached copy.

To retain image data for a specific image, you must send a *setDataRetained:* message to the *NSImage* object. Preferably, you should send this message immediately after creating the image object.


###Caching Images Separately

To improve performance, most caching of an application’s images occurs in one or more offscreen windows. These windows act as image repositories for the application and are not shared by other applications. Cocoa manages them automatically and assigns images to them based on the current image attributes.

By default, Cocoa tries to reduce the number of offscreen windows by putting multiple images into a single window. For images whose size does not change frequently, this technique is usually faster than storing each image in its own window. 

For images whose size does change frequently, it may be better to cache the image separately by sending a setCachedSeparately: message to the image object.


###Image Size and Resolution

Both *NSImage* and *NSImageRep* define methods for getting and setting the size of an image. The meaning of sizes can differ for each type of object, however.

For NSImage, the size is always specified in units of the user coordinate space.
For NSImageRep, the size is generally tied to the size of the native or cached bitmap.

With data retention disabled in an image, scaling the image multiple times can seriously degrade the resulting image quality. 

If you need several different sizes of an image, you might want to create multiple image representation objects, one for each size, to avoid any lossy behavior. Alternatively, you can use the *setDataRetained:* method to ensure that the caching system has access to the original image data.


###Image Coordinate Systems

Like views, NSImage objects use their own coordinate system to manage their content, which in this case is the image data itself. This internal coordinate system is independent of any containing views into which the image is drawn. 

The purpose of the internal coordinate system is to orient the image data itself.

Image objects have two possible orientations: standard and flipped.

By default, images use the standard Cartesian (unflipped) coordinate system, but you can force them to use a flipped coordinate system by calling the setFlipped: method of NSImage prior to drawing. 

Regardless of the orientation of its internal coordinate system, you always place an image relative to the current view’s coordinate system. 


###Drawing Versus Compositing

The NSImage class offers different groups of methods to facilitate drawing your images to the current context. The two main groups of methods can be generally categorized as the “drawing” versus “compositing” methods.

There are three “drawing” methods of NSImage:

* drawAtPoint:fromRect:operation:fraction:
* drawInRect:fromRect:operation:fraction:
* drawRepresentation:inRect:

The draw methods use **extra safety checking** to ensure that only the contents of the current image are ever drawn in one of your views. The same is not true of compositing methods, of which there are the following:

* compositeToPoint:operation:
* compositeToPoint:fromRect:operation:
* compositeToPoint:fromRect:operation:fraction:
* compositeToPoint:operation:fraction:

The most important behavior is that the compositing methods undo any scale or rotation factors (but not translation factors) applied to the CTM prior to drawing.



##Supported Image File Formats

Cocoa supports many common image formats internally and can import image data from many more formats through the use of the Image I/O framework (ImageIO.framework).


###Basic Formats
###TIFF Compression
###Support for Other File Formats



##Guidelines for Using Images

Here are some guidelines to help you work with images more effectively:

* Use the NSImage interface whenever possible.
* Treat NSImage and its image representations as immutable objects.
* For screen-based drawing, it is best to use the built-in caching mechanism of NSImage.
* There is little benefit to storing multiple representations of the same image (possibly at different sizes) in a single NSImage. 

The only reason to consider storing multiple representations is if each of those representations contains a customized version of the image or if your app supports high-resolution displays.

* If caching is enabled and you modify an image representation object directly, be sure to invoke the recache method of the owning NSImage object. 
* Avoid recreating art that is already provided by the system.
* If your app supports high resolution displays, follow the guidelines in **High Resolution Guidelines for OS X** for providing and naming standard- and high-resolution versions of image resources.



##Creating NSImage Objects

Before you can draw an image, you have to create or load the image data


###Loading an Existing Image

For existing images, you can load the image directly from a file or URL using the methods of NSImage. 

It is important to remember that images loaded from an existing file are intended primarily for rendering. If you want to manipulate the data directly, copy it to an offscreen window or other local data structure and manipulate it there.


###Loading a Named Image

For frequently used images, you can use the Application Kit’s **named image registry** to load and store them. This registry provides a fast and convenient way to retrieve images without creating a new NSImage object each time.

You can add images to this registry explicitly or you can use the registry itself to load known system or application-specific images, such as the following:

* System images stored in the Resources directory of the Application Kit framework
* Your application’s icon or other images located in the Resources directory of your main bundle.

To retrieve images from the registry, you use the *imageNamed:* class method of *NSImage*. This method looks in the registry for an image associated with the name you provide.

In addition to loading existing image files, you can also add images to the registry explicitly by sending a *setName:* message to an *NSImage* object.


###Drawing to an Image by Locking Focus

It is possible to create images programmatically by locking focus on an NSImage object and drawing other images or paths into the image context.


###Drawing Offscreen Images Using a Block-Based Drawing Method to Support High Resolution Displays

Using the *imageWithSize:flipped:drawingHandler:* method ensures you’ll get correct results under standard and high resolution. 


###Creating a Bitmap

There are a few different ways to create bitmaps in Cocoa. 


###Capturing the Contents of a View or Image

A simple way to create a bitmap is to capture the contents of an existing view or image. 

Before attempting to capture the contents of a view, you should consider invoking the view’s *canDraw* method to see if the view should be drawn. Once you have your view or image, lock focus on it and use the *initWithFocusedViewRect:* method of *NSBitmapImageRep* to capture the contents. 

To capture the content of a detached (offscreen) view, you must create an offscreen window for the view before you try to capture its contents. The window object provides a backing buffer in which to hold the view’s rendered content.


###Drawing Directly to a Bitmap

In OS X v10.4 and later, it is possible to create a bitmap image representation object and draw to it directly.


###Creating a PDF or EPS Image Representation

The NSView class defines two convenience methods for generating data based on the contents of the view:

* For PDF data, use the *dataWithPDFInsideRect:* method of NSView.
* For EPS data, use the *dataWithEPSInsideRect:* method of NSView.


###Using a Quartz Image to Create an NSImage

If you have a *CGImageRef* object, the simplest way to create a corresponding Cocoa image is to lock focus on an *NSImage* object and draw your Quartz image using the *CGContextDrawImage* function.



##Working with Images

The simplest thing you can use image is draw it into a view as part of your program’s user interface. You can also process the image further by modifying it in any number of ways.


###Drawing Images into a View

The NSImage class defines several methods for drawing an image into the current context. The two most commonly used methods are:

* drawAtPoint:fromRect:operation:fraction:
* drawInRect:fromRect:operation:fraction:


###Drawing Resizable Textures Using Images

If you are implementing a resizable custom control and want the control to have a textured background that does not distort as the control is resized, you would typically break up the background portion into several different images and composite them together. Although some of the images would contain fixed size content, others would need to be designed to present a smooth texture even after being resized or tiled.

When it comes time to draw the images, however, you should avoid doing the drawing yourself. Instead, you should use the following AppKit functions, which were introduced in OS X v10.5:

* NSDrawThreePartImage
* NSDrawNinePartImage


###Creating an OpenGL Texture

In OpenGL, a texture is one way to paint the surface of an object. 

For complex or photorealistic surfaces, it may be easier to apply a texture than render the same content using primitive shapes. Almost any view or image in Cocoa can be used to create an OpenGL texture.


###Applying Core Image Filters

In OS X v10.4 and later, Core Image filters are a fast and efficient way to modify an image without changing the image itself. 

Core Image filters use graphics acceleration to apply real-time effects such as Gaussian blurs, distortions, and color corrections to an image. Because the filters are **applied when content is composited to the screen**, they do not modify the actual image data.


###Getting and Setting Bitmap Properties

Every NSBitmapImageRep object contains a dictionary that defines the bitmap’s associated properties. 


###Converting a Bitmap to a Different Format

The NSBitmapImageRep class provides built-in support for converting bitmap data to several standard formats. 


###Associating a Custom Color Profile With an Image

You can associate a custom ColorSync profile with a *NSBitmapImageRep* object containing pixel data produced by decoding a TIFF, JPEG, GIF or PNG file.

To associate the data with the bitmap image representation, you use the *setProperty:withValue:* method of *NSBitmapImageRep* and the *NSImageColorSyncProfileData* property.


###Converting Between Color Spaces

Cocoa does not provide any direct ways to convert images from one color space to another.

Although Cocoa fully supports color spaces included with existing image files, there is no way to convert image data directly using NSImage. Instead, you must use a combination of Quartz and Cocoa to convert the image data.


###Using a Custom Color Profile

If you have an existing ICC profile and want to associate that profile with an image, you must do so using the ColorSync Manager.


###Premultiplying Alpha Values for Bitmaps

The only reason to consider premultiplication of alpha values for your bitmaps is if your data is already premultiplied and leaving it that way is beneficial to your program’s data model.



##Creating New Image Representation Classes

If you want to add support for new image formats or generate images from other types of source information, you may want to subclass *NSImageRep*.


If you decide to subclass *NSImageRep*, you should provide implementations for the following methods:

* imageUnfilteredTypes
* canInitWithData:
* initWithData:
* draw

Before your subclass can be used, it must be registered with the Application Kit. You should do this early in your application’s execution by invoking the registerImageRepClass: class method of NSImageRep. 


**[NOTE]** If your subclass is capable of reading multiple images from a single file, you should also implement the imageRepsWithData: method.