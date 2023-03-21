---
layout: post
title: 🧸 State of the toyboxpy beta
categories: blog toyboxpy update
---
It's been almost a month since the last update and things have definitely been moving forward at a rapid pace. Let's catch up...

First, I'm happy to announce that [Jeremy McAnally](https://github.com/jm), the original creator of **toybox**, and I have decided to join forces and work together on this project.

We are pretty much feature complete for 1.0.0 and are now just looking for bug fixes. If you wish to help out and you have access to a **Linux** or **Windows** machine, please [reach out](https://github.com/toyboxpy/toybox.py/discussions/1) on Github.

On top of supporting **Lua** and **C** toyboxes, you can now also provide **assets** via a toybox (fonts, sprite sheets) either on their own or as part of a code **toybox**. This opens the door for a lot of awesome contributions to the **Playdate** dev community by making it easy to share, maintain and use any type of code or asset for the **Playdate**.

**toyboxes** can also be installed from your local hard drive now, which is very handy during development. On **macOS** and **Linux** those will actually be softlinked into your project so that any modifications you make to the **toybox** inside your project will be made as if it was directly in the **toybox**'s folder. I used this to work on a few of my own **toyboxes** and it really makes development super easy.

Look in the [Readme](https://github.com/toyboxpy/toybox.py#readme) file for more information about all the new features.

Finally I will, in the coming days, reach out to developers of cool third party libraries and engines for the **playdate** in order to have them try **toyboxpy**. I've started [a list of **toyboxes**](https://github.com/stars/DidierMalenfant/lists/toyboxes) on Github and I will update it as new projects come alive.

Happy **toyboxing**!

With ❤️ from Paris, France.
