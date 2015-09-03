apt-get -
lein -v
Leiningen 1.7.1 on Java 1.7.0_80 Java HotSpot(TM) 64-Bit Server VM


make a copy of https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein in a file called lein
3486  sudo mv ~/lein /usr/bin/lein
 3487  chmod a+x /usr/bin/lein
 3488  lein
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

 3489  lein -v
 Leiningen 2.5.2 on Java 1.7.0_80 Java HotSpot(TM) 64-Bit Server VM
 lein repl
 lein repl
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
