--- 
layout: post
title: Text and Paths
date: 2012-11-06 14:28:49
categories:
    - 
tags:
    -
---

#Text

Text rendering is a special type of drawing that is an important part of most applications.


##Text Attributes

Cocoa provides support for programmatically getting font information using the NSFont class.

You can apply fonts as attributes to strings or use them to set the default font in the current context. The Cocoa text system also uses font objects for formatting text.

The NSFont class does not provide a programmatic way to modify other text attributes, such as the character spacing and text drawing mode. Cocoa does, however, provide a system Font panel that you can display to the user. 


##Simple Text Drawing

If you need to draw a small amount of text quickly, the simplest way to do it is using the methods of *NSString* and *NSAttributedString*.

If you need to do more complex text layout, you should still consider using the **Cocoa text system**.


##Advanced Text Drawing

If your program displays a lot of text or needs to arrange text in complex ways, you should use the Cocoa text system. This system provides advanced text-handling capabilities on top of basic features such as text input, layout, display, editing, copying, and pasting. 

**Text layout** is one of the most expensive drawing-related operations you can do and the **Cocoa text system** is optimized for the best possible performance. The text system manages a sophisticated set of caches and optimizes the times at which it performs layout to reduce the impact on your program’s drawing cycle.

The text engine at the heart of the Cocoa text system is highly customizable. You can subclass several text system classes to provide custom layout and typesetting behavior.


---


#Paths

A path is a collection of points used to create primitive shapes such as lines, arcs, and curves. From these primitives, you can create more complex shapes, such as circles, rectangles, polygons, and complex curved shapes, and paint them.


##Path Building Blocks

Cocoa defines several fundamental data types for manipulating geometric information in the drawing environment. These data types include *NSPoint*, *NSRect*, and *NSSize*. 

The NSPoint, NSRect, and NSSize data types have equivalents in the Quartz environment: CGPoint, CGRect, and CGSize.


##The NSBezierPath Class

The NSBezierPath class provides the behavior for drawing most primitive shapes, and for many complex shapes, it is the only tool available in Cocoa.

An NSBezierPath object encapsulates the information associated with a path, including the points that define the path and the attributes that affect the appearance of the path.


###Path Elements

An NSBezierPath object uses path elements to build a path. 

A path element consists of a primitive command and one or more points. The command tells the path object how to interpret the associated points.

The NSBezierPath class defines only four basic path element commands:

* NSMoveToBezierPathElement
* NSLineToBezierPathElement
* NSCurveToBezierPathElement
* NSClosePathBezierPathElement


###Subpaths

A subpath is a series of connected line and curve segments within an NSBezierPath object.

A single path object may contain multiple subpaths, with each subpath delineated by a Move To or Close Path element.


###Path Attributes

An NSBezierPath object maintains all of the attributes needed to determine the shape of its path.

Path attributes do not take effect until you fill or stroke the path, so if you change an attribute more than once before drawing the path, only the last value is used.

The NSBezierPath class maintains both a custom and default version of each attribute. Path objects use custom attribute values if they are set. If no custom attribute value is set for a given path object, the default value is used.

The following sections describe the attributes you can set for a path object and how those attributes affect your rendered paths.


####Line Width

The line width attribute controls the width of the entire path. 

Line width is measured in points and specified as a floating-point value. The default width for all lines is 1.

To change the default line width for all NSBezierPath objects, you use the *setDefaultLineWidth:* method. To set the line width for the current path object, you use the *setLineWidth:* method of that path object.


####Line Cap Styles

The current line cap style determines the appearance of the open end points of a path segment.

To set the line cap style for a NSBezierPath object, use the *setLineCapStyle:* method. The default line cap style is set to **NSButtLineCapStyle**. To change the default line cap style, use the *setDefaultLineCapStyle:* method. 


####Line Join Styles

The current line join style determines how connected lines in a path are joined at the vertices.

To set the line join style for an NSBezierPath object, use the *setLineJoinStyle:* method. The default line join style is set to **NSMiterLineJoinStyle**. To change the default line join style, use the *setDefaultLineJoinStyle:* method.


####Line Dash Style

The line dash style determines the pattern used to stroke a path. By default, stroked paths appear solid.

The NSBezierPath class does not support the concept of a default line dash style. If you want a line dash style, you must apply it to a path explicitly using the *setLineDash:count:phase:* method.


####Line Flatness

The line flatness attribute determines the rendering accuracy for curved segments. The flatness value measures the maximum error tolerance (in pixels) to use during rendering. 


####Miter Limits

Miter limits help you avoid spikes that occur when you join two line segments at a sharp angle.

If the ratio of the miter length—the diagonal length of the miter—to the line thickness exceeds the miter limit, the corner is drawn using a bevel join instead of a miter join.

To set the miter limits for a specific NSBezierPath object, use the *setMiterLimit:* method. To set the default miter limit for newly created NSBezierPath objects, use *setDefaultMiterLimit:*. 


####Winding Rules

When you fill the area encompassed by a path, **NSBezierPath** applies the current winding rule to determine which areas of the screen to fill.

A winding rule is simply an algorithm that tracks information about each contiguous region that makes up the path's overall fill area. 

To set the winding rule for an NSBezierPath object, use the *setWindingRule:* method. The default winding rule is NSNonZeroWindingRule. To change the default winding rule for all NSBezierPath objects, use the *setDefaultWindingRule:* method.



