---
layout: post
title: "Notes on Clojure: Keywords & Symbols Confusion"
date: "2015-09-25"
tags: clojure

---

Having not used Clojure extensively yet, or really much at all, I'm still unsure as to when to use Keywords.  So I just spent a bit of time exploring them, and subsequently touched on Symbols too.  And in writing this post confused myself further in the process.  What you have here then is not a formed explanation of either Keywords or Symbols, but observations on their behaviour. While reading this, imagine me saying 'huh?' and scratching my head like Stan Laurel.

<!--more-->

Keywords are described in the [Clojure docs][cljdocs], within the Data Structures section, as such:

*"Keywords are symbolic identifiers that evaluate to themselves. They provide very fast equality tests. Like Symbols, they have names and optional namespaces, both of which are strings. The leading ':' is not part of the namespace or name.
Keywords implement IFn for invoke() of one argument (a map) with an optional second argument (a default value). For example (:mykey my-hash-map :none) means the same as (get my-hash-map :mykey :none)."*

And within the lein repl:

{% highlight clojure linenos %}
user=> (doc keyword)
-------------------------
clojure.core/keyword
([name] [ns name])
  Returns a Keyword with the given namespace and name.  Do not use :
  in the keyword strings, it will be added automatically.
{% endhighlight %}

Interestingly, on [Wikipedia][wikiID] identifiers are described as *"a name that identifies either a unique object or a unique class of objects"*, which immediately makes me think of Python variables or similar.  But that's not what these are, they don't hold another value, they evaluate to themselves, which kinda confuses me.  Anyway, let's get back in the lein repl and see what they're all about.

{% highlight clojure linenos %}
; create a keyword from a string using the keyword method
user=> (keyword "blues")
:blues
; check what type it is
user=> (type :blues)
clojure.lang.Keyword
; so far I've mostly seen keywords used as the key in
; maps, Clojure's key/value data structure
user=> {:a 1 :b 2 :c 3}
{:a 1, :b 2, :c 3}
user=> (type {:a 1 :b 2 :c 3})
clojure.lang.PersistentArrayMap
; and as the docs say we can use the keyword to get the relevant
; value from the map
user=> (:b {:a 1 :b 2 :c 3})
2
user=> (:b {:a 1 :b {:a 1 :b 2 :c 3}})
{:a 1, :b 2, :c 3}
user=> (:b (:b {:a 1 :b {:a 1 :b 2 :c 3}}))
2
; we can also use keywords as the values in a map
user=> {:a :1 :b :2}
{:a :1, :b :2}
user=> (:a {:a :1 :b :2})
:1
; we can use strings in place of keywords in a map
user=> {"a" 1 "b" 2}
{"a" 1, "b" 2}
user=> (type {"a" 1 "b" 2})
clojure.lang.PersistentArrayMap
user=> ("a" {"a" 1 "b" 2})
ClassCastException java.lang.String cannot be cast to clojure.lang.IFn  user/eval16637 (form-init7909990402899556571.clj:1)

user=> (get {"a" 1 "b" 2} "a")
1
; though we have to use get here, and it's a bit uglier.
; I also get the impression it's just common expectation to use keywords
; for map keys, and that frowning will occur if we use strings.
;
; we can also define keywords with the colon
user=> :blues
:blues
; and using a double colon also defines the namespace
user=> ::blues
:user/blues
user=> (type ::blues)
clojure.lang.Keyword
; which is similar to this
user=> (keyword "blues" "Mississippi-Fred-McDowell")
:blues/Mississippi-Fred-McDowell
user=> (type :blues/Mississippi-Fred-McDowell)
clojure.lang.Keyword
; though I don't think you can define the name of the namespace using
; the double colon method, and to be honest I don't yet know why you would
; do this I thought maybe it would have something to do with referencing a value
; in a different namespace, but I really don't understand this yet, I need
; to actually start building stuff in Clojure. Understanding is a constant
; cycle of learning and building and learning and building.
;
; I tried this....
user=> {:a 1 :b 2 :c 3}
{:a 1, :b 2, :c 3}
user=> (ns notuser)
nil
notuser=> :a
:a
notuser=> user/:a

