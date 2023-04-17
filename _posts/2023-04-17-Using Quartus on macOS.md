---
layout: post
title: 🤓 Using Quartus on macOS
categories: blog nerdy
---

When I first started getting excited about building my own fantasy console on the **Pocket**, one thing almost immediately stopped me dead in my tracks:

<div style="text-align: center;">
<iframe src="https://mastodon.gamedev.place/@didier/109687971117867481/embed" class="mastodon-embed" style="max-width: 100%; border: 0" width="400" allowfullscreen="allowfullscreen"></iframe><script src="https://mastodon.gamedev.place/embed.js" async="async"></script>
</div>
<br>

In the end I soldiered on anyway, [dug up some old mini-pcs](/blog/projectfreedom/2023/03/27/building-a-core.html) and installed **Windows** and **Linux** on them to try and put together a working toolchain. It was clunky, I had to remote desktop or `ssh` into the other box and then copy the results over.

Enter **Marcus Andrade**, aka [Boogermann](https://github.com/boogermann) and the whole `#dev-talk` gang over at the [FPGAming](https://discord.gg/VNz3yCse) Discord. Those guys develop most of the retro cores for the Pocket and, of course, they have a much better way of using the **Intel** tools on various platforms. Since a lot of that info is hidden behind a **Discord** wall, I thought I'd put together a little blog post here for anyone who may want to use this in their own projects.

### Installing Docker

The magic comes in two parts, the first one is thanks to [**Docker**](https://docker.com) which lets you run images of OSes inside their own sandbox or container as if they were simple command line apps. The great thing about this is that the environment for the app/image being run is consistant, the image used is reset every time you use it, but also that it doesn't have to be running the same OS as the host it's being run on. In this case, you're going to run a **Linux** image inside a docker container on **macOS**.

So you'll need to install [Docker Desktop](https://www.docker.com/get-started/). The **Docker Engine** needs to be running while using containers so you can just leave it run in the background.

### What if I'm using an Apple Silicon Mac?

The second part of the magic comes from Apple's **Rosetta**. This is the technology that allows you to run **Intel** binaries on your **M1** or **M2** Mac. And guess what? The crazy people over at **Docker** have used it to allow you to run **Intel** images, even on an **Apple Silicon** Mac 🤯.

All you have to do is make sure that the feature `Use Rosetta for x86/amd64 emulation on Apple Silicon` is enabled in `Settings->Features in development`.

<div style="text-align: center;">
    <img alt="Analog Pocket Core info screen" src="{{ "/assets/blog/2023-04-17/docker-rosetta-enable.png" | relative_url }}" width="800"> 
</div>
<br>

Be careful, it seems to un-set itself from time to time for some reason. If you're starting to see problems when running the image, like **Quartus** hanging for no reasons, this is probably the first place to look.

### Using the Intel Quartus image

Everything else can be done from the command line and therefore can be integrated into your build system or your IDE.

If you want to try it out, clone Analogue's [template](https://github.com/open-fpga/core-template) and cd into `src/fpga` then type the following:
```
docker run --platform linux/amd64 -t --rm -v $(DEST_VERILOG_DIR):/build didiermalenfant/quartus:18.1-apple-silicon quartus_sh --flow compile ap_core
```

This will build the bitstream file for this project, by running the **Linux** version of the **Intel Quartus** toolchain right there on your Mac. First time you run this it may take a while before doing anything as it will download the docker image but after that it should start right away. Keep in mind **FGPA** compilations are slow, even for small projects like this.

The image used here is one I put together based on **Marcus**' own images. It only contains a small change to allow it to run on **Apple Silicon** Macs. **Quartus 18.1** is the minimum recommended version for **Analogue Pocket** development but if you want to use the bleeding edge version you can also use the image `didiermalenfant/quartus:22.1-apple-silicon` instead.

### Modifying the Docker image

To modify an existing image you can run it in interactive mode by typing:
```
docker run --platform linux/amd64 -it --rm -v $(DEST_VERILOG_DIR):/build didiermalenfant/quartus:18.1-apple-silicon
```

This will give you a command/terminal prompt within the container itself. You can now make any modifications you want and when you're ready just commit those changes to a new image.

First find the hash of the container image by typing:
```
docker ps
```

This will display the running container, together with its hash and you can then type:
```
docker commit <container hash> <new image name>
```

This will save your changes under a new image name.

### Building the image from scratch

If you want to build an image from scratch you can simply use **Marcus**' [build scripts](https://github.com/raetro/sdk-docker-fpga). These will only work on an **Intel** machine unfortunately but that's due to a problem in **Rosetta**'s where. `rols` gives the following explanation:

>The **Quartus** setup app has a slightly unusual layout string offsets in the **ELF** file, usually they are pretty close to the start, within the first page of memory. Rosetta only `mmap()`s the **ELF** header then goes to start looking for libraries (which needs strings), so it reads memory it hasn't actually mapped. Usually it gets away with this as `mmap()` maps pages and the strings are usually in the first page, but for some reason that **Quartus** install has a legal but odd **ELF** layout and they are way way up the file in an unmapped page, so you get a `segfault`.

But if you have access to an **Intel** box you can simply do:
```
git clone https://github.com/raetro/sdk-docker-fpga.git
cd sdk-docker-fpga
./build.sh -v18.1 -b
```

and this will build a brand new `18.1` image locally for you.

### Special thanks

Thanks 🙏🏻 to `boogermann`, `agg23` and `rols` from the [FPGAming](https://discord.gg/VNz3yCse) Discord for their help and patience in guiding me through all this.

With ❤️ from Paris, France.
