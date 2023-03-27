---
layout: post
title: 👾 Building a core
categories: blog projectfreedom
---

Now that we have a fairly decent toolchain to compile target code with and a simple simulator to...well...simulate real hardware, it's time to bite the bullet and start looking at implementing the actual core  on **Analogue**'s [**OpenFPGA**](https://www.analogue.co/developer) platform. This is the area that I know the least about. In fact, I'm truly starting from scratch as I know next to nothing about **FPGA**s or the **Verilog** language that is used to develop on them. Exciting times ahead indeed...

### Describing the hardware

**FPGA** chips are programmed using what is called a hardware description language (or **HDL**). This is basically a way to describe how the gates are arranged in your design to make the whole thing work. Unlike a typical programming language, you're not describing how a program works in a sequence (do this, then do that, etc...) you're describing how things are connected together and how it will react to the signals it receives when it's put to work. Some or all of these things may take place all at once if your chip is mostly combinatory so it takes some getting used to when coming from a coding background.

### First baby step...

**Analogue** provides a [template](https://github.com/open-fpga/core-template) for **OpenFPGA** developers and this template actually contains a compiled core, complete with the required configuration files and a bitstream file. Step number one was therefore trying to install that core onto my **Pocket** and make sure that it works.

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

Copying all this in the right spot on the the SD card makes the core show up in the **OpenFPGA** menu on the Pocket and, since I didn't modify anything yet, it works as expected and displays a grey screen.

### Packaging the core

As I mentioned above, all the files required to build a core for the **Analog Pocket** have a specific directory structure to follow but also a specific format and some required information. Some of that information, like the core name itself, is also used in a naming convention for some of the file. This feels highly error prone, so much so that people apparently already started writing validators for all this to catch potential errors. I was just about to write one such validator when I realized that a better approach was probably to write a core packaging tool which would take care of generating all these files automatically.

This turned into a few tools actually. First, you may notice that the file extension for bitstream file in the core package is `rbf_r`. The reason for this is that the Analogue Pocket doesn't read the `rbf` bitstream files that are generated from the **Verilog** source but instead requires a reversed bitstream file, therefore an `rbf_r` file. So I first wrote a quick `pfReverseBitstream` Python script to make that conversion part of the build process.

Second, images also have a specific format which involves them being converted to greyscale and being rotated 90 degrees counter-clockwise. So I also wrote a `pfConvertImage` script to take regular image files and convert them to the `bin` format required.

Finally, I wrote a `pfBuildCore` script to generate all the json files required by the core, convert the bitstream and the images and package all those into a zip file with the correct directory structure. This script uses a [toml](https://toml.io/en/) config file like this one to drive this process:

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

You may notice that one entry uses an environment variable `PF_POCKET_CORE_DEV_DIR`, we'll get to that a bit later...

After creating some assets for the **pfx-1** core and quickly writing a `pfInstallCore` script which takes a zipped up core and installs all the files in their correct location on an SD card, it was time to give it a whirl:

<div style="text-align: center;">
    <img alt="Analog Pocket Core info screen" src="{{ "/assets/blog/2023-03-27/CoreInfo.png" | relative_url }}" width="300"> 
</div>
<br>

Success! (Although the assets here were incorrectly converted to black and white instead of greyscale but let's ignore that, it's been fixed since...)

### Intel Quartus Prime

The hardware description language used on the **OpenFPGA** platform is called [**Verilog**](https://en.wikipedia.org/wiki/Verilog). The development environment recommended by **Analogue** is [**Intel Quartus Prime**](https://www.intel.com/content/www/us/en/products/details/fpga/development-tools/quartus-prime/resource.html) which is 100% proprietary. There are some open-source **Verilog** compilers, for example [**Icarus Verilog**](https://github.com/steveicarus/iverilog), but the issue here is that I'm not sure that it can actually produce bitstream file compatible with the Analogue Pocket.

While I'm sure it can compile most of the **Verilog** files used to develop the core, part of the codebase for the core is what is known as **IP** files which help the compiler know what it is targeting. For example which type of **FPGA** chip (an **Intel Cyclone V** in our case), or what it is interfaced to externally (memory chips, PLLs, etc...). There are constraints files in a **Verilog** project which help configure all this. Long story short, I'm not in a place knowledge-wise right now to try and get all this stuff to work on the Pocket from scratch using an open-source environment. So in the interest of baby-stepping my way into this, I decided to go ahead and use Intel's tools for the time being.

**Intel**'s IDE is available on Windows and Linux but unfortunately not on **macOS** and only for x86 architectures. Since I'm using an **Apple Silicon** Macbook, running an x86 VM is out of the question. So I decided to dust up one of the old mini-PCs I used for CI on an older project and re-install Windows on it. The idea is to remote-desktop into this machine and use it to compile the core project until I can find a better solution.

In order to avoid distributing files that cannot be license under the **GPL-V3** or later, I decided to fork **Analogue**'s template project and use it as a host to compile my core's **Verilog** files into a bitstream file, the result of which can then be imported back into my core project. The workflow is this: Edit the **Verilog** files, copy those over to the **Verilog** compilation host project (`make update` takes care of that in my **Makefile** using the `PF_POCKET_CORE_DEV_DIR` variable that we saw earlier), remote desktop to the **Windows** machine which hosts this project, compile the **Verilog** project, back on the core project run make to copy the resulting bitstream file and package the core 🥵.

At least this is all automated but it is still a little convoluted. I'm hoping that I can put together **Quartus** on a **Linux** machine in order to be able to build the core from scratch in only one step on the same machine.

### Writing my own Verilog code

It was finally time to try and write some real code and see the results on screen. My first attempt to make sure the whole toolchain worked was to change the color used to draw on the screen in **Analogue**'s template file:

<div style="text-align: center;">
    <img alt="Analog Pocket displaying an orange screen" src="{{ "/assets/blog/2023-03-27/OrangeScreen.png" | relative_url }}" width="300"> 
</div>
<br>

Second step was to separate the graphics part of the design into into its own file (and it own module in Verilog-speak) and switch that file to using the `.sv` extension to make sure I can eventually use [**System Verilog**](https://en.wikipedia.org/wiki/SystemVerilog)'s improved syntax over regular **Verilog**.

Then it was time to start having some fun. Fun tutorials for **Verilog** are hard to find but I stumbled across the most amazing ones from **Will Green**'s smartly named [**Project F**](https://projectf.io). They are short, start pretty much from scratch and all end up focusing on games and demos which is perfect for me. After reading some of the early articles I decided to try and port one from [this](https://projectf.io/posts/fpga-graphics/) post. It's a very simple example of changing the pixel color based on the current position of the raster-beam. This is also a perfect leg-up for what I'll be eventually looking at which is to recreated the simulator's graphics chip which has to clear the screen based on a color provided in its registers.

Here is the resulting module for `flip`, my future graphics chip (named in honor of the **N64**'s graphics chip `flipper`):

```
module pf_flip (input   wire        reset_n,
                input   wire logic  clk_core_12288,
                output  wire [23:0] video_rgb,
                output  wire        video_de,
                output  wire        video_skip,
                output  wire        video_vs,
                output  wire        video_hs);

assign video_rgb = vidout_rgb;
assign video_de = vidout_de;
assign video_skip = vidout_skip;
assign video_vs = vidout_vs;
assign video_hs = vidout_hs;

    localparam  VID_V_BPORCH = 'd10;
    localparam  VID_V_ACTIVE = 'd240;
    localparam  VID_V_TOTAL = 'd512;
    localparam  VID_H_BPORCH = 'd10;
    localparam  VID_H_ACTIVE = 'd320;
    localparam  VID_H_TOTAL = 'd400;

    reg [15:0]  frame_count;
    
    reg [9:0]   x_count;
    reg [9:0]   y_count;
    
    wire [9:0]  visible_x = x_count - VID_H_BPORCH;
    wire [9:0]  visible_y = y_count - VID_V_BPORCH;

    reg [23:0]  vidout_rgb;
    reg         vidout_de;
    reg         vidout_skip;
    reg         vidout_vs;
    reg         vidout_hs;

logic [7:0] pixel_out_r, pixel_out_g, pixel_out_b;
always_comb begin
    vidout_rgb[23:16] = pixel_out_r;
    vidout_rgb[15:8] = pixel_out_g;
    vidout_rgb[7:0] = pixel_out_b;
end
    
always @(posedge clk_core_12288 or negedge reset_n) begin
    if(~reset_n) begin
        x_count <= 0;
        y_count <= 0;
    end else begin
        vidout_de <= 0;
        vidout_skip <= 0;
        vidout_vs <= 0;
        vidout_hs <= 0;
        
        // x and y counters
        x_count <= x_count + 1'b1;
        if(x_count == VID_H_TOTAL-1) begin
            x_count <= 0;
            
            y_count <= y_count + 1'b1;
            if(y_count == VID_V_TOTAL-1) begin
                y_count <= 0;
            end
        end

        // generate sync 
        if(x_count == 0 && y_count == 0) begin
            // sync signal in back porch
            // new frame
            vidout_vs <= 1;
            frame_count <= frame_count + 1'b1;
        end
        
        // we want HS to occur a bit after VS, not on the same cycle
        if(x_count == 3) begin
            // sync signal in back porch
            // new line
            vidout_hs <= 1;
        end

        // inactive screen areas are black
        pixel_out_r <= 8'd0;
        pixel_out_g <= 8'd0;
        pixel_out_b <= 8'd0;
        
        // generate active video
        if(x_count >= VID_H_BPORCH && x_count < VID_H_ACTIVE+VID_H_BPORCH) begin
            if(y_count >= VID_V_BPORCH && y_count < VID_V_ACTIVE+VID_V_BPORCH) begin
                // data enable. this is the active region of the line
                vidout_de <= 1;
                
                if (visible_x < 320 && visible_y < 240) begin
                    pixel_out_r <= visible_x[7:0];  // 16 horizontal pixels of each red level
                    pixel_out_g <= visible_y[7:0];  // 16 vertical pixels of each green level
                    pixel_out_b <= 8'd64;
                end else begin
                    pixel_out_r <= 8'd0;
                    pixel_out_g <= 8'd16;
                    pixel_out_b <= 8'd48;
                end               
            end 
        end
    end
end
    
endmodule
```

This is not the place for a **Verilog** tutorial but you can see that this indeed looks a lot like code but remember, this most if not all of this doesn't happen sequentially like regular code but most of it happens actually all at once on each rising clock edges (that's the difference between the `=` assignment  which happens right away and the `<=` which happens only of a rising clock edge).

The result is a feast for the eyes:

<div style="text-align: center;">
    <img alt="Analog Pocket displaying a color test" src="{{ "/assets/blog/2023-03-27/Rainbow.png" | relative_url }}" width="300"> 
</div>
<br>

I have a bit more playing around to do but I'm getting very close to having all the tools I need to put together the first version of the graphics chip, together with color and v-blank registers like the one in **pfSimulator**. I may try to get that working next before embarking on getting the **MC68000** plugged in and bootstrapped on the **Pocket** which is, by all indications right now, going to be a huge undertaking.

If you want to follow along, here are the repos for the [pfCore](https://github.com/DidierMalenfant/pfCore). This week's progress is at commit [83f771b](https://github.com/DidierMalenfant/pfCore/commit/83f771bdd7532502790bcc4c257d480a831a17f3). You can also follow my current [tasklist](https://trello.com/b/ihs3W4ux/%F0%9F%91%BE-project-freedom) on Trello.

With ❤️ from Paris, France.
