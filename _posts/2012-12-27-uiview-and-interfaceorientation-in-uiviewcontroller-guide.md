--- 
layout: post
title: UIView and InterfaceOrientation in UIViewController Guide
date: 2012-12-27 14:21:04
categories:
    - 
tags:
    -
---

##startup

**1:5.0** frame = (0 20; 768 1004); autoresize = W+H; layer = <CALayer: 0x6a5ab50>> bounds:{{0, 0}, {768, 1004}


* viewDidAppear 

**2:6.0** frame = (20 0; 748 1024); transform = [0, -1, 1, 0, 0, 0]; autoresize = W+H; layer = <CALayer: 0x715dd10>> bounds:{{0, 0}, {1024, 748}} **6.0**


##rotation


* willRotateToInterfaceOrientation:duration:

frame = (20 0; 748 1024); transform = [0, -1, 1, 0, 0, 0]; autoresize = W+H; layer = <CALayer: 0x715dd10>> bounds:{{0, 0}, {1024, 748} **6.0**

**1 --> 3** frame = (0 20; 768 1004); autoresize = W+H; layer = <CALayer: 0xcd0c2c0>> bounds:{{0, 0}, {768, 1004}} **5.0**

**3 --> 4** frame = (0 0; 748 1024); transform = [0, 1, -1, 0, 0, 0]; autoresize = W+H; layer = <CALayer: 0xcd0c2c0>> bounds:{{0, 0}, {1024, 748}} **5.0**


* willAnimateRotationToInterfaceOrientation:duration:

frame = (0 20; 768 1004); autoresize = W+H; animations = { transform=<CABasicAnimation: 0x944fab0>; position=<CABasicAnimation: 0x9451440>; bounds=<CABasicAnimation: 0x94515f0>; }; layer = <CALayer: 0x715dd10>> bounds:{{0, 0}, {768, 1004}} **6.0**


frame = (0 0; 748 1024); transform = [0, 1, -1, 0, 0, 0]; autoresize = W+H; animations = { transform=<CABasicAnimation: 0x6812700>; position=<CABasicAnimation: 0x6839480>; bounds=<CABasicAnimation: 0x6839a10>; }; layer = <CALayer: 0xcd0c2c0>> bounds:{{0, 0}, {1024, 748}} **5.0**


**1 --> 4** frame = (20 0; 748 1024); transform = [0, -1, 1, 0, 0, 0]; autoresize = W+H; layer = <CALayer: 0xc52ca50>> bounds:{{0, 0}, {1024, 748}} **5.0**



* didRotateFromInterfaceOrientation:

frame = (20 0; 748 1024); transform = [0, -1, 1, 0, 0, 0]; autoresize = W+H; layer = <CALayer: 0xcd0c2c0>> bounds:{{0, 0}, {1024, 748}} **6.0**

frame = (0 0; 748 1024); transform = [0, 1, -1, 0, 0, 0]; autoresize = W+H; layer = <CALayer: 0xcd0c2c0>> bounds:{{0, 0}, {1024, 748}} **5.0**

###########

* shouldAutorotateToInterfaceOrientation:

