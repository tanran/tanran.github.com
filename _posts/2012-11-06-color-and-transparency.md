--- 
layout: post
title: Color and Transparency
date: 2012-11-06 10:10:57
categories:
    - 
tags:
    -
---

One of the keys to creating interesting graphics is the effective use of color and transparency. 

---

##About Color and Transparency

Support for color in Cocoa is built on top of **Quartz**. The *NSColor* class provides the interface for creating and manipulating colors in a variety of color spaces. 


###Color Models and Color Spaces

A color model is a geometric or mathematical framework that attempts to describe the colors seen by the eye. Each model contains one or more dimensions, which together represent the visible spectrum of color. Numerical values are pinned to each dimension making it possible to describe colors in the color model numerically.

A color space is a practical adaptation of a color model. It specifies the gamut (or range) of colors that can be produced using a particular color model.

While the color model determines the relationship between values in each dimension, the color space defines the absolute meaning of those values as colors.

Cocoa supports the same color spaces as **Quartz 2D**, although accessor methods of *NSColor* focus on the following color spaces:

* RGB
* CMYK
* Gray


###Color Objects

The *NSColor* class in Cocoa provides the interface you need to create and manage individual colors. 

The *NSColor* class is itself *a factory class* for creating the actual color objects. The class methods of NSColor create color objects that are actually based on specific subclasses of NSColor, where **each subclass implements the behavior for a specific color space**.


###Color Component Values

In Cocoa, color space values, called components, are specified as floating-point values in the range 0.0 to 1.0.

When working with other color values from other systems, you must convert any values that do not fall into the supported range. 


###Transparency

Transparency is a powerful effect used to give the **illusion of light passing** through a particular area instead of reflecting off of it.

When you render an object using a partially transparent color, the object picks up some color from the object directly underneath it. The amount of color it picks up depends on the value of the color’s alpha component and the compositing mode.

Like color components, the alpha component is specified as a floating-point value in the range 0.0 to 1.0.


###Pattern Colors

In addition to creating monochromatic colors, you can also **create pattern colors using images**. 

Pattern colors are most applicable as fill colors but can be used as stroke colors as well. When rendered, the image you specify is drawn on the path or its fill area instead of a solid color.


###Color Lists

A color list is a dictionary-like object (implemented by the *NSColorList* class) that contains an ordered list of NSColor objects, identified by keys.

Color lists are available as a tool to help you manage any document-specific colors. They are also used to customize the list of colors displayed in a color panel. 


###Color Matching

Cocoa provides automatic color matching whenever possible using ColorSync. Color matching ensures that the colors you use to draw your content look the same on different devices.

Cocoa provides full support for creating and getting color profile information using the *NSColorSpace* class. 



##Creating Colors

The NSColor class supports the creation of several different types of color objects:

* Commonly used colors, such as red, green, black, or white
* System colors, such as the current control color or highlight color, whose values are set by user preferences
* Calibrated colors belonging to a designated color space
* Device colors belonging to the designated device color space
* Pattern colors

To create most color objects, simply use the appropriate class method of *NSColor*.

**[NOTE]** Never allocate and initialize an NSColor object directly.



##Working with Colors

Once you have an NSColor object, you can apply it to the stroke or fill color of the current context. Once set, any shapes you draw in the current context take on that color. 


###Applying Colors to Drawn Content

Stroke and fill colors modify the **appearance of path-based shapes**, such as those drawn with the *NSBezierPath* class or functions such as *NSRectFill*. The stroke color applies to the path itself, and the fill color applies to the area bounded by that path.

Methods for changing color attributes:
* set					Sets both the stroke and fill color to the same value.
* setFill			Sets the fill color.
* setStroke		Sets the stroke color.

All subsequent drawing operations in the current context would use the specified colors. If you do not want any color to be drawn for the stroke or fill, you can set the current stroke or fill to a completely transparent color, which you can get by calling the clearColor method of NSColor.

**[Note]** Stroke and fill colors do not affect the appearance of text. 


###Applying Color to Text

To apply color to a range of characters in an *NSAttributedString* object, you apply the *NSForegroundColorAttributeName* attribute to the characters. This attribute takes a corresponding *NSColor* object as its value.

To apply color to the characters of an *NSString* object, you apply the *NSForegroundColorAttributeName* attribute just as you would for an *NSAttributedString* object. The difference in application is that attributes applied to an NSString object affect the entire string and not a specified range of characters.


###Getting the Components of a Color

NSColor provides the following accessor methods for retrieving component values of a color:

* numberOfComponents
* getComponents:
* getRed:green:blue:alpha:
* getCyan:magenta:yellow:black:alpha:
* getHue:saturation:brightness:alpha:
* getWhite:alpha:


###Choosing Colors

Applications that need to present a color picker interface to the user can use either a color well or a color panel. 



##Working with Color Spaces

Color spaces help your program maintain color fidelity throughout the creation and rendering process. 


###Converting Between Color Spaces

You can convert between color spaces using the *colorUsingColorSpaceName:* method of *NSColor*.


###Mapping Physical Colors to a Color Space

During rendering, Cocoa attempts to match the colors you specify in your code as closely as it can to the colors available in the target device. 

Sometimes, though, it maps colors in a different way so as to emphasize different aspects of a color that might be more important when reproducing that color. The mapping used for colors is referred to as the **rendering intent** and it is something most developers rarely need to change. 

Because most developers should not need to change the rendering intent, you cannot set the attribute directly from Cocoa. If your application needs more control over the color management, you must use **Quartz** to change the rendering intent.

To change the rendering intent, you must get a Quartz graphics context for the current drawing environment and call the *CGContextSetRenderingIntent* function.