---
layout: post
title: 👾 Introducing pfDevTools
categories: blog projectfreedom
---

One thing when you work on your own little project is that you don't always build it as well as it can be. After all, if it '**works on your machine™**', then you're less inclined to architecture it in a way that is more portable or cross-platform.

As I started putting together the sample code for [openFPGA Tutorials](https://github.com/DidierMalenfant/openFPGA-tutorials), I had to improve my little build system for [pfCore](https://github.com/DidierMalenfant/pfCore) in order to make it more re-usable with other projects. Up to now I had put together a collection of **Python** scripts held together by a **Makefile**. It required pointing to a cloned repo for **Intel's IP** so the whole thing wasn't really user-friendly. As the Makefile was calling some shell commands it wasn't very portable to other OSes either. I had to find a cleaner solution.

I've always been fond of build systems and I've used a few in my days, **Make** or **CMake** for example. One that we used for while when we started making [Daxter](https://en.wikipedia.org/wiki/Daxter_(video_game)) was [SCons](https://scons.org). This is, in fact, where I cut my teeth on **Python** and started falling in love with the language. I put together a build system for both the code and game assets back then but eventually we moved away from **SCons** because the project had become too large for it and build times were too long, especially when nothing needed to be built.

I you remember one of the ideas behind the tools for **Project Freedom** was to use **Python** as much as possible. I'm not sure why I didn't remember **SCons** but when it came time to replace my **Makefile** stuff with something more portable, it suddenly clicked and I decided to port the build system to **SCons**. The beauty of it all is that both the **Python** scripts AND the build system can be encapsulated into one **Python** module. You install the module and you're ready to build. Well... almost... the system still needs Docker and I don't really have a way to automate that dependency yet.

So all this turned into [pfDevTools](https://pypi.org/project/pf-dev-tools/). It's easy to install:
```console
pip install pf-dev-tools
```
and the **SConstruct** (equivalent to a **Makefile** for **SCons**) for a simple **openFPGA** core is also quite simple:
```python
import pfDevTools

# -- We need pf-dev-tools version 1.x.x but at least 1.0.2.
pfDevTools.requires('1.0.2')

env = pfDevTools.Environment(PF_CORE_TEMPLATE_REPO_TAG='v.0.0.3_for_pfCore')

env.OpenFPGACore('src/config.toml')
```

The **Python** module also comes with the `pf` command which lets you easily build the project:
```console
pf make
```

or even install it on your SD card:
```console
pf install
```

Just about everything is automated: cloning the core template repo, listing the `.v` and `.sv` verilog files found in the `src` folder and adding them to the **Quartus** project, compiling the bitstream using a **Docker** image and packaging the core into a `.zip` file. Installing then copies the content of the zip file in the right spots.

Everything in the build system is in **Python**. No need to mess with another language or file format like **Make** or **CMake**. Everything just works.

The `pf` commands has quite a few more tricks up its sleeve. More info on this and on the core building toolchain can be found in the [README](https://github.com/DidierMalenfant/pfDevTools#readme) file for **pfDevTools**.

One last added bonus of switching to a all-**Python** build system is that it has made the entire thing very portable. My development environment is **macOS** based so my main focus is making sure it works on that but it's nice to know other can use the same thing on **Linux** or **Windows** too.

That's actually what got me started on porting the whole thing to **SCons**. Someone using **Windows** wanted to try it and I knew that making the switch would give me cross-platform support while making the whole system better.

Sometimes worrying about how your stuff **could** be used by others helps you improve it in ways you wouldn't have if it was just for you.

With ❤️ from Paris, France.
