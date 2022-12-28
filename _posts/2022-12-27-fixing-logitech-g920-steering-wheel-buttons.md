---
author: Philip Hendry
title: Fixing the Logitech G920 Steering Wheel Buttons
date: 2022-12-27 12:38
comments: true
layout: post
tags:
- hobbies
---

I've been using the Logitech G9200 steering wheel on my PC for the last 4 years and find it a great budget wheel to use but it's not without fault. After about 3 years of use the cheap potentiometers in the pedals were wearing down and gave "jumpy" signals which caused spinning out of corners due to brake input being applied randomly! Spraying contact cleaner in to the pedals helped a bit but ultimately I replaced the potentiometers with an [upgrade kit from AXC-Sim](https://www.axc-sim.com/product/potentiometer-kit-for-logitech-g-series-pedals/) and after almost a year use these are proving great so far.

The latest issue however, and the reason for this post, is I had noticed failing paddle gear changes whilst cornering! I used the [Game Controllers](https://support.microsoft.com/en-us/topic/how-to-troubleshoot-game-controllers-in-microsoft-games-ee731224-b2d7-f25e-3c2a-338b27dbe1f5) tool in Windows to confirm this for certain and whilst performing my tests I noticed that the other buttons also stopped working although pressing the "A" button caused a lot of other buttons to be detected as pressed! At this point I knew there was a break in a cable and during this testing it got worse and ultimately broke permanently as the buttons stopped working entirely.

The instructions below detail the process I went through in order to fix this issue including links to the parts I used to make the fix.

## Remove the steering wheel

First remove the five small black screens from behind the steering wheel. The first is located at the bottom:

![image-20221227125824710](.images\image-20221227125824710.png)

There are two behind each gear paddle:

![image-20221227125745850](.images\image-20221227125745850.png)

Now remove the six bolts from the front after which the plastic disc can be removed:

![image-20221227130239156](.images\image-20221227130239156.png)

The wheel should now pull away from the base with just one connector that should now be removed:
![image-20221227130422138](.images\image-20221227130422138.png)

The picture above shows my new cable in place. The original used thinner cables and were wrapped with shrink wrapped tubing. When I had disassembled the wheel I could see the shrink tubing had split and the orange wire had snapped and was now protruding through the split.

To remove the final piece of the wheel remove the three screws highlighted below and pull the piece away from the base:

![image-20221228093625618](.images\image-20221228093625618.png)

## Opening the wheel base

The next stage is removing the top of the wheel base of and this requires flipping the base over and removing the eight screws indicated below (with a couple hidden in this photo by the clamps). When removing the top the clamps will have to be loose but once removed clamp the base unit back on to a desk to make it easier to work with:

![image-20221228093943119](.images\image-20221228093943119.png)

I store my wheel under my desk and therefore I've cable tied the many cables together and to the clamps to make them more manageable when setting up.

The cable to replace travels under the circuit board and up through the centre of the wheel. It's therefore much easier to remove the circuit board and frame before trying to fit the new cable. Unplug all the cables, remove the four screws holding the circuit board, then finally remove the four screws holding the frame:

![image-20221228094551262](.images\image-20221228094551262.png)

Unfortunately I don't have a photo at this point showing how the wheel looks without the circuit board in place but essentially it makes it much easier to pass the ends of the new cable up through the centre of the wheel.

## Components required for the repair

Rather than try to repair the broken cable I thought it best to replace it with a new one which would mean the next time it breaks I can repair it in the same way. I had contacted Logitech support to enquire whether they hold stock of spare parts but was very disappointed to hear that they didn't stock the cable even though it must be a very common failure for this wheel!

Instead I identified the plug on the end of the cable as a *JST PH 2mm 7-pin* and found [cables on eBay](https://www.ebay.co.uk/itm/292920313930) that had one end pre-made and at 30cm long are the perfect length:

![image-20221228101038435](.images\image-20221228101038435.png)

The advantage of having only one end of the cable pre-made is feeding the cable through the small centre hole in the steering column would be possible. However this also meant the other end would have to be added manually after feeding through and require the purchase of the connectors, again, [from eBay](https://www.ebay.co.uk/itm/232383863731), and choosing the 7-pin version in a pack of five:

![image-20221228101402756](.images\image-20221228101402756.png)

## Fitting the new cable

Ultimately I cut the old cable and removed it from the wheel base but before I did that I practised making up one cable, plugged it in to the circuit board and wheel without feeding through the steering column, and tested the cable whilst the wheel was still disassembled to confirm I could make it all work.

Having confirmed I could make a cable successfully, I cut away the old cable and began making another new one. It's much easier to crimp the new metal ends on the cable then feed it through the steering column from the inside such that you're left with this:

![image-20221228102016609](.images\image-20221228102016609.png) 

The metal ends can then be pushed into the plastic socket paying attention to copy the same pin-to-pin colour coding in the old cable. I should point out at this point that I chose not to re-fit the magnet fitted to the old cables - I'm unsure what the effect of doing this will be but the wheel seems to work perfectly well for my needs at least!

## Reassembly

All that's left at this point is to follow the steps for disassembly in reverse then test. I'm unsure how long this fix is going to last but at after a few days it continues to work perfectly.

I hope you've found these instructions useful and can continue enjoying driving with the G920.