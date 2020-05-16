+++
title = "Longan Nano with ft2232h"
date = "2020-05-16T18:03:00-04:00"
tags = ["risc-v", "gd32vf103", "openocd", "ft2232h"]
description = "Adventures using PlatformIO with the Longan Nano and an ft2232h minimodule"
draft = false
+++
I recently purchased an RISC-V development board called the Longan Nano, which is based on the GD32vf103C family from GigaDevices. RISC-V is an open source ISA which seems to be getting some traction. Cheap dev boards are becoming available. There is apparently [Arduino support](https://github.com/sipeed/Longduino), but I couldn't get it to work properly in the arduino IDE. When installing the board support, there was a java error that the compiler wasn't available for my operating system. Glancing at the issues for Longduino, it looks like similar errors are common with no response from the author.

The good news is that if you are willing to try [PlatfromIO](https://platformio.org/), there is good support for the [Longan Nano](https://docs.platformio.org/en/latest/boards/gd32v/sipeed-longan-nano.html). As a grumpy old unix guy, I'm a fan of EMACS. The Arduino IDE, while it's simple to get started with, fits like a shoe that's too small. PlatformIO has [EMACS integration](https://docs.platformio.org/en/latest/integration/ide/emacs.html), so I went about setting it up. The docs are pretty good, and soon I was able to happily compile RISC-V blink examples.

I looked into how to connect my PC to the Longan Nano, and that's where the trouble began. The simplest ting to try is the `dfu` upload_protocol and plug in the usb cable! In order to initiate the upload, you need to hold down the boot and reset buttons, then release reset, then release boot. That puts the device in dfu mode. I was mostly able to upload with dfu, but there isn't a way to debug, and I was getting inconsistent upload results.

Looking at the [supported upload methods](https://docs.platformio.org/en/latest/boards/gd32v/sipeed-longan-nano.html#uploading), I see that I don't own any of those devices, but I do own an ft2232h minimodule (or the equivalent). I was able to get the minimodule working by adding it to `platforms/gd32v/platform.py`. Below is the diff:

```
~/.platformio $ diff -u platforms/gd32v/platform.py.dist platforms/gd32v/platform.py
--- platforms/gd32v/platform.py.dist	2020-05-16 19:09:45.477399402 -0400
+++ platforms/gd32v/platform.py	2020-05-16 15:51:29.865143998 -0400
@@ -30,6 +30,7 @@
             "sipeed-rv-debugger",
             "altera-usb-blaster",
             "um232h",
+            "minimodule",
             "rv-link"
         ]
 
@@ -80,7 +81,7 @@
                 "-f", "target/gd32vf103.cfg"
             ]
             server_args.append("-c")
-            if link in ["um232h"]:
+            if link in ["um232h", "minimodule"]:
                 server_args.append("adapter_khz 8000")
             else:
                 server_args.append("adapter_khz 1000")
```
Once this is done, I can add (or change) a couple of lines in my platform.ini:
```
debug_tool = minimodule
upload_protocol = minimodule
```
and make some connections to my Longan Nano:

|ft2232h |Longan Nano|
|-----------|-----------|
|3.3v|3.3v|
|GND|GND|
|AD0|JTCK|
|AD1|JTDI|
|AD2|JTDO|
|AD3|JTMS|

With all of this setup, I can now run my upload with `platformio run --target upload`. I get a funny notice at the end of the upload:
```
** Programming Started **
** Programming Finished **
** Verify Started **
** Verified OK **
Info : Hart 0 unexpectedly reset!

*** [upload] Error 1
================================================== [FAILED] Took 1.43 seconds ==================================================
```
Pushing the reset button doesn't seem to start the program, but when I disconnect and connect the power, the led starts blinking.

One of the sharp edges that I tripped over was the fact that the RGB led is connected to the power rail instead of ground like it is on the Arduino. This means that setting the LED pin to LOW turns on the LED.

Now that I've done all this hacking about in platformio, it's probably time to make up a couple of PR's so that hopefully, other folks won't need to do all of this discovery themselves.
