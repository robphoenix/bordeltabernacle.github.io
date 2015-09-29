---
layout: post
title: 'Notes on Clojure: Basic Data Structures'
date: '2015-09-29 19:54'
tags: clojure
---

Let's have a wee gander at Clojure's data structures, shall we?

<!--more-->

Clojure has four fundamental data structures, also known as collections.

- *Lists* are, well, they're lists.
- *Vectors* are indexed lists.
- *Maps* are key/value collections.
- *Sets* are collections of unique values

All these collections are *immutable* and *persistent*, so when altering them after they've been created, adding or removing items, they're not actually changed, instead they create new, updated versions of themselves.

Lists & Vectors
---------------

To create a list we can use the explicit `list` function:

```clojure
> (list "Memphis Slim" "Elmore James" "Bukka White")
("Memphis Slim" "Elmore James" "Bukka White")
> (type (list "Memphis Slim" "Elmore James" "Bukka White"))
clojure.lang.PersistentList
```

Or more succintly using a quote:

```clojure
> '("Memphis Slim" "Elmore James" "Bukka White")
("Memphis Slim" "Elmore James" "Bukka White")
> (type '("Memphis Slim" "Elmore James" "Bukka White"))
clojure.lang.PersistentList
```

The list can contain different types of items:

```clojure
> '(1 2 :three "four")
(1 2 :three "four")
```

Vectors are similar to lists, but subtly different. We can create them with the explicit `vector` function, or just with square brackets...  

```clojure
> (vector "Memphis Slim" "Elmore James" "Bukka White")
["Memphis Slim" "Elmore James" "Bukka White"]
> ["Memphis Slim" "Elmore James" "Bukka White"]
["Memphis Slim" "Elmore James" "Bukka White"]
> (type ["Memphis Slim" "Elmore James" "Bukka White"])
clojure.lang.PersistentVector
> [1 2 :three "four"]
[1 2 :three "four"]
```

They are visually different, using square brackets, which may not seem like much but in a world of parenthesis can make a difference.  The contents are accessed differently.  To get an entry, a list works through from the beginning of the list to the entry, whereas a vector can directly access the entry by index.  To be honest I don't yet understand when you would use one over the other, but I'm sure I'll get a feel for them the more I work with Clojure.

So what can we do with these two types of collections?

The pretty much self-explanatory `first`, `last` and `rest` functions enable us to access the items in the collection.

So, with a List...

```clojure
> (first '("Memphis Slim" "Elmore James" "Bukka White"))
"Memphis Slim"
> (last '("Memphis Slim" "Elmore James" "Bukka White"))
"Bukka White"
> (rest '("Memphis Slim" "Elmore James" "Bukka White"))
("Elmore James" "Bukka White")
> (first (rest '("Memphis Slim" "Elmore James" "Bukka White")))
"Elmore James"
```

And with a Vector...

```clojure
> (first ["Memphis Slim" "Elmore James" "Bukka White"])
"Memphis Slim"
> (last ["Memphis Slim" "Elmore James" "Bukka White"])
"Bukka White"
> (rest ["Memphis Slim" "Elmore James" "Bukka White"])
("Elmore James" "Bukka White")
> (first (rest ["Memphis Slim" "Elmore James" "Bukka White"]))
"Elmore James"
```

We can see how many items are in the collection.

```Clojure
> (count '("Memphis Slim" "Elmore James" "Bukka White"))
3
> (count ["Memphis Slim" "Elmore James" "Bukka White"])
3
```

There are a couple of functions to build up our collections, `cons` and `conj`.

`cons` takes just one argument and adds the item to the front of the collection.

```clojure
; Lists
> (cons "Memphis Slim" '())
("Memphis Slim")
> (cons "Memphis Slim" "Elmore James" '())
clojure.lang.ArityException: Wrong number of args (3) passed to: core$cons
> (cons "Memphis Slim" (cons "Elmore James" '()))
("Memphis Slim" "Elmore James")
> (cons "Memphis Slim" nil)
("Memphis Slim")
; Vectors
> (cons "Memphis Slim" [])
("Memphis Slim")
> (cons "Memphis Slim" "Elmore James" [])
clojure.lang.ArityException: Wrong number of args (3) passed to: core$cons
> (cons "Memphis Slim" (cons "Elmore James" [])))
("Memphis Slim" "Elmore James")
```

