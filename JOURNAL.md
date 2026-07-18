---
title: "TBD"
author: "NoxAevi"
description: "USB hub"
created_at: "2026-07-18"
---

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
