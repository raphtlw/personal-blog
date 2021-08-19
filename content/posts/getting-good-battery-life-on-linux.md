---
title: "Getting Good Battery Life on Linux"
date: 2021-05-14T01:49:32+08:00
draft: true
---

Over the past year of using Linux daily, i've discovered many ways to improving battery life and thermal efficiency on my Linux laptop and I would like to share those tips with you. I'm daily driving a Surface Laptop 2 with Arch Linux and it's been great so far. The only drawback I faced after installing Linux was the battery life. It wasn't bad, but I felt that it could definitely do better. Linux isn't configured by the manufacturers who make laptops so you'll need to do the changes yourself. Hopefully, this guide will help you with everything you need to get great battery life on Linux laptops. This guide doesn't only apply to Arch Linux. It applies to **all** Linux distros out there.

<!-- change -->
<!-- TODO: add average battery life stats and stuff -->

The fastest and easiest way to improve battery life on Linux is to use [TLP](https://linrunner.de/tlp/). It's a program that's designed to improve your laptop battery efficiency by tuning down power-hungry services and drivers like WiFi, Bluetooth and others. To install it, simply fire up a terminal and do `yay -S tlp`. Since I'm on Arch, I'll have to start/enable the service as well, so I'll do `sudo systemctl enable --now tlp`. It's that easy.

Now, to get the most out of `tlp`, type `sudo tlp-stat -w` and check the output. If it says "No warnings detected", you're good to go. Else, follow what it says in the output. In my case, I had to install `ethtool` to get WoL (Wake on Lan) working. All I had to do was to use `yay -S ethtool` to get Wake on Lan support to work again as normal.

To further improve battery life, you want to install `powertop` to see what processes are using the most amount of battery. Usually, for the rogue processes