---
layout: post
title: "Notes on Clojure: Dipping My Toes In"
date: "2015-09-04"
tags: clojure

---

Maybe because I'm such a late arrival to this programming pastime, I feel a certain inadequacy, a need to over compensate.  Or maybe I just find it exciting learning new things, and I just want to get deeper and consume everything.  Anyway, I've been getting more interested in functional programming, I don't know why.  I'm not so embedded in object-oriented, and I've been trying to understand JavaScript's prototype-based programming, and if I'm honest, these concepts aren't yet meaningful enough to me to matter so much outside of syntax and the practicalities of how to write code, yet functional programming seems to make a certain amount of sense. Perhaps this will become an endeavour in proving myself wrong. So, I listened to [Carin Meier][cm] on [The Changelog][cl] and bought [her book][lc], and am going to start learning Clojure.

<!--more-->

[Leiningen][lngen] is the tool Carin recommends for getting started with Clojure, so let's install that.
Check Java is installed first:

{% highlight bash linenos %}
$ java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
{% endhighlight %}

Initially I used my package manager to install Leiningen, but this installed an old version.

{% highlight bash linenos %}
$ sudo apt-get update
$ sudo apt-get leiningen
$ lein -v
Leiningen 1.7.1 on Java 1.7.0_80 Java HotSpot(TM) 64-Bit Server VM
{% endhighlight %}

So, I uninstalled that, and followed the instructions on the Leiningen site.  Copy and Paste the contents of the [lein][lein] file, into a file called `lein`.  Move the file into my `$PATH`, and make it executable.

{% endhighlight %}
$ sudo apt-get remove leiningen # remove apt-get installed lein
$ vim ~/lein # copy and paste the contents of the lein file
$ echo $PATH # check path
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
:/usr/lib/jvm/java-7-oracle/bin:/usr/lib/jvm/java-7-oracle/db/bin:/usr/lib/jvm/java-7-or
acle/jre/bin:/usr/lib/jvm/java-7-oracle/bin:/usr/lib/jvm/java-7-oracle/db/bin:/usr/lib/j
vm/java-7-oracle/jre/bin
$ mv ~/lein /usr/bin/lein # move the lein file into my $PATH
$ chmod a+x /usr/bin/lein # make it executable
$ lein # aaaaaand run it!
 Downloading Leiningen to /home/willem/.lein/self-installs/leiningen-2.5.2-standalone.jar now...                                                                       [1050/1997]
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   406    0   406    0     0    653      0 --:--:-- --:--:-- --:--:--   652
100 15.0M  100 15.0M    0     0  4160k      0  0:00:03  0:00:03 --:--:-- 6334k
Leiningen is a tool for working with Clojure projects.

Several tasks are available:
change              Rewrite project.clj by applying a function.
check               Check syntax and warn on reflection.
classpath           Print the classpath of the current project.
clean               Remove all files from project's target-path.
compile             Compile Clojure source into .class files.
deploy              Build and deploy jar to remote repository.
deps                Download all dependencies.
do                  Higher-order task to perform other tasks in succession.
help                Display a list of tasks or help for a given task.
install             Install the current project to the local repository.
jar                 Package up all the project's files into a jar file.
javac               Compile Java source files.
new                 Generate project scaffolding based on a template.
plugin              DEPRECATED. Please use the :user profile instead.
pom                 Write a pom.xml file to disk for Maven interoperability.
release             Perform :release-tasks.
repl                Start a repl session either with the current project or standalone.
retest              Run only the test namespaces which failed last time around.
run                 Run a -main function with optional command-line arguments.
search              Search remote maven repositories for matching jars.
show-profiles       List all available profiles or display one if given an argument.
test                Run the project's tests.
trampoline          Run a task without nesting the project's JVM inside Leiningen's.
uberjar             Package up the project files and dependencies into a jar file.
update-in           Perform arbitrary transformations on your project map.
upgrade             Upgrade Leiningen to specified version or latest stable.
vcs                 Interact with the version control system.
version             Print version for Leiningen and the current JVM.
with-profile        Apply the given task with the profile(s) specified.

Run `lein help $TASK` for details.

Global Options:
  -o             Run a task offline.
  -U             Run a task after forcing update of snapshots.
  -h, --help     Print this help or help for a specific task.
  -v, --version  Print Leiningen's version.

See also: readme, faq, tutorial, news, sample, profiles, deploying, gpg,
mixed-source, templates, and copying.

$
$ lein -v # check the version again, 2.5.2 > 1.7.1, super.
Leiningen 2.5.2 on Java 1.7.0_80 Java HotSpot(TM) 64-Bit Server VM
$
$ lein repl # Fire up the REPL, first time this did some more stuff, see below
            # but subsequent times it didn't, no worries.
