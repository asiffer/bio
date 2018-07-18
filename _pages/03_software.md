---
layout: page
title: Software
permalink: /software/
---

<h2>libspot</h2>

In our work, we developped an algorithm aimed to detect outliers in streaming data: SPOT.
**libspot** is a C++ library which mainly implements it. 

We also released **python3-libspot** which embeds `python3` bindings to libspot.

This code is hosted on <a href="https://asiffer.github.io/libspot/">github</a> and <a href="https://launchpad.net/~asiffer/+archive/ubuntu/libspot">launchpad</a>
(so you can just add my ppa to your debian/ubuntu system, use `apt` magic command and you are done!)

Furthermore, I develop a kind of plugin for <a href="https://www.bro.org/">Bro</a> (an Intrusion Detection System) which uses SPOT instances to monitor some network features. When an anomaly occurs, the plugin sends the event to the Bro agent. It is based on <a href="https://www.bro.org/sphinx/components/broccoli/broccoli-manual.html">Broccoli</a>, the Bro client communication library.

---

<h2>libfolding</h2>

We have also published a new statistical test aimed to detect whether the distribution of your data is *unimodal* or *multimodal*. It works in multidimensional and even streaming data. It is called the **Folding Test of Unimodality** (or FTU). 
I developped a `C++` library and its `python3` bindings too.

The code is also hosted on <a href="https://github.com/asiffer/libfolding">github</a> and <a href="https://launchpad.net/~asiffer/+archive/ubuntu/libfolding">launchpad</a> (but through a different ppa).

I have also a `R` package but is not released yet.
