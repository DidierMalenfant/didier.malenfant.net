---
layout: post
title: 👾 Building a core
categories: blog projectfreedom
---

Now that we have a fairly decent toolchain to compile target code with and a simple simulator to simulate real hardware, it's time to bite the bullet and start looking at implementing the actual core  on Analogue's [OpenFPGA](https://www.analogue.co/developer) platform. This is the area that I know the least about. In fact, I'm truly starting from scratch as I know next to nothing about FPGAs or the Verilog language that is used to develop on them. Exciting times ahead indeed...

### Describing the hardware

FPGA chips are programmed using what is called a hardware description language. This is basically a way to describe how the gates are arranged in your design to make the whole thing work. Unlike a typical programming language, you're not describing how a program works in a sequence (do this, then do that, etc...) you're describing how things are connected together and how it will react to the signals it receives when it's put to work.

Again, even when describing how your chip reacts to external stimuli, you're still NOT describing a sequence of events. Some or all of these things may take place all at once if your chip is mostly combinatory. It takes some getting used to when coming from a coding background.

### Verilog

The hardware description language used on the OpenFPGA platform is called [Verilog](https://en.wikipedia.org/wiki/Verilog). The development environment recommended by Analogue is [Intel Quartus Prime](https://www.intel.com/content/www/us/en/products/details/fpga/development-tools/quartus-prime/resource.html) which is 100% proprietary. Now there are some open-source Verilog compilers, for example [Icarus Verilog](https://github.com/steveicarus/iverilog), but the issue here is that I'm not sure that it can actually produce bitstream file compatible with the Analogue Pocket. While I'm sure it can compile most of the Verilog files used to develop the core, part of the codebase for the core is what is know as IP files which help the compiler know what it is targeting. For example which type of FPGA chip (an Intel Cyclone V in our case), or what it is interfaced to externally (memory chips, PLLs, etc...). There are what is known as constraints files in a Verilog project which help configure all this.

Long story short, I'm not in a place knowledge-wise right now to try and get all this stuff to work on the Pocket from scratch using an open-source environment. So in the interest of baby-stepping my way into this, I decided to go ahead and use Intel's tools for the time being.

### First baby step

Analogue provides a [template](https://github.com/open-fpga/core-template) for core developers and this template actually contains a compiled core, complete with the required configuration files and a bitstream file. Step number one was therefore trying to install that core onto my Pocket and making sure that works.

Cores on the pockets use a specific file folder structure:
```
    Cores/
    ├─ <AuthorName>.<CoreName>/
    │  ├─ audio.json
    │  ├─ data.json
    │  ├─ info.txt
    │  ├─ interact.json
    │  ├─ variants.json
    │  ├─ core.json
    │  ├─ icon.bin
    │  ├─ input.json
    │  ├─ <CoreName>.rbf_r
    │  ├─ video.json
    Plaftorms
    ├─ _images/
    │  ├─ <CoreName>.bin
    ├─ <CoreName>.json
```

Copying all this in the right spot on the the SD card makes the core show up in the OpenFPGA menu on the Pocket and, since I didn't modify anything yet, it works as expected and displays a grey screen.

### Customizing the core

Before we get into modifying the core's source code itself, 

https://toml.io/en/

```
[Platform]
name = "pfx-1"
image = "assets/pfx1-platform-image.png"
# Core short names shall only contain lowercase alphanumeric characters (a-z, 0-9, underscore _) and have a maximum length of 15 characters.
# The first character of the name may not be an underscore.
short_name = "pfx1"
category = "Fantasy"
description = "An open-source fantasy gaming console for the Analog Pocket."
info = "info.txt"

[Build]
version = "0.0.2"

[Author]
name = "ProjectFreedom"
icon = "assets/pfx1-core-author-icon.png"
url = "https://projectfreedom.io"

[Bitstream]
source = "$PF_POCKET_CORE_DEV_DIR/src/fpga/output_files/ap_core.rbf"

[Video]
width = 320
height = 240
aspect_w = 4
aspect_h = 3
rotation = 0
mirror = 0
```

### Intel Quartus Prime

Intel's IDE is available on Windows and Linux but unfortunately not on macOS and only for x86 architectures. Since I'm using an Apple Silicon Macbook, running an x86 VM is out of the question. So I decide to dust up one of the old mini-PCs I used for CI on an older project and re-install Windows on it. The idea is to remote-desktop into this machine and use it to compile the core project until I can find a better solution.





https://projectf.io/posts/fpga-graphics/

https://projectf.io


https://en.wikipedia.org/wiki/SystemVerilog



If you want to follow along, here are the repos for the [pfCore](https://github.com/DidierMalenfant/pfCore). This week's progress is at commit [662605f](). You can also follow my current [tasklist](https://trello.com/b/ihs3W4ux/%F0%9F%91%BE-project-freedom) on Trello.

With ❤️ from Paris, France.