##Manipulating Geometric Types

The Foundation framework includes numerous functions for manipulating geometric values and for performing various calculations using those values.

NSPoint NSMakePoint(x, y)
NSSize NSMakeSize(w, h)
NSRect NSMakeRect(x, y, w, h)


##Drawing Fundamental Shapes

For many types of content, path-based drawing has several advantages over image-based drawing:

* Because paths are specified mathematically, they scale easily to different resolutions.
* The geometry information associated with a path requires much less storage space than most image data formats.
* Rendering paths is often faster than compositing a comparable image. 


###Adding Points

An NSPoint structure by itself represents a location on the screen;


###Adding Lines and Polygons

Cocoa provides a couple of options for adding lines to a path, with each technique offering different tradeoffs between efficiency and correctness.

You can draw lines in the following ways:

* Create single horizontal and vertical lines by filling a rectangle using *NSRectFill*. 
* Use the *lineToPoint:*, *relativeLineToPoint:*, or *strokeLineFromPoint:toPoint:* methods of NSBezierPath to create individual or connected line segments. 
* Use the *appendBezierPathWithPoints:count:* method to create a series of connected lines quickly. 


###Adding Rectangles

###Adding Rounded Rectangles

In OS X v10.5 and later, the NSBezierPath class includes the following methods for creating rounded-rectangles:

* bezierPathWithRoundedRect:xRadius:yRadius:
* appendBezierPathWithRoundedRect:xRadius:yRadius:


###Adding Ovals and Circles

To draw ovals and circles, use the following methods of NSBezierPath:

* bezierPathWithOvalInRect:
* appendBezierPathWithOvalInRect:

Both methods inscribe an oval inside the rectangle you specify. You must then fill or stroke the path object to draw the oval in the current context. 


###Adding Arcs

To draw arcs, use the following methods of NSBezierPath:

* appendBezierPathWithArcFromPoint:toPoint:radius:
* appendBezierPathWithArcWithCenter:radius:startAngle:endAngle:
* appendBezierPathWithArcWithCenter:radius:startAngle:endAngle:clockwise:


###Adding Bezier Curves

To draw Bezier curves, you must use the *curveToPoint:controlPoint1:controlPoint2:* method of NSBezierPath. 


###Adding Text

Because NSBezierPath only supports path-based content, you cannot add text characters directly to a path; instead, you must add glyphs.

A glyph is the **visual representation of a character** (or partial character) in a particular font. For glyphs in an outline font, this visual representation is stored as a set of mathematical paths that can be added to an NSBezierPath object.


###Drawing the Shapes in a Path

There are two options for drawing the contents of a path: you can stroke the path or fill it.

Stroking a path renders an outline of the path’s shape using the current stroke color and path attributes. Filling the path renders the area encompassed by the path using the current fill color and winding rule.



##Drawing Rectangles

Cocoa provides several functions for drawing rectangles to the current context immediately using the default attributes. 

void NSEraseRect(aRect)
void NSFrameRect(aRect)
void NSFrameRectWithWidth(aRect, width)



##Working with Paths

Building a sleek and attractive user interface is hard work and most programs use a combination of images and paths to do it. Paths have the advantage of being lightweight, scalable, and fast. 

The following sections provide some basic tips and guidance on how to use paths effectively in your program.


###Building Paths

Building a path involves creating an NSBezierPath object and adding path elements to it. All paths must start with a Move To element to mark the first point of the path. 


###Improving Rendering Performance

As you work on your drawing code, you should keep performance in mind.


###Reuse Your Path Objects

If you draw the same content repeatedly, consider caching the objects used to draw that content. 

It is usually more efficient to retain an existing NSBezierPath object than to recreate it during each drawing cycle.  For content that might change dynamically, you might also consider maintaining a pool of reusable objects.


###Correctness Versus Efficiency

When writing your drawing code, you should always try to make that code as efficient as possible without sacrificing the quality of the rendered content.


###Reduce Path Complexity

If you are drawing a large amount of content, you should do your best to reduce the complexity of the path data you store in a single NSBezierPath object. 


###Manipulating Individual Path Elements

Given an NSBezierPath object with some existing path data, you can retrieve the points associated with that path and modify them individually. 


###Transforming a Path

The coordinate system of an NSBezierPath object always matches the coordinate system of the view in which it is drawn. 

Thus, given a path whose first point is at (0, 0) in your NSBezierPath object, drawing the path in your view places that point at (0, 0) in the view’s current coordinate system. 

To draw that path in a different location, you must apply a transform in one of two ways:

* Apply the transform to the view coordinate system and then draw the path.
* Apply the transform to the NSBezierPath object itself using the *transformUsingAffineTransform:* method and then draw it in an unmodified view.


###Creating a CGPathRef From an NSBezierPath Object

There may be times when it is necessary to convert an NSBezierPath object to a CGPathRef data type so that you can perform path-based operations using Quartz. 


###Detecting Mouse Hits on a Path

If you need to determine whether a mouse event occurred on a path or its fill area, you can use the *containsPoint:* method of NSBezierPath.

When determining whether a point is inside a subpath, the method uses the nonzero winding rule.

If your software runs in OS X v10.4 and later, you can perform more advanced hit detection using the *CGContextPathContainsPoint* and *CGPathContainsPoint* functions in Quartz. Using these functions you can determine if a point is on the path itself or if the point is inside the path using either the nonzero or even-odd winding rule.