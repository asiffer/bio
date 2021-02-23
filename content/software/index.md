---
title: Software
---


In addition to the research, we try to cleanly distribute our work. You could find several projects on my [github](https://github.com/asiffer/), some `debian` packages on my [ppa](https://launchpad.net/~asiffer) and also some `python3` packages on [PyPI](https://pypi.org/user/asiffer/), all with [GPLv3](https://www.gnu.org/licenses/gpl-3.0.en.html) license. Below we detail some of them. 



### Research related
---


#### netspot

We have built an network IDS (Intrusion Detection System) upon the SPOT algoritm, called **netspot**.
The idea is to flag extreme events whike monitoring netwrok statistics. The philosophy of netspot
is the simplicity: neither deep learning stuff nor end-to-end 0-day detection. 
We merely want to make it a credible real-world anamoly-based IDS. 
See [https://asiffer.github.io/netspot/](https://asiffer.github.io/netspot/) for further details.

#### libspot

In our work, we developped an algorithm aimed to detect outliers in streaming data: SPOT.
**libspot** is a C++ library which mainly implements it. 

We also released `python3` bindings to `libspot` and a pure `Go` implementation of the library is also available at 
[https://github.com/asiffer/gospot](https://github.com/asiffer/gospot)

More details are given on [https://asiffer.github.io/libspot/](https://asiffer.github.io/libspot/).

<!-- Furthermore, I currently develop an IDS (Intrusion Detection System) which uses SPOT instances to monitor some network features. 
I don't claim it will be better than a signature-based IDS like [Snort](https://www.snort.org/), [Zeek](https://www.zeek.org/) or [Suricata](https://suricata-ids.org/). The purpose is to merely make a proof-of-concept that it is possible to do *behavioural detection*.  -->


#### libfolding

<!-- [![libfolding](/assets/ftu.png "The folding test of unimodality")](https://asiffer.github.io/libfolding/) -->

<img src="/assets/ftu.png" alt="The folding test of unimodality" style="float:left;width:33%;">


We have also published a new statistical test aimed to detect whether the distribution of your data is *unimodal* or *multimodal*. It works in multidimensional and even streaming data. It is called the **Folding Test of Unimodality** (or **FTU**). 
I developped a `C++` library and its `python3` bindings too.

More details can be found at [https://asiffer.github.io/libfolding/]().

<!-- The code is also hosted on <a href="https://asiffer.github.io/libfolding/">github</a> and <a href="https://launchpad.net/~asiffer/+archive/ubuntu/libfolding">launchpad</a> (but through a different ppa). -->

Yes, an `R` package exists too! Initially you can find it on [github](https://github.com/asiffer/Rfolding) but since october 2018)it has been made available on CRAN through the name `Rfolding`. This is a pure `R` package. I apologize for the documentation which is rather minimalist. 

### Nothing related

I like exploring new technologies and particularly the things that are about Linux and/or the network.

#### carnx

`carnx` is an XDP-based (eXpress Data Path) network statistics digger. It leverages the recent XDP hook within
the Linux kernel to computes some network statistics. 
The advantage of this technology is that all heavy processing (packet parsing) is let to the the kernel. 
It notably removes context switching, leading to very high performances compared with common user-space tools (like `libpcap`).

In addition, this project links several components (`C` library, `Go` server) and exposes a gRPC API.

#### wg-easy-vpn

I started this project at the beginning of the COVID pandemic. 
That is a tool that ease the deploymnent of Wireguard VPN
in the admin sys point of view. 
This VPN is far simpler that OpenVPN and IPSec
but the config file management remains a pain and `wg-easy-vpn`
tries to hide this task.

#### arduigo

Once again a tehnology I did not know: embedded systems. First of all
I tested my Arduino board through the common user-friensly libraries
but there were some bugs in the networking part (yes I bought the ethernet shield).
So I decided to have a look to the vendor `C` libraries to improve my understanding
of the interface hardware/software (and the embedded systems protocols).

Finally I came across `tinygo` that allows to flash `Go` program to many boards. Unfortunately
I did not anage to make it work on my poor Arduino board, so I rewrite some parts of the vendor
`C` library into `Go`, `arduigo` was born.

Now it is rather minimal but I managed to make LCD and Ethernet work.