Whereas `conj` can take a number of arguments and will add the items differently depending on the data structure; to the front of a List and to the end of a Vector.

```clojure
; Lists
> (conj '("Memphis Slim" "Elmore James" "Bukka White") "Robert Johnson")
("Robert Johnson" "Memphis Slim" "Elmore James" "Bukka White")
> (conj '("Memphis Slim" "Elmore James" "Bukka White") "Robert Johnson" "Bessie Smith")
("Bessie Smith" "Robert Johnson" "Memphis Slim" "Elmore James" "Bukka White")
; Vectors
> (conj ["Memphis Slim" "Elmore James" "Bukka White"] "Robert Johnson")
["Memphis Slim" "Elmore James" "Bukka White" "Robert Johnson"]
> (conj ["Memphis Slim" "Elmore James" "Bukka White"] "Robert Johnson" "Bessie Smith")
["Memphis Slim" "Elmore James" "Bukka White" "Robert Johnson" "Bessie Smith"]
```

`nth` allows you to get an item at a given index, taking a default argument if the request is out of range.

```clojure
; Lists
> (nth '("Memphis Slim" "Elmore James" "Bukka White") 0)
"Memphis Slim"
> (nth '("Memphis Slim" "Elmore James" "Bukka White") 1)
"Elmore James"
> (nth '("Memphis Slim" "Elmore James" "Bukka White") 4)
java.lang.IndexOutOfBoundsException
> (nth '("Memphis Slim" "Elmore James" "Bukka White") 4 "No Dice!")
"No Dice!"
; Vectors
> (nth ["Memphis Slim" "Elmore James" "Bukka White"] 0)
"Memphis Slim"
> (nth ["Memphis Slim" "Elmore James" "Bukka White"] 1)
"Elmore James"
> (nth ["Memphis Slim" "Elmore James" "Bukka White"] 4)
java.lang.IndexOutOfBoundsException
> (nth ["Memphis Slim" "Elmore James" "Bukka White"] 4 "No Dice!")
"No Dice!"
```

With Vectors we can also use the `get` function to retrieve items by index.

```clojure
; Lists have no index.
> (get '("Memphis Slim" "Elmore James" "Bukka White") 1)
nil
; Vectors do.
> (get ["Memphis Slim" "Elmore James" "Bukka White"] 1)
"Elmore James"
> (get ["Memphis Slim" "Elmore James" "Bukka White"] 5)
nil
> (get ["Memphis Slim" "Elmore James" "Bukka White"] 5 "No Dice!")
"No Dice!"
```

Maps
----

Maps provide us with a key/value store.  They are defined by the curly handlebar braces.

```clojure
> (type {})
clojure.lang.PersistentArrayMap
> {"Memphis Slim" "Miss Ida B" "Otis Spann" "Good Morning Mr. Blues"}
{"Memphis Slim" "Miss Ida B", "Otis Spann" "Good Morning Mr. Blues"}
> {"Memphis Slim" "Miss Ida B" "Otis Spann" "Good Morning Mr. Blues" "Bukka White"}
java.lang.RuntimeException: Map literal must contain an even number of forms
```

You don't need to include commas, but they may well help with understanding the code, visually separating the key/value pairs and ensuring you don't add a key without it's corresponding value.  I believe it's more common for keywords to be used for the keys in a map, which also provides an extra method for retrieving items from the map, alongside the `get` function.

```clojure
> (get {"Memphis Slim" "Miss Ida B", "Otis Spann" "Good Morning Mr. Blues"} "Memphis Slim")
"Miss Ida B"
> (get {:MemphisSlim "Miss Ida B" :OtisSpann "Good Morning Mr. Blues"} :OtisSpann)
"Good Morning Mr. Blues"
> (:OtisSpann {:MemphisSlim "Miss Ida B" :OtisSpann "Good Morning Mr. Blues"})
"Good Morning Mr. Blues"
```

We can access both the keys and the values easily enough...

```clojure
> (keys {:MemphisSlim "Miss Ida B", :OtisSpann "Good Morning Mr. Blues"})
(:MemphisSlim :OtisSpann)
> (vals {:MemphisSlim "Miss Ida B", :OtisSpann "Good Morning Mr. Blues"})
("Miss Ida B" "Good Morning Mr. Blues")
```

