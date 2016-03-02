---
title: "Notes on Elixir: Getting Started with Nerves"
layout: post
tags: elixir, nerves

---

This week I got my first Raspberry Pi, so of course I set up a wicked cool media
centre with it. Nah, jokes, I checked out the [Nerves Project][np], and got
`blinky`, the `Hello, World!` of embedded systems running.  I had a few false
starts and wrong turns, so here's what ended up working for me.

<!--more-->

First I had to get the micro SD card that came with my rpi2 recognised on my
laptop. I use Xubuntu 14.04.4 on an old Dell Latitude E6400 that has an inbuilt
SD card reader. I removed all the files that came on the micro SD card, then I
had to run `sudo mkfs -j /dev/mmcblk0`, where `/dev/mmcblk0` is my SD card, on
my Linux laptop and then reformat it on a Windows laptop.

Nerves has a great project called [Bakeware][bake] that massive simplifies the
process of configuring and compiling systems, toolchains and firmware.
Unfortunately I was under the impression it was only available for Mac OS and so
I went down a long and unsuccessful path involving `make`.  Thank goodness for
the kind folks on the #nerves elixir-lang slack channel, who sent me in the
right direction.

So, to get going with Bake, we need to install some dependencies, including [fwup][fwup]:

{% highlight bash linenos %}
sudo apt-get -y install autoconf libtool squashfs-tools mtools zip unzip
# install dependencies for fwup
# install libconfuse 2.8
curl -L https://github.com/martinh/libconfuse/releases/download/v2.8/confuse-2.8.tar.gz | tar -xz -C /tmp
pushd /tmp/confuse-2.8
./configure
make && sudo make install
popd
rm -rf /tmp/confuse-2.8
# add libsodium ppa
sudo add-apt-repository ppa:chris-lea/libsodium
# install libsodium-dev
sudo apt-get update && sudo apt-get -y install libsodium-dev libarchive-dev
# install fwup
git clone https://github.com/fhunleth/fwup.git
pushd fwup
git checkout tags/v0.6.0
./autogen.sh
./configure
make && sudo make install
# check fwup
make check
popd
{% endhighlight %}

Now we can install Bake:

{% highlight bash linenos %}
ruby -e "$(curl -fsSL https://bakeware.herokuapp.com/bake/install)"
# add bake to path
printf "\nexport PATH=$PATH:~/.bake/bin" >> ~/.bashrc
source ~/.bashrc
{% endhighlight %}

Right, we're now ready to create our first embedded system in Elixir!
Nerves has a couple of [example projets][ep] available, one being `blinky`,
which is apparently the `Hello, World!` of the embedded world.  That's what
we'll do now:

{% highlight bash linenos %}
# clone the examples repo
git clone https://github.com/nerves-project/nerves-examples.git
# go into the blinky directory
cd nerves-examples/blinky
# thanks to a tip from Wendy Smoak, we can save some effort and
# define our target platform inside our Bakefile
DEFAULT_TARGET=rpi2
sed -i "4i default_target :$DEFAULT_TARGET" Bakefile
# get our system, which can take a little while
bake system get
# get our toolchain, which can also take a little while
bake toolchain get
# and bake our firmware
bake firmware
# now we just need to burn our firmware to the SD card
bake burn -d /dev/mmcblk0
{% endhighlight %}

So, that should work.  You may need to `chown`/`chmod` your SD card for the
`bake burn` to work.  All that's left is to plug our rpi2 in and have our minds
blown.

<iframe width="640" height="360" src="https://www.youtube.com/embed/txqA3c49iPo" frameborder="0"> </iframe>

As far as I know, in the `blinky` file you can comment out the `Logger` commands
that show up, looping, on screen and you'll end up in iex.

[np]: http://nerves-project.org/
[bake]: http://www.bakeware.io/
[fwup]: http://github.com/fhunleth/fwup
[ep]: https://github.com/nerves-project/nerves-examples
