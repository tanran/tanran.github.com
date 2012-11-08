--- 
layout: post
title: Using Tracking-Area Objects
date: 2012-11-08 16:07:47
categories:
    - 
tags:
    -
---


An instance of the NSTrackingArea class defines an area of a view that is responsive to the movements of the mouse.

The Application Kit sends the mouse-tracking messages mouseEntered: and mouseExited: to an object when the mouse cursor enters and exits a region of a window.

Cursor-update events are a special kind of mouse-tracking event that the Application Kit handles automatically.


##Creating an NSTrackingArea Object

An NSTrackingArea object defines a region of a view that is sensitive to the movements of the mouse.

The region is a rectangle specified in the local coordinate system of its associated view. 

When you create an NSTrackingArea object you must specify one or more options. 

* The type of event message sent
* The active scope of tracking-area messages
* Refinements of tracking-area behavior

You initialize an allocated instance of NSTrackingArea with the *initWithRect:options:owner:userInfo:* method. After creating the object, you must associate it with a view by invoking the NSView method *addTrackingArea:*. 


##Managing a Tracking-Area Object

Because NSTrackingArea objects are owned by their views, the Application Kit can automatically recompute the tracking-area regions when the view is added or removed from a window or when the view changes position within its window. But in situations where the Application Kit cannot recompute an affected tracking area (or areas), it sends *updateTrackingAreas* to the associated view, asking it to recompute and reset the areas.

You can override the *updateTrackingAreas* method to remove the current tracking areas from their views, release them, and then add new NSTrackingArea objects to the same views with recomputed regions.

If your class is not a custom view class, you can register your class instance as an observer for the notification NSViewFrameDidChangeNotification and have it reestablish the tracking rectangles on receiving the notification.


##Responding to Mouse-Tracking Events

You override the corresponding NSResponder methods to handle these messages to perform such tasks as highlighting the view, displaying a custom tool tip, or displaying related information in another view.


##Managing Cursor-Update Events

One common use of NSTrackingArea objects is to change the cursor image over different types of views. Text, for example, typically requires an I-beam cursor. 

You change the cursor image for your view in an override of the NSResponder method *cursorUpdate:*.

**[Note]** It is not necessary to reset the cursor image back to what it was when the mouse exits the tracking area. The Application Kit handles this automatically for you by sending a cursorUpdate: message to the view over which the mouse cursor moves as it exits the cursor rectangle. 

As with any other kind of NSTrackingArea object, you might occasionally need to recompute and recreate a tracking-area object used for cursor updates when the associated view has changes in its location or size.


##Compatibility Issues

Beginning with OS X v10.5, the NSTrackingArea class and the related NSView methods addTrackingArea:, removeTrackingArea:, updateTrackingAreas, and trackingAreas replace the following methods of NSView, which are considered legacy API but remain supported for compatibility.