Retrieving org/clojure/tools.nrepl/0.2.10/tools.nrepl-0.2.10.pom from central
Retrieving org/clojure/pom.contrib/0.1.2/pom.contrib-0.1.2.pom from central
Retrieving org/sonatype/oss/oss-parent/7/oss-parent-7.pom from central
Retrieving clojure-complete/clojure-complete/0.2.3/clojure-complete-0.2.3.pom from clojars
Retrieving org/clojure/clojure/1.7.0/clojure-1.7.0.pom from central
Retrieving org/clojure/clojure/1.7.0/clojure-1.7.0.jar from central
Retrieving org/clojure/tools.nrepl/0.2.10/tools.nrepl-0.2.10.jar from central
Retrieving clojure-complete/clojure-complete/0.2.3/clojure-complete-0.2.3.jar from clojars
nREPL server started on port 33732 on host 127.0.0.1 - nrepl://127.0.0.1:33732
REPL-y 0.3.7, nREPL 0.2.10
Clojure 1.7.0
Java HotSpot(TM) 64-Bit Server VM 1.7.0_80-b15
   Docs: (doc function-name-here)
         (find-doc "part-of-name-here")
 Source: (source function-name-here)
Javadoc: (javadoc java-object-or-class-here)
   Exit: Control+D or (exit) or (quit)
Results: Stored in vars *1, *2, *3, an exception in *e

user=>
user=> quit
Bye for now!
$
{% endhighlight %}

So the following is basically just me following along with Carin's book, where she starts to introduce Clojure.  So all credit goes to her, I'm just a messenger.

{% highlight bash linenos %}
$ lein new wonderland # start a new Clojure project
Generating a project called wonderland based on the 'default' template.
The default template is intended for library projects, not applications.
To see other templates (app, plugin, etc), try `lein help new`.
$ cd wonderland/ # change into it
/wonderland$ lein repl # and get the REPL going again
nREPL server started on port 50609 on host 127.0.0.1 - nrepl://127.0.0.1:50609
REPL-y 0.3.7, nREPL 0.2.10
Clojure 1.7.0
Java HotSpot(TM) 64-Bit Server VM 1.7.0_80-b15
    Docs: (doc function-name-here)
          (find-doc "part-of-name-here")
  Source: (source function-name-here)
 Javadoc: (javadoc java-object-or-class-here)
    Exit: Control+D or (exit) or (quit)
 Results: Stored in vars *1, *2, *3, an exception in *e

user=>
{% endhighlight %}

{% highlight clojure linenos %}
; Some basic number stuff. Clojure is littered with parenthesis, you get used to
; it.  Clojure structures things differently to what I'm used to.  The operator
; goes first, then the parameters it takes.  I really quite like this, it's a
; nice way of organising things, though I can see it might annoy some people.
user=> (+ 1 1)
2
user=> 42
42
user=> 6/3  ; Clojure will reduce a ratio if it can.
            ; This is different to division
2
user=> 8/3  ; And won't if it can't, but will leave it as a ratio, rather than
            ; change it to a decimal
8/3
user=> 6.0/3 ; you can't use decimals in a ratio.  
NumberFormatException Invalid number: 6.0/3  clojure.lang.LispReader.readNumber (LispReader.java:330)

user=> 6/3.0
NumberFormatException Invalid number: 6/3.0  clojure.lang.LispReader.readNumber (LispReader.java:330)

user=> 6.0/3.0

NumberFormatException Invalid number: 6.0/3.0  clojure.lang.LispReader.readNumber (LispReader.java:330)
user=> (/ 6 3)
2
user=> (/ 3 6) ; Dividing whole numbers will result in a ratio
1/2
user=> (/ 3.0 6.0) ; To get a decimal result, use a decimal in the equation
0.5
user=> (/ 3.0 6)
0.5
user=> "blues" ; yep, that's a string
"blues"
user=> blues
CompilerException java.lang.RuntimeException: Unable to resolve symbol: blues in this context, compiling:(/tmp/form-init7599042722842248080.clj:1:1010)

user=> true ; Ain't nothing going on but a boolean, yo.
true
user=> nil ; nil is the absence of a value, like None in Python
nil
user=> (+ 5 (- 4 2)) ; Here's the beginning of the growth of the parens
7
user=> '(5 "blues" :music) ; A list is denoted with a '
(5 "blues" :music)
user=> (first '(5, "blues", :music)) ; first gives us the first list item
5
user=> (rest '(5, "blues", :music)) ; and rest, the rest
("blues" :music)
user=> (first (rest '(5, "blues", :music))) ; we can nest these fuctions
"blues"
user=> (cons "down" '(5, "blues", :music)) ; and use cons to add to our list
("down" 5 "blues" :music)
user=> (cons "down" (cons 7 '(5, "blues", :music))) ; and nest our cons
("down" 7 5 "blues" :music)
{% endhighlight %}

That's as far as I got last night. I know it's not far, but having kids makes me tired.  And I just want to reiterate again, this is basically just regurgitating Carin's work, go [buy her book][lc].

Interestingly, the name *Leiningen* comes from the short story [Leiningen versus the Ants][story].  A tale of plantation owner Leiningen's battle against an invading army of flesh-eating ants.  It's a really good read, triumph over adversity, the importance of intelligence and resourcefulness, I highly recommend you [read][story] it.

[cm]: https://twitter.com/gigasquid
[cl]: https://changelog.com/171/
[lc]: http://www.amazon.co.uk/Living-Clojure-Carin-Meier/dp/1491909048/ref=sr_1_1?ie=UTF8&qid=1441319254&sr=8-1&keywords=living+clojure
[lngen]: http://leiningen.org/
[story]: http://www.classicshorts.com/stories/lvta.html
[lein]: https://github.com/technomancy/leiningen/blob/stable/bin/lein