CompilerException java.lang.RuntimeException: No such var: user/:a, compiling:(/tmp/form-init7909990402899556571.clj:1:6084)
notuser=> user/:b

CompilerException java.lang.RuntimeException: No such var: user/:b, compiling:(/tmp/form-init7909990402899556571.clj:1:6084)
notuser=> (ns user)
nil
user=> {::a 1 ::b 2 ::c 3}
{:user/a 1, :user/b 2, :user/c 3}
user=> (ns notuser)
nil
notuser=> :a
:a
notuser=> user/:a
CompilerException java.lang.RuntimeException: No such var: user/:a, compiling:(/tmp/form-init7909990402899556571.clj:1:6084)

notuser=> :user/a
:user/a
; but of course I then realised that this is just daft, because....
notuser=> (ns user)
nil
user=> :a
:a
; so many moments of stupidity along the path to enlightenment!
; Anyway just to reiterate, I don't quite get the why of all this yet
{% endhighlight %}

Symbols appear to be easily confused with Keywords in Clojure.

*"Symbols are identifiers that are normally used to refer to something else. They can be used in program forms to refer to function parameters, let bindings, class names and global vars. They have names and optional namespaces, both of which are strings. Symbols can have metadata (see with-meta).
Symbols, just like Keywords, implement IFn for invoke() of one argument (a map) with an optional second argument (a default value). For example ('mysym my-hash-map :none) means the same as (get my-hash-map 'mysym :none)."*

{% highlight clojure linenos %}
user=> (doc symbol)
-------------------------
clojure.core/symbol
([name] [ns name])
Returns a Symbol with the given namespace and name.

user=> (symbol foo)

