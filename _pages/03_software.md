---
layout: page
title: Software
permalink: /software/
---

## libspot

[![libspot](/assets/bat.png "Born to flag outliers")](https://asiffer.github.io/libspot/)

In our work, we developped an algorithm aimed to detect outliers in streaming data: SPOT.
**libspot** is a C++ library which mainly implements it. 

We also released **python3-libspot** which embeds `python3` bindings to libspot.

This code is hosted on <a href="https://asiffer.github.io/libspot/">github</a> and <a href="https://launchpad.net/~asiffer/+archive/ubuntu/libspot">launchpad</a>
(so you can just add my ppa to your debian/ubuntu system, use `apt` magic command and you are done!)

Furthermore, I develop an IDS (Intrusion Detection System) which uses SPOT instances to monitor some network features. 
I don't claim it will be better than a signature-based IDS like [Snort](https://www.snort.org/), [Bro](https://www.bro.org/) or [Suricata](https://suricata-ids.org/). The purpose is to merely make a proof-of-concept that it is possible to do *behavioural detection*. 

<!-- Furthermore, I develop a kind of plugin for <a href="https://www.bro.org/">Bro</a> (an Intrusion Detection System) which uses SPOT instances to monitor some network features. When an anomaly occurs, the plugin sends the event to the Bro agent. It is based on <a href="https://www.bro.org/sphinx/components/broccoli/broccoli-manual.html">Broccoli</a>, the Bro client communication library. -->

---

## libfolding

[![libfolding](/assets/ftu.png "The folding test of unimodality")](https://asiffer.github.io/libfolding/)

We have also published a new statistical test aimed to detect whether the distribution of your data is *unimodal* or *multimodal*. It works in multidimensional and even streaming data. It is called the **Folding Test of Unimodality** (or FTU). 
I developped a `C++` library and its `python3` bindings too.

The code is also hosted on <a href="https://asiffer.github.io/libfolding/">github</a> and <a href="https://launchpad.net/~asiffer/+archive/ubuntu/libfolding">launchpad</a> (but through a different ppa).

Yes, an `R` package exists too! Initially you can find it on <a href="https://github.com/asiffer/Rfolding">github</a> but recently (october 2018) it has been made available on CRAN through the name `Rfolding`. This is a pure `R` package. I apologize for the documentation which is rather minimalist. 

