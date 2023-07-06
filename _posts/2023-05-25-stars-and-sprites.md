---
layout: post
title: 👾 Stars and Sprites
categories: blog projectfreedom
---

It's been a minute since the last update on **Project Freedom**. I usually end each post with some screenshot showing the current state of the project but this time I'll open with it instead because...well...it just looks so pretty:

<div style="text-align: center;">
    <img alt="Analogue Pocket showing a starfield and some sprites spelling pfx-1" src="{{ "/assets/blog/2023-05-25/starsnsprites.mov" | relative_url }}" width="300"> 
</div>
<br>

As you can see things have been moving forward nicely with the core but, as usual, there's actually been a few side projects spawned along the way.

First I decided to start gathering all the information I got about **openFPGA** development and start a wiki and series of tutorials. It's called [openFPGA Tutorials](https://github.com/DidierMalenfant/openFPGA-tutorials). I'd been keeping my notes locally as I went along and I quickly realized that this information was scattered all around the internet, sometimes hidden behind un-searchable paywalls. So I thought it would be a good way to make all this info available in one place and keep adding to it as I go. There's a wiki, a discussion forum and some sample code that can get people started easily.

### Beefing up the toolchain

In order to make the sample code super easy to use, I needed to simplify the requirements needed to build a core. Up to now I'd been remote logging into a **Windows** or **Linux** machine, copying the project's source code in order to run **Intel**'s tools remotely and then copying the results back onto my dev machine. Not ideal...

Marcus Andrade, aka **Boogermann** a member of the [FPGaming discord](https://discord.gg/k6DEJ89W), came to the rescue with an awesome solution. He put together **Docker** images that include all the **Quartus** tools running on a **Linux** image, allowing you to build **FPGA** bitstream and more importantly to include this into a local build process. Even better, and thanks to the incredible technologies behind **Docker** and Apple's **Rosetta**, you can run this on an **Apple Silicon** Mac too. Mind literally **blown** 🤯. If you want to learn more about this, I wrote a [post](https://didier.malenfant.net/blog/nerdy/2023/04/17/Using-Quartus-on-macOS.html) which talks about the **Apple Silicon**-compatible images I put together based on **Boogermann**'s ones.

Next, I needed to beef up the tool chain I use to build my core and make it easily available to people who are not using any of my **pfx-1** stuff. Since the toolchain is mostly written in **Python** I converted that to a **Python** module and published it on [PyPI](https://pypi.org/project/pf-fpga-tools/). This includes command line tools to reverse a bitstream, edit **Quartus** config files, package a core and convert images for example. Those are not completely cross-platform yet but it's a start.

Finally, since I still don't want to distribute **Intel**'s IP as part of other projects, I tweaked the fork of **Analogue**'s core template repo to fit this new build pipeline a little bit better and make it more versatile. The new [pfCoreTemplate](https://github.com/DidierMalenfant/pfCoreTemplate) repo is now automatically cloned into the build folder for each project. Its project's `qsf` file is edited by the build tools to automatically add any source files used by the project and then the bitstream is built locally using a **Docker** image.

What this results in is a one-step/one-line make process that does everything, even install the core on an SD card, which means I can develop and build everything from my editor which is really nice.

I'm not going to delve too much on the code behind the current version of **pfCore**. Short version is that pretty much everything is split up into separate modules now and the coding standard has greatly improved now that I understand more about best practices. As you can see above there are some hardware sprites there and even a little co-processor which can change the sprite color based on the vertical position of the beam. Some of this will be useful in the near future (I plan on using those sprites as debugging display for the core for example) but the main idea was to get more and more comfortable with real code and learn a bit more **SystemVerilog** syntax along the way. You can find a better description as to what is going on in the [README](https://github.com/DidierMalenfant/openFPGA-tutorials/tree/main/examples/03_StarsAndSprites) for a sample in the tutorials which pretty much covers the same thing.

### The (future) specs

I wrote about the general intentions behind the project before but I hadn't put down exactly what I expect the specs to be. So I went ahead and did just that. It will be interesting to see how much of this sticks in the final version:

**PFX-1** console:
- **MC68000** processor.
- Custom '**Flip**' graphics chip.
- **400x360** resolution. 2x-scaled.
- Fixed number of hardware sprites per scan line. 
- 5 tile map planes.
- Fixed number of 256 colors palettes. Shareable between sprites and tile maps.
- 32-bit RBGA (8888) color entries. Support for translucency on both tile maps and sprites.
- 8 voice custom sound chip.

The resolution is full screen on the **Pocket**. I've already tweaked the core to use that exact display mode with a 10x9 aspect ratio. The 2x scale factor is mainly because I want to both save some bandwidth for the graphics chip and also retain a little bit of a retro look for the console.

For the SDK I'm shooting for:

- [C17](https://en.wikipedia.org/wiki/C17_(C_standard_revision)) based **SDK**.
- **MC68000** assembly supported too.
- No user-facing memory allocations. Rely on memory pools as much as possible.
- Index-handles instead of pointers.
- Fixed-point math.

I was really inspired by **Andre Weissflog**'s [post](https://floooh.github.io/2019/09/27/modern-c-for-cpp-peeps.html) about modern **C** development. I've never been a fan of **C++** and getting back to **C** feels so nice and simple in comparaison. Fixed point makes sense since I'm using a stock **68000** with no **FPU**. The memory allocation thing will be interesting. I definitely wants anything coming from the **SDK** to not be memory allocated, i.e. to not need to be managed by the end-user. We'll see how that goes...

Meanwhile, down to the nitty-gritty bits, I also needed to start connecting the various parts of the future hardware and make decisions as to how/which busses will interconnect everything:

<div style="text-align: center;">
    <img alt="Preliminary block diagram of the pfx-1 fantasy console" src="{{ "/assets/blog/2023-05-25/blockdiagram.png" | relative_url }}" width="500"> 
</div>
<br>

This is the area where I know the least for the time being so I could end up being way off. The idea for the time being is to reduce bus-contention by cleanly separating all the different parts. The graphics chips uses its own local **VRAM** so while the **CPU** is putting together the next frame, it has free range to draw the current one in sync with the video beam. All sprites and tile map graphics will already be in **VRAM**, only updates to the sprite and tile map lists will be **DMA**'ed during **VBLANK**.

I imagine the graphics chip will have some sort of a 'reverse' **DMA** mode where rendering will be paused and the **MMU** chip can upload some data directly into the **VRAM**. This needs to be more fleshed out but it would be used to load sprites and tiles from the ROM for example.

The graphics cache will be used to cache color palettes and buffer entire 2 screen lines for sprite rendering.

This diagram was also a good exercise at assigning some of memory chips found in the **Pocket** and locking down bus sizes between all the different chips.

It's getting exciting!

If you want to follow along, here is the repo for [pfCore](https://github.com/DidierMalenfant/pfCore). This week's progress is at commit [9f33ee3](https://github.com/DidierMalenfant/pfCore/commit/9f33ee3a5507c2e406ee0162c23a847c5a6fae59).

With ❤️ from Paris, France.
