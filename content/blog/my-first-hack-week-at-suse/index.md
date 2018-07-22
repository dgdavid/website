---
title: "My First Hack Week at SUSE"
date: 2018-07-14T07:56:04+01:00
lastmod: 2018-07-22T23:06:42+01:00 
tags: [ "Hack Week", "YaST" ]
---

At the beginning of July, I started to work at [SUSE](https://www.suse.com) as
a member of the [YaST](http://yast.opensuse.org/) team. A wanted, great, and
stimulating step in my professional career :blush:. Just one week after, I
could enjoy the Hack Week 17 which is

> the time SUSE engineers experiment, innovate & learn interruption-free for a
whole week <small>-- [[SUSE Hack Week homepage]](https://hackweek.suse.com/)</small>

My first Hack Week! So exciting! :tada::tada::tada:

I was tempted to start learning a new language or framework, but instead I
decided to do something related to YaST in order to get more familiar with it
and its ecosystem. That was the reason why I ended up trying to implement a
[graphical view of the changes to be made to disk during
installation](https://hackweek.suse.com/17/projects/graphical-view-of-the-changes-to-be-made-to-disks-during-installation),
**a cool feature** suggested [in a Bugzilla
entry](https://bugzilla.suse.com/show_bug.cgi?id=1096900).

After a first and quick research (I mean after a few `grep`s) I saw that there
is already a
[`DiskBarGraph`](https://github.com/yast/yast-storage-ng/blob/master/src/lib/y2partitioner/widgets/disk_bar_graph.rb)
widget which receives a `disk` object and represents it graphically. In
addition, I could check that the [`Y2Storage` proposal
dialog](https://github.com/yast/yast-storage-ng/blob/master/src/lib/y2storage/dialogs/proposal.rb)
had all that I needed. 

_"A piece of cake"_ I thought... How wrong can one be! :sweat_smile: I could
not finish it. Even worse, I believe - at least with my current knowledge -
that it is not possible right now to implement it. 

Let me share what I discovered/learn

* The [`libyui`](https://github.com/libyui/libyui)
  [BarGraph](https://github.com/libyui/libyui/blob/master/src/YBarGraph.cc)
  seems to not be rendering regions properly; when given sizes differ too much
  between them, small ones are not drawn _[[Figure 1.1](#figure-1-1) and
  [Figure 1.2](#figure-1-2)]_. Bug or feature? A bug, IMO. What is more, label
  showed on "mouse over" are placed wrongly _[[Figure 1.3](#figure-1-3)]_.

* `initial_devicegraph` (or `probed devicegraph`) is not accessible publicly
  through the proposal.

* In YaST there is not scroll (which was a big surprise to me since I come from
  web development and... you know what crazy stuff you can do in the browser).
  Elements, (a.k.a `Terms`) are placed in horizontal or vertical, distributing
  available space proportionally or according to Min/Max Width/Height and
  margins. That is a problem because depending on system resolution and number
  of disks elements could collapse or directly "dissappears" (each disk
  requires two graph for a full representation) _[[Figure 2.1](#figure-2-1) and
  [Figure 2.2](#figure-2-2)]_.

* YaST is not designed for an extensive user interactivity. So, it is not
  possible to draw a bar graph which allows the user "click on a partition to
  show its subvolumes in a drop-down component" :sob::sob::sob:

But I do not like to talk about "problems" without offering a possible
solutions :) So, here you have what I thought to address the above issues

* For the first one, the solution seems to fix how bar graph is drawn directly
  in the `libyui`. For example, setting a minimum graphical representation
  (1px?) for any size.

* For the second, maybe it will be enough to make the method public, but I am
  not completely sure if it is the right way or it should be used
  `StorageManager.instance.probed` instead.

* The other two could be avoided using a view similar to the partitioner
  _[[Figure 3](#figure-3)]_, where disk will be shown in a tree in the left
  leaving the right side to display the related information and graphs of
  selected disk.

In addition, I must admit that I was not able to find out how to check if
probed and proposal disk state are different.

That's all :) [You can see the commits that I finally did in the
`feature/graphical-proposal` branch of my `yast-storage-ng`
fork](https://github.com/dgdavid/yast-storage-ng/commits/feature/graphical-proposal).
Not a big deal :stuck_out_tongue_winking_eye:

By the way, just for the records, I also did some not related things in my
first Hack Week, namely

- set-up the `VPN`
- update my [openSUSE
  TW](https://software.opensuse.org/distributions/tumbleweed) after a while 
- see how vim started crashing :sob: :sob: 
- switch to [`Neovim`](https://neovim.io/) (just install a package and create
  an alias)
- test a few dark themes for vim/neovim
  - perun - https://github.com/aradunovic/perun.vim
  - Quantum - https://github.com/tyrannicaltoucan/vim-quantum
  - Iosvkem - https://github.com/neutaaaaan/iosvkem
  - :star2: grubvox - https://github.com/morhetz/gruvbox (the chosen one)
- change the office distribution (with the rest of teammates)

---

<div class="has-text-centered title is-5">
Some screenshots
</div>

<div id="figure-1-1">
  {{<figure 
  src="images/empty_15GiB_640x480.png"
  class="image"
  title="Figure 1.1 - Suggested partitioning showing graphs at the right in a 640x480 resolution"
  >}}
</div>

<div id="figure-1-2">
  {{<figure 
  src="images/empty_50GiB_vertical_1024x768.png"
  class="image"
  title="Figure 1.2 - Suggested partitioning showing graphs at the bottom in a 1024x768 resolution"
  >}}
</div>

<div id="figure-1-3">
  {{<figure 
  src="images/wrong_graph_label.png"
  class="image"
  title="Example of wrong label on mouse over"
  >}}
</div>

<div id="figure-2-1">
  {{<figure 
  src="images/mixed_disks_horizontal_1024x768.png"
  class="image"
  title="Figure 2.1 - Actions buttons dissapeared with 3 disks in a 1024x768 resolution (graphs at right)"
  >}}
</div>

<div id="figure-2-2">
  {{<figure 
  src="images/mixed_disks_vertical_1024x768.png"
  class="image"
  title="Figure 2.2 - Actions buttons dissapeared with 3 disks in a 1024x768 resolution (graphs at bottom)"
  >}}
</div>

<div id="figure-3">
  {{<figure 
  src="images/expert_partitioner.png"
  class="image"
  title="Figure 3 - The expert partitioner view, which could be useful as inspiration to show the proposal in a similar way when system have more than one disk"
  >}}
</div>