And add and remove key/value pairs with `assoc`, `dissoc` and `merge`...

```clojure
> (assoc {:MemphisSlim "Miss Ida B", :OtisSpann "Good Morning Mr. Blues"} :SkipJames "Worried Blues")
{:SkipJames "Worried Blues", :MemphisSlim "Miss Ida B", :OtisSpann "Good Morning Mr. Blues"}
> (dissoc {:MemphisSlim "Miss Ida B", :OtisSpann "Good Morning Mr. Blues", :SkipJames "Worried Blues"} :OtisSpann)
{:MemphisSlim "Miss Ida B", :SkipJames "Worried Blues"}
> (merge {:MemphisSlim "Miss Ida B", :SkipJames "Worried Blues"} {:RobertJohnson "I Believe I'll Dust My Broom", :LeroyCarr "Blues Before Sunrise"})
{:LeroyCarr "Blues Before Sunrise", :RobertJohnson "I Believe I'll Dust My Broom", :MemphisSlim "Miss Ida B", :SkipJames "Worried Blues"}
```

Sets
----

Sets are collections of unique data, defined by `#{}`.  They must be unique at the time of creation, and will not recognise repeated elements if you try to add them.

```clojure
> #{:MemphisSlim :OtisSpann :BukkaWhite :RobertJohnson :OtisSpann}
java.lang.IllegalArgumentException: Duplicate key: :OtisSpann
> #{:MemphisSlim :OtisSpann :BukkaWhite :RobertJohnson}
#{:RobertJohnson :MemphisSlim :OtisSpann :BukkaWhite}
> (conj #{:MemphisSlim :OtisSpann :BukkaWhite :RobertJohnson} :OtisSpann)
#{:RobertJohnson :MemphisSlim :OtisSpann :BukkaWhite}
```

Yep, `conj` can add to a set, and `disj` removes items.

```clojure
> (conj #{:MemphisSlim :OtisSpann :BukkaWhite :RobertJohnson} :SamChatmon)
#{:RobertJohnson :MemphisSlim :OtisSpann :SamChatmon :BukkaWhite}
> (disj #{:MemphisSlim :OtisSpann :BukkaWhite :RobertJohnson} :RobertJohnson)
#{:MemphisSlim :OtisSpann :BukkaWhite}
```

`contains?` can be used to check membership of the set, and `get` can get the item...

```clojure
> (contains? #{:RobertJohnson :MemphisSlim :OtisSpann :BukkaWhite} :BukkaWhite)
true
> (contains? #{:RobertJohnson :MemphisSlim :OtisSpann :BukkaWhite} :SamChatmon)
false
> (get #{:RobertJohnson :MemphisSlim :OtisSpann :BukkaWhite} :BukkaWhite)
:BukkaWhite
> (get #{:RobertJohnson :MemphisSlim :OtisSpann :BukkaWhite} :SamChatmon)
nil
> (get #{:RobertJohnson :MemphisSlim :OtisSpann :BukkaWhite} :SamChatmon "No Dice!")
"No Dice!"
```

`clojure.set` offers us some further functions for working with sets. `union` combines sets, `difference` removes items from the first set that exist in subsequent sets, and `intersection` returns items shared by both sets.

```clojure
> (clojure.set/union #{:RobertJohnson :MemphisSlim} #{:BukkaWhite :RobertJohnson})
#{:RobertJohnson :MemphisSlim :BukkaWhite}
> (clojure.set/difference #{:RobertJohnson :MemphisSlim} #{:BukkaWhite :RobertJohnson})
#{:MemphisSlim}
> (clojure.set/intersection #{:RobertJohnson :MemphisSlim} #{:BukkaWhite :RobertJohnson})
#{:RobertJohnson}
```

There we go, storing and retrieving data in Clojre, lovely.  As per, I learnt a lot of this from other more knowledgeable people than myself, most significantly [Carin Meier](https://twitter.com/gigasquid) and [Daniel Higginbotham](https://twitter.com/nonrecursive), as well as the good folks that give us the [Clojure Docs](http://clojure.org/documentation) and that contribute to the [community-driven docs](http://clojuredocs.org/). Thanks.
