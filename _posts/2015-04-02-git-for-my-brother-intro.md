---
layout: post
title:  "Git, for my brother: Intro"
date:   2015-04-02
tags:   git
---

> In an attempt to better understand Git, I'm going to try and explain it to [my brother].[4]

<!--more-->
**1. What is Git?**

[Git][1] is a *Distributed Version Control System* created to keep track of the building and development of the core [Linux][2] operating system.  Git enables the reliable management of changes made to a project and keeps an accurate record of the evolution of the project.

**2. Right, so what is version control, then?**

Yeah, ok, it still sounds a bit like an obscure programmer's tool, right?  Version control is pretty much what it sounds like.

- You create a file: ***filename.doc***
- You change the file, add content to it, delete content from it: ***filename-VERSION 2.doc***
- You try something different to a section of the file, a *wild idea* that you're not sure you want to continue with: ***filename-VERSION 2b.doc***
- You do some more work on the file: ***filename-VERSION 3.doc***
- Your friend/colleague adds their idea/viewpoint/error checking to your file: ***filename-VERSION 3-James.doc***
- You continue with your previous *wild idea*: ***filename-VERSION 2c.doc***
- You like your *wild idea* and keep it in the file: ***filename-VERSION 3c.doc*** *(I think)*
- You get round to making changes based on your friend/colleague's suggestions: ***filename-VERSION 3c-updated.doc***
- You realise your *wild idea* is actually rubbish and discard it: ***filename-VERSION 4-LATEST.doc***

With a version control system like Git you don't have to worry about creating a confusing jumble of filenames.  Git allows you to just concentrate on your project and all the content within it, taking care of which version of the file you're working on, showing you the history of the changes that have been made and enabling you to try *wild ideas*, back out to previous versions and collaborate with others.

**3. Okay, I understand that, but I don't understand what Git actually is ... like is it a standalone program?**

Well, kinda, I guess it's more of a system, a command line tool than a program.  It's not like Microsoft Office, it's more a bunch of instructions to use, here let me show you;
In the Git command line on Windows I'm going to start a new project, in a folder called ***git-formybrother***, that contains a single file called ***test-file.txt***.  Don't worry about what I'm actually doing, I'll go into that later...

{% highlight bash %}
Welcome to Git (version 1.9.5-preview20141217)

Run 'git help git' to display the help index.
Run 'git help <command>' to display help for specific commands.

rob@laptop$ git init
Initialized empty Git repository in c:/Users/robertph/Documents/git-formybrother
/.git/

rob@laptop$ ls
test-file.txt

rob@laptop$ git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        test-file.txt

nothing added to commit but untracked files present (use "git add" to track)

rob@laptop$ git add test-file.txt

rob@laptop$ git commit -a -m "started new project"
[master (root-commit) 3110c11] started new project
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test-file.txt

rob@laptop$ git status
On branch master
nothing to commit, working directory clean
{% endhighlight %}
I realise not everyone is comfortable on the command line and this can scare them away, but it's OK, there are a number of different graphical user interfaces (GUIs), that can make it easier to deal with, and show a nice visual representation of your project, it's development and the changes made to it.  This in itself can provide an incredibly useful overview of what's going on in your project. Here's our new project in the *Github for Windows* client:

![Github for Windows screenshot]({{ site.url }}/assets/images/gfmb01-github-for-windows.png)

**4. Okay, but I'm not a software developer, do I really care?  Can I really be bothered?**

I'm not gonna lie, it's great but it's not magic, there's a learning curve involved.  Even so, once you get the basics you'll be creating and maintaining cleaner, clearer projects with more structure and accountability.  Have a go [here][3], see what you think...

[1]: https://git-scm.herokuapp.com/
[2]: http://www.linux.com/
[3]: https://try.github.io/levels/1/challenges/1
[4]: http://cargocollective.com/richardjamesphoenix/About
