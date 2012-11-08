--- 
layout: post
title: Handling Mouse Events
date: 2012-11-08 14:59:45
categories:
    - 
tags:
    -
---


##Overview of Mouse Events

Here are some central facts about mouse events:

* Mouse events are dispatched by an NSWindow object to the NSView object over which the event occurred.
* Subsequent key events are dispatched to that view object if it accepts first responder status.
* If the user clicks a view that isn’t in the key window, by default the window is brought forward and made key, but the mouse event is not dispatched.
* Mouse events are of various event types related to the mouse button pressed (left, right, other) and the nature of the action on the mouse button.

Because mouse-moved events occur so frequently that they can quickly flood the event-dispatch machinery, **an NSWindow object by default does not receive them from the global NSApplication object**. However, you can specifically request these events by sending the NSWindow object an *setAcceptsMouseMovedEvents:* message with an argument of YES.


##Handling Mouse Clicks

By default, a mouse-down event in a window that isn’t the key window simply brings the window forward and makes it key; the event isn’t sent to the NSView object over which the mouse click occurs. The NSView can claim an initial mouse-down event, however, by overriding *acceptsFirstMouse:* to return YES. 

In your implementation of an NSResponder mouse-event method, often the first thing you might do is examine the passed-in NSEvent object to decide if this is an event you want to handle.

Many view objects in the Application Kit (such as controls and menu items) change their appearance in response to mouse-down events, sometimes only until the subsequent mouse-up event.

But many view objects, particularly controls and cells, do more than simply change their appearance in response to mouse clicks. 


##Handling Mouse Dragging Operations

Views can also receive mouse-dragged events when a user moves a mouse while pressing down a mouse button. A view typically interprets mouse-dragged events as commands to move itself by altering its frame location or to move a region within its bounds by redrawing it. 

Establishing a mouse-tracking loop gives you greater control over the way other events interact with your application during a dragging operation. However, the application’s main thread is unable to process any other requests during an event-tracking loop and timers might not fire as expected.


###The Three-Method Approach

The subclass implementing these methods often has to declare instance variables to hold the changing values or states of various things between successive events. 

Because mouse-dragging operations often redraw an object in the incrementally changing locations where users drag that object, implementations of the three mouse-dragging methods usually need to find the location of each mouse event in the view’s coordinate system.


###The Mouse-Tracking Loop Approach

The mouse-tracking technique for handling mouse-dragging operations is applied in a single method, usually (but not necessarily) in mouseDown:. The implementing responder object first declares and possibly initializes one or more local variables to use within the loop. Often one of these variables holds a value, often Boolean, that is used to control the loop. When some condition is met, typically the receipt of a mouse-up event, the variable value is changed; when this variable is tested next time through the loop, control exits the loop.


###Filtering Out Key Events During Mouse-Tracking Operations

A potential problem with mouse-tracking code is the user pressing a key combination that is a command, such as Command-z (undo), while a tracking operation is underway.

For dragging operations handled with the three-method approach, the situation with simultaneous mouse and key events is a bit different. In this case, the AppKit processes keyboard events as it normally does during tracking.

The solution to this problem involves a few steps:

* Declare a Boolean **instance variable** that reflects when a dragging operation is underway.
* Set this variable to YES in *mouseDown:* and reset it to NO in *mouseUp:*.
* Override *performKeyEquivalent:* to check the value of the instance variable and, if a dragging operation is occurring, to discard the key event.