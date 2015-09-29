Look into what is included when you start a new clojure project.

what are :keywords

Clojure collections (data structures), and the various functions to use with them.
- lists - ' & (list), (sort), (max), (map), (apply)
- vectors
- maps
- sets


Functional programming's immutable feature.  Create new collection rather than changing existing one.

Symbols - like variables, bind data to a name. `def` & `let`
Namespaces - `require`
Functions - `defn` / `fn`
Print - `println`
Docs - (doc)

------------------
Clojure has four fundamental data types, also known as collections.

- *Lists* are, well, they're lists.
- *Vectors* are indexed lists.
- *Maps* are a key/value collection.
- *Sets* are collections of unique values

All these collections are immutable and persistent, so when altering them after they've been created, adding or removing items, they're not actually changed, instead they create new, updated versions of themselves.

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

Vectors are similar to lists, but subtly different. We create them with square brackets...  

```clojure
> ["Memphis Slim" "Elmore James" "Bukka White"]
["Memphis Slim" "Elmore James" "Bukka White"]
> (type ["Memphis Slim" "Elmore James" "Bukka White"])
clojure.lang.PersistentVector
> [1 2 :three "four"]
[1 2 :three "four"]
```

They are visually different, using square brackets, which may not seem like much but in a world of parenthesis can make a difference.  The contents are accessed differently.  To get an entry a list works through from the beginning of the list to the entry, whereas a vector can directly access the entry by index.  To be honest I don't yet understand when you would use one over the other, but I'm sure I'll get a feel for them the more I work with Clojure.

So what can we do with these two types of collections?

The pretty much self-explanatory 'first', 'last' and 'rest' functions enable us to access the items in the collection.

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

Maps provide us with a key/value store.  They are defined by the curly handlebar braces.

```clojure
> (type {})
clojure.lang.PersistentArrayMap
> {"Memphis Slim" "Miss Ida B" "Otis Spann" "Good Morning Mr. Blues"}
{"Memphis Slim" "Miss Ida B", "Otis Spann" "Good Morning Mr. Blues"}
> {"Memphis Slim" "Miss Ida B" "Otis Spann" "Good Morning Mr. Blues" "Bukka White"}
java.lang.RuntimeException: Map literal must contain an even number of forms
```

You don't need to include commas, but they may well help with visually understanding the code, ensuring you don't add a key without it's corresponding value.  I believe it's more likely that keywords will be used for the keys in a map, which also means you hae an extra way of retrieving items from the map, alongiside the `get` function.

```clojure
> {:MemphisSlim "Miss Ida B" :OtisSpann "Good Morning Mr. Blues"}
{:MemphisSlim "Miss Ida B", :OtisSpann "Good Morning Mr. Blues"}
> (get {:MemphisSlim "Miss Ida B" :OtisSpann "Good Morning Mr. Blues"} :OtisSpann)
"Good Morning Mr. Blues"
> (:OtisSpann {:MemphisSlim "Miss Ida B" :OtisSpann "Good Morning Mr. Blues"})
"Good Morning Mr. Blues"
> (get {"Memphis Slim" "Miss Ida B", "Otis Spann" "Good Morning Mr. Blues"} "Memphis Slim")
"Miss Ida B"
```
