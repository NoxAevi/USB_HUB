---
title: "TBD"
author: "NoxAevi"
description: "USB hub"
created_at: "2026-07-18"
---

# 7/23/26: Where to go from here

After reflecting on both this project and the project I did before, I've realized that I've been trying to do too many new things at once

For example, in this project, it'd be my first time implementing USB SS (which I somewhat understand) as well as my first time exploring USB PD (which still confuses me quite a bit)

It'd be a bummer if I finished this project only to find that everything except something like USB PD works (and since a lot of the parts, like the high-speed usb hub controller cost a lot, it'd be difficult to reassemble it & more expensive for revisions)

Thus, I'm going to be placing this project on hold and making a MUCH smaller scale one

What I plan to do is to make a barebones implementation of USB PD passthrough with a USB hub (so three ports: 1 for host, 1 for charger/data, 1 for data)

I also did some more (re)research on PD controllers during this time, mainly the ones by TI (i mean they had the largest selection by far) to see if I could do anything with them

Looking at their USBCPD software application (yeah USB PD seems complicated enough that it warrants using stuff like this to program it -- ouch), it seems that I might be able to set up something to auto negotiate as a sink as well as trigger a GPIO event to communicate with the host-side (source) PD controller to determine what it should advertise to the host

<img width="1201" height="175" alt="image" src="https://github.com/user-attachments/assets/eb83a11c-3e3f-45b7-8fe3-7efbd989d29e" />

<img width="1213" height="235" alt="image" src="https://github.com/user-attachments/assets/5f5cd2ac-681a-488e-8c2a-cc1d91dff91f" />

(These both are on different application notes for some reason, but wasn't too hard to find)

Anyways, before moving on to the other project, I'll email TI to confirm if using this would work for my application

**Total Time Spent:** 30m

# 7/21/26: Quick update on MCP22301

<img width="1266" height="220" alt="image" src="https://github.com/user-attachments/assets/d7a17c2a-ee7e-43ef-adce-258f36778beb" />

AKA this chip won't really work (though I pretty much already decided to redrive USB PD)

**Total Time Spent:** 5m

# 7/21/26: USB PD passthrough research

On some time that I didn't track, I did some research about having the charging configuration in the design, and it seemed that the one in the USB hub IC only supports up to 5V (and that's kinda low for a laptop)

Thus, I began looking at USB PD controllers on their website, and found the MCP22301, which has something pretty similar to what I need

<img width="693" height="694" alt="image" src="https://github.com/user-attachments/assets/a4d99bf4-07f2-4174-8681-9ff98e238e59" />

