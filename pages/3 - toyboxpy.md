---
layout: page
title: toybox.py
permalink: /toybox.py/
---

<img alt="toyboxpy logo" src="{{ "/assets/img/toybox-449x449.png" | relative_url }}" width="150"> 

<a href="https://spdx.org/licenses/MIT.html" target="_blank"><img alt="license badge" src="https://img.shields.io/github/license/DidierMalenfant/toybox.py"></a>
<a href="https://python.org" target="_blank"><img alt="Python version badge" src="https://img.shields.io/pypi/pyversions/toyboxpy.svg"></a>
<a href="https://pypi.org/project/toyboxpy" target="_blank"><img alt="latest PyPI version badge" src="https://img.shields.io/pypi/v/toyboxpy.svg"></a>
<a href="https://github.com/DidierMalenfant/toybox.py/actions/workflows/python-package.yml"><img alt="unit test badge" src="https://github.com/DidierMalenfant/toybox.py/actions/workflows/python-package.yml/badge.svg"></a>

### A Lua, C and asset dependency manager for the <a href="https://play.date" target="_blank">Playdate</a> SDK.

#### What is it?

<b>toybox.py</b> installs and handles dependencies between third party <b>Lua</b>, <b>C</b> and <b>asset</b> libraries to make <b>Playdate</b> development easier. Read the <a href="https://github.com/DidierMalenfant/toybox.py#toyboxpy">manual</a> for more info.

#### Install toybox.py

Make sure you have a <a href="/blog/installing-python">supported version</a> of Python then paste this in a <b>macOS</b> Terminal, a <b>Linux</b> shell prompt or a <b>Windows</b> Powershell.

{%- highlight bash -%}
pip install toyboxpy
{%- endhighlight -%}

Then simply <a href="https://github.com/DidierMalenfant/toybox.py#using-lua-toyboxes">import</a> the <b>toyboxes</b> in your project.

#### What can I play with?

Here are some <b>toyboxes</b> that you can play with right now. Best part is, you can also <a href="https://github.com/DidierMalenfant/toybox.py#creating-your-own-toyboxes">create your own</a>.

<p><img alt="toyboxpy logo" src="{{ "/assets/img/toybox-449x449.png" | relative_url }}" width="20" height="20"> <a href="https://github.com/NicMagnier/PlaydateLDtkImporter">PlaydateLDtkImporter</a> (Lua)</p>
<p>Load tilemaps created with <a href="https://ldtk.io" target="_blank">LDtk</a> in playdate games.</p>
<p><img alt="toyboxpy logo" src="{{ "/assets/img/toybox-449x449.png" | relative_url }}" width="20" height="20"> <a href="https://github.com/DidierMalenfant/TiledUp">TiledUp</a> (Lua)</p>
<p>A level importer and renderer for <a href="https://mapeditor.org" target="_blank">Tiled</a> level files.</p>
<p><img alt="toyboxpy logo" src="{{ "/assets/img/toybox-449x449.png" | relative_url }}" width="20" height="20"> <a href="https://github.com/DidierMalenfant/FontSample">Font Sample</a> (Asset)</p>
<p>An example of an asset-only toybox to easily add a font to any project.</p>
<p id="title"><img alt="toyboxpy logo" src="{{ "/assets/img/toybox-449x449.png" | relative_url }}" width="20" height="20"> <a href="https://github.com/Whitebrim/AnimatedSprite">AnimatedSprite</a> (Lua)</p>
<p>Sprite class extension with imagetable animation and finite state machine support.</p>
<p><img alt="toyboxpy logo" src="{{ "/assets/img/toybox-449x449.png" | relative_url }}" width="20" height="20"> <a href="https://github.com/DidierMalenfant/playbox2d">playbox2d</a> (Lua)</p>
<p>A port of <a href="https://box2d.org" target="_blank">box2d</a> lite for the <b>Playdate</b>.</p>
<p id="title"><img alt="toyboxpy logo" src="{{ "/assets/img/toybox-449x449.png" | relative_url }}" width="20" height="20"> <a href="https://github.com/DidierMalenfant/pdbase">pdbase</a> (C/Lua)</p>
<p>Common low-level C helpers and handy extensions to Lua <b>table</b>, <b>math</b> and also a C-like <b>enum</b> for Lua.</p>

and many more... You can see the complete list by typing:

{%- highlight bash -%}
toybox store content
{%- endhighlight -%}

You can even <a href="https://github.com/DidierMalenfant/toybox.py#creating-your-own-toyboxes">create</a> your own <b>toybox</b>.

#### Where can I get the latest updates?

You can find the project's repo on [Github](https://github.com/DidierMalenfant/toybox.py) or head over to the [main page](/) and look for posts with the 🧸 icon.
