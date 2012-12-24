--- 
layout: post
title: UIViewController Class Reference
date: 2012-11-20 10:45:51
categories:
    - 
tags:
    -
---

The UIViewController class provides the fundamental view-management model for all iOS apps.

You rarely instantiate UIViewController objects directly. Instead, you instantiate subclasses of the UIViewController class based on the specific task each subclass performs.

A view controller manages a set of views that make up a portion of your app’s user interface. As part of the controller layer of your app, a view controller coordinates its efforts with model objects and other controller objects—including other view controllers—so your app presents a single coherent user interface.

Where necessary, a view controller:

* resizes and lays out its views
* adjusts the contents of the views
* acts on behalf of the views when the user interacts with them

A typical view hierarchy consists of a view with flexible bounds—a reference to which is available in the view property of this class—and one or more subviews that provide the actual content. 

The size and position of the root view is usually determined by another object that owns the view controller and displays its contents. The view controller determines the positions of any subviews it owns based on the size given to its root view by the owning object.

 A view controller is usually owned by a window or another view controller. If a view controller is owned by a window object, it acts as the window’s root view controller. The view controller’s root view is added as a subview of the window and resized to fill the window. If the view controller is owned by another view controller, then the parent view controller determines when and how the child view controller’s contents are displayed.


Specifically, views are automatically loaded when the view property is accessed.

* You can specify the views for a view controller using a storyboard created in Interface Builder. 
* You can specify the views for a view controller using a nib file, also created in Interface Builder.
* If you cannot define your views in a storyboard or a nib file, override the loadView method to manually instantiate a view hierarchy and assign it to the view property.

When creating the views for your view hierarchy, you should always set the autoresizing properties of your views.

A container view controller manages the presentation of content of other view controllers it owns, also known as its child view controllers. A child’s view can be presented as-is or in conjunction with views owned by the container view controller.

A child’s view can be presented as-is or in conjunction with views owned by the container view controller.

Your container view controller subclass should declare a public interface to associate its children.

To display a child container’s content, your view controller subclass needs to add the root view of the child into its own view hierarchy, adjusting its size and location to fit. 

In order for iOS to route events properly to child view controllers and the views those controllers manage, your container view controller must associate a child view controller with itself before adding the child’s root view to the view hierarchy. 