However, according to a [reddit post](https://www.reddit.com/r/microchip/comments/1p35evk/power_delivery_stack_hardware_firmware_support/), they've had mid support and haven't been able to provide precise instructions on how to implement this charge-through feature

(This is just the summary of what I determined, but did quite a bit of searching outside of that)

For now, I'll be looking into TI PD controllers

Most of the ones I looked at were for automotive uses (and since you can't really charge a car via usb-c, none of the intended applications were even close to matching)

I did find the TPS65982DMC, which is really close to what I'm looking for

However, I'd have to lose one of the ports entirely and use a barrel jack for power (not to mention that the footprint is BGA, so I'm basically locked into jlcpcb parts [and the parts I'm using don't really have huge stocks, so they might run out], meaning I'd have to at least buy twice of everything too)

<img width="1239" height="712" alt="image" src="https://github.com/user-attachments/assets/d5da9917-f40b-4ef5-94da-4956f457345d" />

I then looked into Analog Devices and found nothing of note that could be used for this

Same for infineon (most of their stuff uses another mcu to communicate what standard, though this does give me the idea of using an MCU/PD detector IC [if those exist] and then use that to communicate a standard with the PD IC, though if possible I'd like to avoid this)

After more searching, there really isn't anything that fit my criteria

After looking a bit on reddit, I found [this](https://medium.com/@kolluru.nathan/usb-pd-power-reserve-and-you-71cf4d18505c) article, which gave an interesting idea about redriving PD after taking in PD power (this is similar to the idea before, but I'd in theory be asking for the highest voltage the charger can provide, then stepping it down into a system voltage, and supplying it to a USB PD controller which then negotiates with the host device)

<img width="1071" height="285" alt="image" src="https://github.com/user-attachments/assets/74b0729e-c94d-447a-91dd-c45afb15b425" />


Since the article also mentions stuff like being able to only have a specific amount of delay, the first option (of using an MCU to act as the middleman) is much more risky (as there's a chance of it being too slow). Thus, I'll be using the second option (from the article)

This means that next session I'll have to find:

* A way to determine which side is the host
* A way to determine PD capabilities on the charger side (this doesn't need to be a data port anymore, so i can have 2 usb c ports [yippee!!])
* A way to negotiate with the host what voltage they would like to receive (at most one voltage step under the supply, accounting for power used in the dock itself, but i gotta research this more to know how strict this is)
* Make sure I don't forget to set the other ports properly (downstream, DP, HDMI, etc.)

**Total Time Spent:** 2h

# 7/18/26: Initial Research (USB HUB IC found, but still confused)

The obvious first step in designing a USB hub would be to decide what ports I wanted it to have, and I eventually decided on these

<img width="330" height="391" alt="image" src="https://github.com/user-attachments/assets/09916148-4f3a-4161-92c1-b0fe34d2f7e3" />

I don't really know yet if I need to include the power port as one of the data ports or not, but this is the port that I'd be most willing to let go if I had to

Following this, I started searching for a USB hub IC that I could use on LCSC, and came upon a lot of stuff by microchip, including the USB7206

Thus, I went onto microchip's website to see their other listings

In order to fully include the 7 ports that I want, I would have to accept a 5 gbps speed instead of 10gbps

<img width="1419" height="186" alt="image" src="https://github.com/user-attachments/assets/5975ccef-0773-4817-88b4-a3d8a1d0bde4" />

It also turned out to be a bit cheaper than the USB7206

<img width="1032" height="136" alt="image" src="https://github.com/user-attachments/assets/814ebc41-5866-4710-8e85-0ca26ea74521" />

At this moment, I thought back to the display options, and was considering making them both be one type of port instead

When looking at other usb hubs, like dell, I saw that they used both types of ports, so that's probably going to be the route that I'm going to take

When looking at the documentation, it seems that the controller does have support for charging, but it'd have to be one of the data ports (though it can be used for data while charging or data when not charging)

Then, I noticed that the IC seems to have more than 7 ports (there's 7 usb 3 downstream and more usb 2 downsteam), which I believe is a misunderstanding on my part

When scrolling further, it appears that a MUX is used to swap the port from usb 3 to usb 2 / other way around

<img width="1144" height="690" alt="image" src="https://github.com/user-attachments/assets/195e4b33-1a41-4c98-9cbc-33fb9a9cd3eb" />

I was still pretty confused, so I did some research, and I still don't know why I specifically need to include this only for USB-C (since USB A can also have the same speeds)

Anyways, I found a potential MUX I could use for the USB-C ports (HD3SS3220)

Using flex connect is probably how I can get the power port and connection to laptop to be able to be swapped, but it's still a bit confusing due to how there are different connections for a USB-C upstream and downstream port based on the diagrams
I'd also need a way to determine which port is being used as the upstream port, since the flex connnect isn't automatic

<img width="1179" height="672" alt="image" src="https://github.com/user-attachments/assets/fa104c31-4a89-4f53-bd39-071d651c1c40" />

I was then going to make a diagram, but decided against it because it'd probably be best to see how easy/hard it'd be to route (since I'd need additional parts to handle the eth/display to usb)

Finally, I did some research on why USB-C ports need a mux, and it's mainly due to how the when swapping the orientation of a USB-C plug, it swaps the RX/TX lines (differing from how nothing extra is needed for USB 2)

Before moving on, I believe it'd be best to figure out how exactly I'd be able to implement the swapping between upstream/downstream and how I'd implement charging

**Total Time Spent:** 2h
