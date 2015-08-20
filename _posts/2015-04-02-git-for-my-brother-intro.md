---

layout: post title: "Introducing my brother to Git" date: 2015-04-02

tags: git
---------

I recently bought a [book on Git](http://goo.gl/VFZV5v) Version Control, and my brother and girlfriend both looked at me weirdly and asked why I had bought a book on how to be a git. Har-de-har-har. I started trying to explain what it actually was, and my [brother](http://cargocollective.com/richardjamesphoenix/About) was kinda interested. So, this is an attempt to explain it to him, in order to better understand it myself.

<!--more-->

**What is Git?**

[Git](https://git-scm.herokuapp.com/) is a *Distributed Version Control System* created to keep track of the building and development of the core [Linux](http://www.linux.com/) operating system. It's used to reliably manage changes made to a project and keep an accurate record of the evolution of the project. *Git* is used mainly with programming [source code](http://www.linfo.org/source_code.html), but it can also be used with a variety of files, mainly text, you might want to manipulate and track over time.

**Oh right, so what's version control, then?**

Yeah, okay, it still sounds a bit like an obscure programmer's tool, right? Version control is pretty much what it sounds like, it's a method for managing the state of a project, through controlling the different versions created when changes are made to it. Here's a project you're working on:

-	You create a file and name it: `filename`
-	You change the file, add content to it, delete content from it, and create a new file with a similar but different name to differentiate it from, but associate it with, the original file: `filename-VERSION 2`
-	You try something different to a section of the file, a *wild idea* that you're not sure you want to continue with, and create another new, similar but slightly different, file: `filename-VERSION 2b`
-	You do some more work on the original file, without the *wild idea* in it, and when you're done create another file with a different name, so you know it has the most recent changes in it: `filename-VERSION 3`
-	You ask your friend to read through what you've done. They send back a file with their idea/viewpoint/error checking in it, renamed so you know it's different to your original file: `filename-VERSION 3-James`
-	Meanwhile, you continue with your previous *wild idea*, saving an updated version of it: `filename-VERSION 2c`
-	You like your *wild idea* and keep it in the file, so now it's part of the main, original file: `filename-VERSION 3c`
-	You get round to making changes based on your friend/colleague's suggestions: `filename-VERSION 3c-updated`
-	You realise your *wild idea* is actually rubbish and discard it: `filename-VERSION 4-LATEST`

So now you have a project folder with these files in: - `filename` - `filename-VERSION 2` - `filename-VERSION 2b` - `filename-VERSION 2c` - `filename-VERSION 3` - `filename-VERSION 3c` - `filename-VERSION 3c-updated` - `filename-VERSION 3-James` - `filename-VERSION 4-LATEST`

All of which are basically the same file with changes made to it. And to be honest you're not entirely sure if that last file has both the suggestions from your friend in it and the most recent changes and not the *wild idea* still in there. I've certainly been there before, and it's a messy uncertain experience. Add to this further collaboration with other project members and you've got a situation where no-one knows for sure if they're working on the most recent up-to-date version of the project files.

Arrrghhh *CATASTROPHE!*

With a version control system like *Git* you don't have to worry about creating a confusing jumble of filenames. *Git* allows you to just concentrate on your project and all the content within it. *Git* takes care of which version of the file you're working on, logs the history of the changes that have been made, enables you to try *wild ideas* without buggering up the original file, revert to previous versions and collaborate easily with others.

**Okay, I understand that, but I don't understand what Git actually is ... like is it a standalone program?**

Well, kinda, but it's more of a system than a program. Once *Git* is installed you'll have access to a set of instructions you can use in the [command line/terminal](https://en.wikipedia.org/wiki/Command-line_interface) of your computer.

Here, let me show you. In the *Git* command line on Windows I'm going to start a new project, in a folder called `git-formybrother`, that contains a single file called `test-file.txt`. Here, the `$` is a command prompt, where the instructions to the computer are typed.

{% highlight bash %} Welcome to Git (version 1.9.5-preview20141217)

Run 'git help git' to display the help index. Run 'git help <command>' to display help for specific commands.

$ git init # Initialise the Git repository Initialized empty Git repository in C:/git-formybrother/.git/

$ ls # List the contents of the folder/repository test-file.txt

$ git status # Check the status of the Git repository On branch master

Initial commit

Untracked files: (use "git add <file>..." to include in what will be committed)

```
    test-file.txt
```

nothing added to commit but untracked files present (use "git add" to track)

$ git add test-file.txt # Add test-file.txt to Git to be tracked

$ git commit -am "started new project" # Commit the current state of the files, with a short message, to your Git history[master (root-commit) 3110c11] started new project 1 file changed, 0 insertions(+), 0 deletions(-) create mode 100644 test-file.txt

$ git status # Check again the status of the Git repository On branch master nothing to commit, working directory clean {% endhighlight %} So basically, we've just started a new project to be tracked by *Git*, added a file to it and commited/saved the state of the project to the *Git* log.

I realise not everyone is comfortable on the command line and this can scare them away, but there are a number of different graphical user interfaces [(GUIs)](https://www.git-scm.com/downloads/guis), that can make it easier to deal with, and show a nice visual representation of your project, it's development and the changes made to it. This in itself can provide an incredibly useful overview of what's going on in your project. Here's our new project in the *Github for Windows* client:

![Github for Windows screenshot]({{ site.url }}/assets/images/gfmb01-github-for-windows.png)

**Okay, but I'm not a software developer, do I really care? Can I really be bothered?**

I'm not gonna lie, it's great but it's not magic, there's a learning curve involved, and to be honest I don't know if it's a sensible way of managing more complicated files like [MS Office files](http://blog.martinfenner.org/2014/08/25/using-microsoft-word-with-git/), but you should avoid MS Office anyway. Even so, once you get the basics you'll be creating and maintaining cleaner, clearer projects with more structure and accountability. Have a go [here](https://try.github.io/levels/1/challenges/1), see what you think...