CompilerException java.lang.RuntimeException: Unable to resolve symbol: foo in this context, compiling:(/tmp/form-init7909990402899556571.clj:1:1)
user=> (symbol 'foo)
foo
user=> (type (symbol 'foo))
clojure.lang.Symbol
user=> (symbol "foo")
foo
; keywords cannot be converted to symbols
user=> (symbol :foo)

ClassCastException clojure.lang.Keyword cannot be cast to java.lang.String  clojure.core/symbol (core.clj:552)
{% endhighlight %}

We can do some similar things with Symbols as we can with Keywords.

{% highlight clojure linenos %}
user=> {'a 1 'b 2 'c 3}
{a 1, b 2, c 3}
user=> (type {'a 1 'b 2 'c 3})
clojure.lang.PersistentArrayMap
user=> (type 'a)
clojure.lang.Symbol
user=> ('a {'a 1 'b 2})
1
; but we can assign other values to Symbols, where we can't with Keywords.
; This comes back to the description of Keywords as
; "symbolic identifiers that evaluate to themselves"
user=> (eval :blues)
:blues
; evaluates to itself
user=> (eval blues)
CompilerException java.lang.RuntimeException: Unable to resolve symbol: blues in this context, compiling:(/tmp/form-init7909990402899556571.clj:1:1)

; we have to assign something to blues
user=> (def blues "Muddy Waters")
#'user/blues
user=> (eval blues)
"Muddy Waters"
user=> blues
"Muddy Waters"
user=> (type blues)
java.lang.String
; yet we are unable to assign anything else to a keyword
user=> (def :blues "Muddy Waters")

CompilerException java.lang.RuntimeException: First argument to def must be a Symbol, compiling:(/tmp/form-init7909990402899556571.clj:1:1)
; Now I thought we could assign a value to a symbol, buuuuut...
user=> (def 'blues "Muddy Waters")
CompilerException java.lang.RuntimeException: First argument to def must be a Symbol, compiling:(/tmp/form-init7909990402899556571.clj:1:1)
user=> (defn 'blues [a] (str a " got the blues"))

IllegalArgumentException First argument to defn must be a symbol  clojure.core/defn--4156 (core.clj:281)
user=> (defn blues [a] (str a " got the blues"))
#'user/blues
user=> (blues "Muddy Waters")
"Muddy Waters got the blues"
user=> 'blues
blues
; So when assigning a value to a Symbol you don't use the quote.
user=> (symbol? 'blues)
true
user=> (symbol? (quote blues))
true
user=> (def blues "Muddy Waters")
#'user/blues
user=> (symbol? blues)
false
user=> (symbol? 'blues)
true
; Here blues is false as it refers to the string "Muddy Waters", whereas
; 'blues is the symbol
{% endhighlight %}

Keywords and Symbols are not the same thing.

{% highlight clojure linenos %}
user=> (keyword? 'blues)
false
user=> (keyword? :blues)
true
user=> (symbol? :blues)
false
user=> (symbol? 'blues)
true
user=> (:a {'a 1 'b 2})
nil
; as keywords refer to themselves they are identical, whereas symbols are not
user=> (identical? :a :a)
true
user=> (identical? 'a 'a)
false
; even though
user=> (= :a :a)
true
user=> (= 'a 'a)
true
; which, like above, has to do with the difference between the thing itself
; and what the thing is referencing.
; Here is an example of this difference
user=> (def blues ["Blind Willie Johnson" "Muddy Waters" "Sister Gertrude Morgan"])
#'user/blues
user=> blues
["Blind Willie Johnson" "Muddy Waters" "Sister Gertrude Morgan"]
user=> (type blues)
clojure.lang.PersistentVector
user=> 'blues
blues
user=> (type 'blues)
clojure.lang.Symbol
; yet the Symbol refers to the underlying list, not to itself, so if we
; reference the symbol we will always get back what it refers to
user=> (eval 'blues)
["Blind Willie Johnson" "Muddy Waters" "Sister Gertrude Morgan"]
user=> (eval blues)
["Blind Willie Johnson" "Muddy Waters" "Sister Gertrude Morgan"]
user=> (first '["Blind Willie Johnson" "Muddy Waters" "Sister Gertrude Morgan"])
"Blind Willie Johnson"
user=> (first blues)
"Blind Willie Johnson"
user=> (type '["Blind Willie Johnson" "Muddy Waters" "Sister Gertrude Morgan"])
clojure.lang.PersistentVector
user=> (type blues)
clojure.lang.PersistentVector
{% endhighlight %}

Keywords and Symbols can also be used within Vectors

{% highlight clojure linenos %}
user=> [1 2 3]
[1 2 3]
user=> (type [1 2 3])
clojure.lang.PersistentVector
user=> [1 "one" "two" 3]
[1 "one" "two" 3]
user=> [1 :one :two 3]
[1 :one :two 3]
user=> [1 :one :two 'three 3]
[1 :one :two three 3]
user=> (first (rest [1 :one :two 'three 3]))
:one
user=> ['one 1 :one :two 'three 3]
[one 1 :one :two three 3]
user=> (first ['one 1 :one :two 'three 3])
one
{% endhighlight %}

I also discovered that `'` & `"` are not interchangeable as they are in other languages, due to the use of `'` to define Symbols and Lists.

{% highlight clojure linenos %}
user=> "blues"
"blues"
user=> (type "blues")
java.lang.String
user=> 'blues'
blues'
user=> (type 'blues')
clojure.lang.Symbol
user=> (str 'blues')
"blues'"
user=> (str "blues")
"blues"
user=> ":blues"
":blues"
user=> (type ":blues")
java.lang.String
user=> (type ':blues')
clojure.lang.Keyword
user=> ':blues'
:blues'
{% endhighlight %}

The confusion is all mine, any insight is borrowed heavily from the following articles.

  - [Why does Clojure have “keywords” in addition to “symbols”?](https://stackoverflow.com/questions/1527548/why-does-clojure-have-keywords-in-addition-to-symbols?rq=1)
  - [ClojureDocs - Symbol](http://clojuredocs.org/clojure.core/symbol)
  - [ClojureDocs - Keyword](http://clojuredocs.org/clojure.core/keyword)
  - [Clojure For The Brave and True - Quoting](http://www.braveclojure.com/do-things/#2_10__Quoting)
  - [Clojure For The Brave and True - Keywords](http://www.braveclojure.com/do-things/#2_5__Keywords)
  - [Clojure For The Brave and True - Symbols & Naming](http://www.braveclojure.com/do-things/#2_9__Symbols_and_Naming)
  - [Symbols in Clojure](https://stackoverflow.com/questions/2320348/symbols-in-clojure)

[cljdocs]: http://clojure.org/data_structures#Data%20Structures-Keywords
[wikiID]: https://en.wikipedia.org/wiki/Identifier
