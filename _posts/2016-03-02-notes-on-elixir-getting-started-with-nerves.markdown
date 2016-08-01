---
title: "Notes on Elixir: Getting Started with Nerves"
layout: post
tags: elixir nerves

---

This week I got my first Raspberry Pi, so of course I set up a wicked cool media
centre with it. Nah, jokes, I checked out the [Nerves Project][np], and got
`blinky`, the `Hello, World!` of embedded systems running.  I had a few false
starts and wrong turns, so here's what ended up working for me.

<!--more-->

First I had to get the micro SD card that came with my rpi2 recognised on my
laptop. I use Xubuntu 14.04.4 on an old Dell Latitude E6400 that has an inbuilt
SD card reader. I removed all the files that came on the micro SD card, then I
spent ages formatting and reformatting on both Linux & Windows until I finally
turned up something in a forum that worked. So, I had to run `sudo mkfs -j /dev/mmcblk0`,
where `/dev/mmcblk0` is my SD card, on my Linux laptop and then
reformat it on a Windows laptop, sheesh. I take this as an indication that I
need to spend more time learning Linux fundamentals.

Nerves has a great project called [Bakeware][bake] that massively simplifies the
process of configuring and compiling systems, toolchains and firmware.
Unfortunately I was under the impression it was only available for Mac OSX and so
I went down a long and unsuccessful road involving `make`.  Thank goodness for
the kind folks on the #nerves elixir-lang Slack channel, who sent me in the
right direction.

So, to get going with Bake, we need to install some dependencies, including [fwup][fwup]:

```bash
~ $ sudo apt-get install autoconf libtool squashfs-tools mtools zip unzip
# install libconfuse 2.8, the version available in apt is too old.
~ $ curl -L https://github.com/martinh/libconfuse/releases/download/v2.8/confuse-2.8.tar.gz | tar -xz -C /tmp
~ $ pushd /tmp/confuse-2.8
/tmp/confuse-2.8 $ ./configure
/tmp/confuse-2.8 $ make && sudo make install
/tmp/confuse-2.8 $ popd
~ $ rm -rf /tmp/confuse-2.8
# add libsodium ppa
~ $ sudo add-apt-repository ppa:chris-lea/libsodium
# install libsodium-dev & libarchive-dev
~ $ sudo apt-get update && sudo apt-get install libsodium-dev libarchive-dev
# install fwup
~ $ git clone https://github.com/fhunleth/fwup.git
pushd fwup
~/fwup $ git checkout tags/v0.6.0
~/fwup $ ./autogen.sh
~/fwup $ ./configure
~/fwup $ make && sudo make install
# check fwup
~/fwup $ make check
~/fwup $ popd
```

Now we can install Bake. This involves downloading a ruby script that does the work for you. It's worth having a look at it to see what's in it, especially if you run into problems, it's fairly simple and self-explanatory. I know there are [plans underway][plans] to port this script from Ruby to Elixir, which makes a lot of sense.

```bash
~ $ ruby -e "$(curl -fsSL https://bakeware.herokuapp.com/bake/install)"
```

The script will ask you to add `bake` to your path, so open up your `~/.bashrc` or `~/.zshrc` in your text editor of choice and pop this in:

```bash
# add bake to path
export PATH=$PATH:~/.bake/bin
```

And back in your terminal, don't forget to type this: `source ~/.bashrc` or this `source ~/.zshrc`

Right, we're now ready to create our first embedded system in Elixir!
Nerves has a couple of [example projects][ep] available, one being `blinky`,
which is apparently the `Hello, World!` of embedded systems.  That's what
we'll use now:

```bash
# clone the examples repo
~ $ git clone https://github.com/nerves-project/nerves-examples.git
```

Thanks to [a tip from Wendy Smoak][ws], we can save some effort and
define our target platform inside our Bakefile, saving us having to declare it every time with `--target rpi2`.
To do this open up `nerves-examples/blinky/Bakefile` and add in `default_target :rpi2` or whatever you're building to, whether it's an earlier rpi or a Beaglebone Black.  The Bakefile should now look like this:

```elixir
use Bake.Config

platform :nerves
default_target :rpi2

target :rpi,
  recipe: {"nerves/rpi", "~> 0.1"}

target :rpi2,
  recipe: {"nerves/rpi2", "~> 0.1"}

target :bbb,
  recipe: {"nerves/bbb", "~> 0.1"}
```

Now, we bake:
```bash
# go into the blinky directory
~ $ cd nerves-examples/blinky
# get our system, which can take a little while
~/nerves-examples/blinky $ bake system get
# get our toolchain, which can also take a little while
~/nerves-examples/blinky $ bake toolchain get
# bake our firmware
~/nerves-examples/blinky $ bake firmware
# now we just need to burn our firmware to the SD card
~/nerves-examples/blinky $ bake burn -d /dev/mmcblk0
```

So, that should work.  You may need to change the permissions on your SD card for the
`bake burn` to work.  All that's left now is to plug in our rpi2 and have our minds
blown.

<iframe width="640" height="360" src="https://www.youtube.com/embed/txqA3c49iPo" frameborder="0"> </iframe>

As far as I know, in the `blinky.ex` file you can comment out the `Logger.debug "blinking led #{inspect led_key}"` line, and you'll end up in iex, rather than seeing a never ending loop of debug messages.

Big big thanks to [Frank Hunleth][fh] and [Greg Mefford][gm].

I've started building a [Vagrant environment][vagrant] for this if you're interested, but it still has teething problems.  It started out as a way to check the steps I'd taken in a clean Ubuntu VM, and the process clarified a few things, though what use it may actually be, I don't know. It almost works, installing everything, and burning the blinky firmware for you, but not quite. One day soon, maybe.

Anyway, I'm super excited about Nerves, dust off that old raspberry pi that you had big dreams for, enlist your kids, and check it out.

[np]: http://nerves-project.org/
[bake]: http://www.bakeware.io/
[fwup]: http://github.com/fhunleth/fwup
[ep]: https://github.com/nerves-project/nerves-examples
[plans]: https://github.com/bakeware/bake/issues/1
[ws]: http://wsmoak.net/2016/01/11/embedded-elixir-nerves-bake.html
[fh]: https://twitter.com/fhunleth
[gm]: http://www.gregmefford.com/
[vagrant]: https://github.com/bordeltabernacle/vagrant-nerves
