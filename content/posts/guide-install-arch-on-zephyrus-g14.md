---
title: "How to install Arch Linux on the Zephyrus G14 with full GPU support"
date: 2021-08-21T14:30:36+08:00
draft: true
---

In this blog, we will cover how to install Arch Linux on the G14 with full GPU support and getting everything working properly. Most importantly, the mic mute button. Later on, I will also show you how to get a QEMU/KVM Windows VM with GPU passthrough and most of the things working properly to be able to use Windows in your Linux daily driver setup, getting the best of both worlds.

Recently, I've been having a lot of problems with Windows in my daily life. Because I already use Arch on my Surface, I decided to try something new this time. I installed Pop!_OS instead of Arch. The first few days were perfectly fine, however, after a few weeks, I discovered that there were a few bugs in GNOME that really annoyed me and sometimes the Pop! Shop would just randomly freeze on me. Another issue was a glitch with the launchpad menu, which caused icons to cut-off somehow. Here's how it looks like:

![screenshot](/images/rxavniiyfii71.png)

So today, I decided to wipe the G14 and install Arch and have the GPU passthrough fully working to be used in a Windows VM.

