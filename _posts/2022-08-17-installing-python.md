---
layout: post
title: 🤓 Installing Python
categories: blog nerdy
---

Most of my python scripts requires at least [**Python**](https://python.org){:target="_blank"} 3.7 and some also need access to [**pip**](https://pypi.org/project/pip/){:target="_blank"}, the **Python** package manager in order to install dependencies. You can check which version you currently have, if any, with:

{%- highlight bash -%}
python --version
{%- endhighlight -%}

On **macOS** or **Linux** if this returns a 2.x version, try this instead:

{%- highlight bash -%}
python3 --version
{%- endhighlight -%}

To check if you have **pip** installed you can do this:

{%- highlight bash -%}
pip --version
{%- endhighlight -%}

Here's how you can install a newer/supported version of **Python** and **pip**:

- [Installing on macOS](#installing-on-macos)
- [Installing on Linux](#installing-on-linux)
- [Installing on Windows](#installing-on-windows)

<br>

### Installing on macOS
<p></p>

Open a terminal window. All the commands listed here will be typed directly there.

First, make sure that you have the developer tools installed on your machine.

{%- highlight bash -%}
git --version
{%- endhighlight -%}

If running this command offers to you install the developer tools, say yes.

If the command returns the current version for **git**, make sure you also have **Python** installed:

{%- highlight bash -%}
python3 --version
{%- endhighlight -%}

This should also report the current version of **Python** which should be higher or equal to `3.7`.

Finally make sure you're running the latest version of **Python**'s package manager **pip**:

{%- highlight bash -%}
python -m pip install --upgrade pip
{%- endhighlight -%}

If you get a message like this one:

```
WARNING: The scripts pip, pip3 and pip3.9 are installed in '/Users/admin/Library/Python/3.9/bin' which is not on PATH.
```

Then do the following, keeping in mind you may need to replace the version number for **Python** with the one given in your warning message.

If the top of your terminal window has `bash` in it (older **macOS** versions before **Catalina**), do:

{%- highlight bash -%}
echo "# Add Python to path" >> .bashrc
{%- endhighlight -%}

{%- highlight bash -%}
echo 'export PATH=$PATH:/Users/admin/Library/Python/3.9/bin' >> .bashrc
{%- endhighlight -%}

and if the top of your terminal Window has `zsh` in it (**macOS Catalina** and later), do:

{%- highlight bash -%}
echo "# Add Python to path" >> .zshrc
{%- endhighlight -%}

{%- highlight bash -%}
echo 'export PATH=$PATH:/Users/admin/Library/Python/3.9/bin' >> .zshrc
{%- endhighlight -%}

Then close the terminal window and re-open it for the changed to take effect.

<br>

### Installing on Linux
<p></p>

Since on **Linux** many services rely on the installed version of **Python**, you have to be careful when trying to upgrade to a newer version and do it in a way that doesn't affect any other versions.

The instructions below apply to **Ubuntu Linux** 18.04 and 20.04. You may need to tweak them for other distributions. You can find a detailed guide on installing **Python 3** and pip over at [RealPython](https://realpython.com/installing-python/){:target="_blank"}.

Open a terminal window. All the commands listed here will be typed directly there.

First make sure your list of packages is up to date:

{%- highlight bash -%}
sudo apt-get update
{%- endhighlight -%}

Install **Python** 3.8 (If you're running **Ubuntu Linux** 20.04 you already have it and you can skip this step):

{%- highlight bash -%}
sudo apt-get install python3.8
{%- endhighlight -%}

Then install the **Python** package manager `pip`:

{%- highlight bash -%}
sudo apt-get install python3-pip
{%- endhighlight -%}

And finally make sure you're running the latest version of **Python**'s package manager **pip**:

{%- highlight bash -%}
python3.8 -m pip install --upgrade pip
{%- endhighlight -%}

If you get a warning about the scripts pip, pip3 and pip3.8 not being in your PATH you can safely ignore it.

Close the terminal and reopen it for the changes to take effect.

<br>

### Installing on Windows
<p></p>

First, make sure that you have [**Git**](https://git-scm.com/download/win){:target="_blank"} installed on your machine. Then install the latest release of [**Python 3**](https://www.python.org/downloads/windows/){:target="_blank"}. Note that with **Windows** 7 you may not be able to install any version beyond **Python** 3.7.

**Make sure to check** `Add Python <version> to path` at the beginning and `Disable Path length limit` at the end of the installation.

Finally open a **Windows Terminal** or **Powershell** and make sure you're running the latest version of **Python**'s package manager **pip**:

{%- highlight bash -%}
python -m pip install --upgrade pip
{%- endhighlight -%}
