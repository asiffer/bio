---
layout: page
title: Software
permalink: /software/
---


In addition to the research, we try to cleanly distribute our work. You could find several projects on my [github](https://github.com/asiffer/), some `debian` packages on my [ppa](https://launchpad.net/~asiffer) and also some `python3` packages on [PyPI](https://pypi.org/user/asiffer/), all with [GPLv3](https://www.gnu.org/licenses/gpl-3.0.en.html) license. Below we detail some of them. 



## libspot
<!-- 
[![libspot](/assets/bat.png "Born to flag outliers")](https://asiffer.github.io/libspot/) -->


<img src="/assets/bat.png" alt="Born to flag outliers" style="float:left;width:30%;">


In our work, we developped an algorithm aimed to detect outliers in streaming data: SPOT.
**libspot** is a C++ library which mainly implements it. 

We also released `python3` bindings to `libspot` and a pure `Go` implementation of the library is also available at [https://github.com/asiffer/gospot]()

More details are given on [https://asiffer.github.io/libspot/]()

<!-- This code is hosted on <a href="https://asiffer.github.io/libspot/">github</a> and <a href="https://launchpad.net/~asiffer/+archive/ubuntu/libspot">launchpad</a>
(so you can just add my ppa to your debian/ubuntu system, use `apt` magic command and you are done!) -->

Furthermore, I currently develop an IDS (Intrusion Detection System) which uses SPOT instances to monitor some network features. 
I don't claim it will be better than a signature-based IDS like [Snort](https://www.snort.org/), [Zeek](https://www.zeek.org/) or [Suricata](https://suricata-ids.org/). The purpose is to merely make a proof-of-concept that it is possible to do *behavioural detection*. 


---

## libfolding

<!-- [![libfolding](/assets/ftu.png "The folding test of unimodality")](https://asiffer.github.io/libfolding/) -->

<img src="/assets/ftu.png" alt="The folding test of unimodality" style="float:left;width:33%;">


We have also published a new statistical test aimed to detect whether the distribution of your data is *unimodal* or *multimodal*. It works in multidimensional and even streaming data. It is called the **Folding Test of Unimodality** (or **FTU**). 
I developped a `C++` library and its `python3` bindings too.

More details can be found at [https://asiffer.github.io/libfolding/]().

<!-- The code is also hosted on <a href="https://asiffer.github.io/libfolding/">github</a> and <a href="https://launchpad.net/~asiffer/+archive/ubuntu/libfolding">launchpad</a> (but through a different ppa). -->

Yes, an `R` package exists too! Initially you can find it on <a href="https://github.com/asiffer/Rfolding">github</a> but recently (october 2018) it has been made available on CRAN through the name `Rfolding`. This is a pure `R` package. I apologize for the documentation which is rather minimalist. 

