--- 
layout: post
title: Event Handling Basics
date: 2012-11-07 16:51:47
categories:
    - 
tags:
    -
---

##Preparing a Custom View for Receiving Events

Although any type of responder object can handle events, NSView objects are by far the most common recipient of events. They are typically the first objects to respond to both mouse events and key events, often changing their appearance in reaction to those events.

Many Application Kit classes are implemented to handle events, but by itself NSView does not.

The bare-minimum steps required for this are few:

* If your custom view should be first responder for key events or action messages, override acceptsFirstResponder.
* Implement one or more NSResponder methods to handle events of particular types.
* If your view is to handle action messages passed to it via the responder chain, implement the appropriate action methods.

NSView inherits the NSResponder implementations of the methods for handling mouse events; in these methods, NSResponder simply passes the message up the responder chain. One of these objects in the responder chain can implement a method in a way that ensures your custom view won’t see subsequent related mouse events. Because of this, **custom NSView objects should not invoke super in their implementations of NSResponder mouse-event-handling methods** such as mouseDown:, mouseDragged: and mouseUp: unless it is known that the inherited implementation provides some needed functionality.



##Implementing Action Methods

Action messages are typically sent by **NSMenuItem** objects or by **NSControl** objects. The latter objects usually work together with one or more **NSCell** objects. 

Action methods, unlike NSResponder event methods, don’t have default implementations, so responder subclasses shouldn’t blindly forward action messages to super.

An important feature of action messages is that you can send messages back to sender to get further information or associated data.

In keyboard action messages, an action method is invoked as a result of a particular key-press being interpreted through the key bindings mechanism. 



##Getting the Location of an Event

You can get the location of a mouse or tablet-pointer event by sending *locationInWindow* to an *NSEvent* object. But, as the name of the method denotes, this location (an NSPoint structure) is in the **base coordinate system of a window**, not in the coordinate system of the view that typically handles the event.



##Testing for Event Type and Modifier Flags

On occasion you might need to discover the type of an event.

from elsewhere in an application you can always obtain the currently handled event by sending **currentEvent** to NSApp.

A common test performed in event-handling methods is finding out whether specific modifier keys were pressed at the same moment as a key-press, mouse click, or similar user action. The modifier key usually imparts special significance to the event. 



##Responder-Related Tasks

###Determining First-Responder Status

Usually an NSResponder object can always determine if it's currently the first responder by asking its **window** (or itself, if it's an NSWindow object) for the first responder and then comparing itself to that object.


###Setting the First Responder

You can programmatically change the first responder by sending *makeFirstResponder:* to an NSWindow object; the argument of this message must be a responder object (that is, an object that inherits from NSResponder).

You can also set the initial first responder for a window—that is, the first responder set when the window is first placed onscreen. 