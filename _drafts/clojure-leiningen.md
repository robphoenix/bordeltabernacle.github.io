
Maybe, because I'm such a late arrival to this programming pastime, I feel a certain inadequacy, a need to over compensate.  Or maybe I just find it exciting learning new things, and I just want to get deeper and consume everything.  Anyway, I've been getting more interested in functional programming, I don't know why.  I'm not so embedded in object-oriented, and I've been trying to understand JavaScript's prototype-based programming, and if I'm honest, these concepts aren't yet meaningful enough to me to matter so much outside of syntax and the practicalities of how to write code, yet functional programming seems to make a certain amount of sense. Perhaps this will become an endeavour in proving myself wrong. So, I listened to [Carin Meier][cm] on [The Changelog][cl] and bought [her book][lc], and am going to start learning Clojure.


```bash
$ sudo apt-get leiningen
$ lein -v
Leiningen 1.7.1 on Java 1.7.0_80 Java HotSpot(TM) 64-Bit Server VM
```

make a copy of https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein in a file called lein

```bash
$ mv ~/lein /usr/bin/lein
$ chmod a+x /usr/bin/lein
$ lein
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
$ lein -v
Leiningen 2.5.2 on Java 1.7.0_80 Java HotSpot(TM) 64-Bit Server VM
$
$ lein repl
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
```

```bash
$ lein new wonderland
Generating a project called wonderland based on the 'default' template.
The default template is intended for library projects, not applications.
To see other templates (app, plugin, etc), try `lein help new`.
$ cd wonderland/
/wonderland$ lein repl
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
```

```clojure
user=> (+ 1 1)
2
user=> 42
42
user=> 6/3
2
user=> 8/3
8/3
user=> 6.0/3
NumberFormatException Invalid number: 6.0/3  clojure.lang.LispReader.readNumber (LispReader.java:330)

user=> 6/3.0
NumberFormatException Invalid number: 6/3.0  clojure.lang.LispReader.readNumber (LispReader.java:330)

user=> 6.0/3.0

NumberFormatException Invalid number: 6.0/3.0  clojure.lang.LispReader.readNumber (LispReader.java:330)
user=> (/ 6 3)
2
user=> (/ 3 6)
1/2
user=> (/ 3.0 6.0)
0.5
user=> (/ 3.0 6)
0.5
user=> "blues"
"blues"
user=> blues
CompilerException java.lang.RuntimeException: Unable to resolve symbol: blues in this context, compiling:(/tmp/form-init7599042722842248080.clj:1:1010)

user=> :blues
:blues
user=> \b
\b
user=> true
true
user=> nil
nil
user=> (+ 5 (- 4 2))
7
user=> '(5 "blues" :music)
(5 "blues" :music)
user=> '(5, "blues", :music)
(5 "blues" :music)
user=> (first '(5, "blues", :music))
5
user=> (rest '(5, "blues", :music))
("blues" :music)
user=> (first (rest '(5, "blues", :music)))
"blues"
user=> (cons "down" '(5, "blues", :music))
("down" 5 "blues" :music)
user=> (cons "down" (cons 7 '(5, "blues", :music)))
("down" 7 5 "blues" :music)
```

[cm]: https://twitter.com/gigasquid
[cl]: https://changelog.com/171/
[lc]: http://www.amazon.co.uk/Living-Clojure-Carin-Meier/dp/1491909048/ref=sr_1_1?ie=UTF8&qid=1441319254&sr=8-1&keywords=living+clojure
