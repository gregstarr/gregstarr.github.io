---
title: Simple auto bed leveling sensor for Ender 3
author: gstarr
categories:
  - 3D Printing
date: 2019-08-23T07:57:26.391Z
thumbnail: /assets/uploads/ender3_carriage.png
---
Notes from the design proces of my bed leveling sensor mount.

![carriage](/assets/uploads/ender3_carriage.png) 

I based the design on something I saw on 
[Thingiverse](https://www.thingiverse.com/thing:3293821).
I liked the design because it was very simple. It consists of only 2 pieces, one to hold
the servo (SG90) to the carriage and an arm to deploy the end switch using the servo. 
The arm is braced against the fan shroud to add some extra stability. Originally
I thought I would try something more complicated, which is why I started designing 
my own in the first place. Ultimately, I basically copied the design for myself. I
am glad I did though because it was good practice using Fusion 360. 

Because the Ender 3 is open source, there are 
[3d models](https://github.com/Creality3DPrinting/Ender-3) of it online which you can 
import into Fusion 360 and design off of. 

I used the projection tool to create holes to perfectly match the placement and diameter 
of holes on the carriage. Then I found a 3d model for the servo I had and used it to
make sure everything would fit together. The offset tool is great for adding a little 
extra space here and there to allow for some tolerance.

![with_servo](/assets/uploads/servo_mount_with_servo.png) 
![without_servo](/assets/uploads/servo_mount.png)
