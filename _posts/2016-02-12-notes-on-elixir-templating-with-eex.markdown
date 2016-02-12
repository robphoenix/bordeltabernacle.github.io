---
title: "Notes on Elixir: Templating with EEx"
layout: post
tags: elixir

---

EEx is to Elixir what [Jinja][j] is to Python.  It enables the use of Elixir
code inside strings and files.  It's used extensively in the Phoenix framework for HTML
templates, and recently I've started to use it to replace my Python scripts for generating
Cisco config files.

<!--more-->

Like most templating engines EEx has various tags it uses for marking up your
template:

- `<% content %>` Contains Elixir code, but won't output anything to the template
- `<%= content %>` That extra `=` in there means the result of any elixir inside the tag
  will appear in the template.
- `<%% content %>` & `<%%= content %>` This is a quotation, returning, rather
  than evaluating the contents.
- `<%# content %>` Comments, discarded in evaluation.

These tags, in the context of a template are evaluated by one of 4 functions, or
2 macros:

- `compile_file/2` & `compile_string/2` These generate a *quoted expression* from
  a file or a string respectively, essentially creating an Abstract Syntax Tree
  representation of the input.
- `eval_file/3` & `eval_string` Evaluate the given file or string respectively,
  generating output based on the given template and variable bindings.
- `function_from_file/5` & `function_from_string/5` These macros generate
  functions from the given file or string respectively, along with any arguments. These
  functions can then be used in other code to generate output from the given
  template and arguments.
  
So, examples;
  
{% highlight elixir linenos %}
iex> string = "Hello, <%= name %>!"
"Hello, <%= name %>!"
iex> EEx.eval_string string, [name: "World"]
"Hello, World!"
{% endhighlight %}

Here, `string` is bound to a simple template, with a variable name inside the
tag.  We then evaluate the string, passing in a keyword list of arguments, and
hey presto, in our output the `name` variable has been replaced! Not so
different from string interpolation really. Except we have access to all of
Elixir inside the tag;

{% highlight elixir linenos %}
iex> template = "<%= if n > 100 do %> \
...> <%= n %> is greater than 100. \
...> <% else %> \
...> <%= n %> is lower than 100. \
...> <% end %>"
"<%= if n > 100 do %>n is greater than 100.<% else %>n is lower than 100.<% end %>"
iex> EEx.eval_string template, [n: 10]
" 10 is lower than 100. "
iex> EEx.eval_string template, [n: 101]
" 101 is greater than 100. "
{% endhighlight %}

Notice the `<% %>` tags for the `else` & `end` of the `if` statement. The
content of these tags is evaluated, but never returned to the template. We'll
get a compiler warning if we include an unused variable in there, or an error if
we have an unbound variable, otherwise any results just won't appear in our
final output;

{% highlight elixir linenos %}
iex> string = "Hello, <% name %>!"
"Hello, <% name %>!"
iex> EEx.eval_string string, [name: "World"]
"Hello, !"
nofile:1: warning: variable name in code block has no effect as it is never returned (remove the variable or assign it to _ to avoid warnings)
iex> EEx.eval_string string
** (CompileError) nofile:1: undefined function name/0
    (elixir) expanding macro: Kernel.<>/2
             nofile:1: (file)
iex> string = "Hello, <% 2 + 2 %>!"
iex> EEx.eval_string string
"Hello, !"
iex> string = "Hello, <%= 2 + 2 %>!"
iex> EEx.eval_string string
"Hello, 4!"
{% endhighlight %}

The quotation tag is interesting, it's almost like a raw string indicator,
telling EEx to not evaluate the tag.  I've not had cause to use it yet, but I
can see it might be useful for some kind of two-stage templating;

{% highlight elixir linenos %}
iex> string = "Hello, <%% 2 + 2 %>!"
"Hello, <%% 2 + 2 %>!"
iex> EEx.eval_string string
"Hello, <% 2 + 2 %>!"
iex> string = "Hello, <%%= 2 + 2 %>!"
"Hello, <%%= 2 + 2 %>!"
iex> output = EEx.eval_string string
"Hello, <%= 2 + 2 %>!"
iex> EEx.eval_string output
"Hello, 4!"
{% endhighlight %}

When first evaluated the quotation tag becomes a regular tag, which can then be
evaluated again if necessary.

So, we've seen `eval_string/2` in the previous examples. `eval_file/2` is much
the same but takes a file as it's template rather than a string;

{% highlight elixir linenos %}
iex> File.write("example.txt", "Hello, <%= name %>!")
:ok
iex(52)> EEx.eval_file "example.txt", [name: "World"]
"Hello, World!"
iex(53)> File.read("example.txt")
{:ok, "Hello, <%= name %>!"}
{% endhighlight %}

Note that the original template file has not been changed, EEx has just created
a new output from the combination of the template and the given variable
bindings. Also, the template file here was just a `.txt` file rather than a
`.eex` file. The template file isn't required to be a `.eex` file, but
convention is to combine the two extensions, for instance `.txt.eex` or
`.html.eex`, as this *'preserves its intent as a template, and also denotes what
it's outputting'*, and helps with syntax highlighting too. (thanks asonge &
ciastek)

The `compile_string/2` & `compile_file/2` functions are fairly straightforward,
generating AST;

{% highlight elixir linenos %}
iex> string = "Hello, <%= 2 + 2 %>!"
"Hello, <%= 2 + 2 %>!"
iex(55)> EEx.compile_string string
{:<>, [context: EEx.Engine, import: Kernel],
 [{:__block__, [],
   [{:=, [],
     [{:tmp1, [], EEx.Engine},
      {:<>, [context: EEx.Engine, import: Kernel], ["", "Hello, "]}]},
    {:<>, [context: EEx.Engine, import: Kernel],
     [{:tmp1, [], EEx.Engine},
      {{:., [],
        [{:__aliases__, [alias: false], [:String, :Chars]}, :to_string]}, [],
       [{:+, [line: 1], [2, 2]}]}]}]}, "!"]}
{% endhighlight %}

Alhough I've had no use for them myself, they are actually used within the
`eval_string/3` & `eval_file/3` functions.

Last, but by no means least, are the `function_from_file/5` &
`function_from_string/5` macros. These basically do what they sound like they
do; create a function from a file or string template for use elsewhere. I'll
illustrate this with an example.

Let's say we have a very large number of devices that we need to generate
individual configurations for, in my experience this would be for Cisco switches
and routers.  We create a template, "base_example.conf.eex":

{% highlight elixir linenos %}
hostname <%= hostname %>
no logging console
username <%= user_name %> secret <%= user_password %>
aaa new-model
aaa authentication login default local
clock timezone GMT 0 0
clock summer-time BST recurring last Sun Mar 1:00 last Sun Oct 1:00
ip name-server <%= name_server %>
no ip http server
no ip http secure-server
ntp server <%= ntp_server %>
ntp update-calendar
!
<%= for [id, name] <- vlans do %>
vlan <%= id %>
  name <%= name %>
<% end %>
!
interface 0
ip address <%= ip_address %>
no shutdown
{% endhighlight %}

This is a very basic template, with some tags expecting variables. There is also
a list comprehension towards the end, this expects `vlans` to be a list of 2
element lists, that it will then map over, creating as many vlan/name pairs as
necessary.  This is really useful for situations where you might have a variable
amount of input data, saving you from having to adjust the template each time 
according to, in this case, the number of vlans. 

So, I've saved this template in a *templates* directory. Let's dip into iex and
see what we can do:

{% highlight elixir linenos %}
iex> defmodule Render do
...>   require EEx
...>   EEx.function_from_file(:def, :base,
...>     Path.expand("./templates/base_example.conf.eex"),
...>     [:hostname, :ip_address, :name_server, :ntp_server,
...>      :user_name, :user_password, :vlans])
...> end
{:module, Render,
 <<70, 79, 82, 49, 0, 0, 14, 184, 66, 69, 65, 77, 69, 120, 68, 99, 0, 0, 1, 26, 131, 104, 2, 100, 0, 14, 101, 108, 105, 120, 105, 114, 95, 100, 111, 99, 115, 95, 118, 49, 108, 0, 0, 0, 4, 104, 2, ...>>,
 {:base, 7}}
{% endhighlight %}

Here, we start a new module called Render, `require EEx`, and then use the
`function_from_file/5` macro, passing it the type of function we want (`def` or
`defp`), the name of the function, the template we're using, and our arguments.

We now have a function we can use to render our template:

{% highlight elixir linenos %}
iex> Render.base(
...>   "ABC20",
...>   "192.168.1.20",
...>   "8.8.8.8",
...>   "uk.pool.ntp.org",
...>   "admin",
...>   "password",
...>   [["999", "NATIVE"], ["100", "VOICE"], ["101", "DATA 1"], ["102", "DATA 2"], ["103", "DATA 3"]]
...> )
"hostname ABC20\nno logging console\nusername admin secret password\naaa new-model\naaa authentication login default local\nclock timezone GMT 0 0\nclock summer-time BST recurring last Sun Mar 1:00 last Sun Oct 1:00\nip name-server 8.8.8.8\nno ip http server\nno ip http secure-server\nntp server uk.pool.ntp.org\nntp update-calendar\n!\n\nvlan 999\n  name NATIVE\n\nvlan 100\n  name VOICE\n\nvlan 101\n  name DATA 1\n\nvlan 102\n  name DATA 2\n\nvlan 103\n  name DATA 3\n\n!\ninterface 0\nip address 192.168.1.20\nno shutdown\n"
{% endhighlight %}

We pass our device variables in to our new function, notice the list of lists
for `vlans`, and out pops our rendered template, including all our vlans,
lovely. Now, obviously, this isn't particularly practical when dealing with
thousands of devices. But what you have is a function you can use as part of a
module that can read data from a csv file, I've found [ex_csv][csv] to be pretty
good for this, and map over it, applying your new rendering function, and then
writing the results to a new file.

But there's more. Passing in that list of data is pretty cumbersome, it'd be
nice if we could just define a single variable that represents a map, and let
the template grab what it needs from that. First, we need to change our template
to look like this:

{% highlight elixir linenos %}
hostname <%= @hostname %>
no logging console
username <%= @user_name %> secret <%= @user_password %>
aaa new-model
aaa authentication login default local
clock timezone GMT 0 0
clock summer-time BST recurring last Sun Mar 1:00 last Sun Oct 1:00
ip name-server <%= @name_server %>
no ip http server
no ip http secure-server
ntp server <%= @ntp_server %>
ntp update-calendar
!
<%= for [id, name] <- @vlans do %>
vlan <%= id %>
  name <%= name %>
<% end %>
!
interface 0
ip address <%= @ip_address %>
no shutdown
{% endhighlight %}

We're now using EEx's `@` macro, which enables us to grab data out of a map or
keyword list.

We'll update our `Render.base` function, like so:

{% highlight elixir linenos %}
iex> defmodule Render do
...>   require EEx
...>   EEx.function_from_file(:def, :base,
...>     Path.expand("./templates/base_example.conf.eex"),
...>     [:assigns])
...> end
iex: warning: redefining module Render
{:module, Render,
 <<70, 79, 82, 49, 0, 0, 15, 8, 66, 69, 65, 77, 69, 120, 68, 99, 0, 0, 0, 151, 131, 104, 2, 100, 0, 14, 101, 108, 105, 120, 105, 114, 95, 100, 111, 99, 115, 95, 118, 49, 108, 0, 0, 0, 4, 104, 2, ...>>,
 {:base, 1}}
{% endhighlight %}

and create a map of our data:

{% highlight elixir linenos %}
iex> device = %{hostname: "ABC20",
...>   ip_address: "192.168.1.20",
...>   name_server: "8.8.8.8",
...>   ntp_server: "uk.pool.ntp.org",
...>   user_name: "admin",
...>   user_password: "password",
...>   vlans: [["999", "NATIVE"], ["100", "VOICE"], ["101", "DATA 1"],
...>    ["102", "DATA 2"], ["103", "DATA 3"]]}
%{hostname: "ABC20", ip_address: "192.168.1.20", name_server: "8.8.8.8",
  ntp_server: "uk.pool.ntp.org", user_name: "admin", user_password: "password",
  vlans: [["999", "NATIVE"], ["100", "VOICE"], ["101", "DATA 1"],
   ["102", "DATA 2"], ["103", "DATA 3"]]}
{% endhighlight %}

Now, let's see if it works...

{% highlight elixir linenos %}
iex> Render.base(device)
"hostname ABC20\nno logging console\nusername admin secret password\naaa new-model\naaa authentication login default local\nclock timezone GMT 0 0\nclock summer-time BST recurring last Sun Mar 1:00 last Sun Oct 1:00\nip name-server 8.8.8.8\nno ip http server\nno ip http secure-server\nntp server uk.pool.ntp.org\nntp update-calendar\n!\n\nvlan 999\n  name NATIVE\n\nvlan 100\n  name VOICE\n\nvlan 101\n  name DATA 1\n\nvlan 102\n  name DATA 2\n\nvlan 103\n  name DATA 3\n\n!\ninterface 0\nip address 192.168.1.20\nno shutdown\n"
{% endhighlight %}

Ahh, that's much easier, and cleaner, super. One more thing before I go, I
really liked with Python's Jinja how you were able to split up your templates,
much like Phoenix does. We don't have access to Phoenix's `render` function, but
we can mimic it.

We'll start by pulling out the vlans section into it's own template,
`vlans.conf.eex`, replacing it with this:

{% highlight elixir linenos %}
<%= if Map.has_key?(assigns, :vlans) do %>
  <%= Render.vlans(@vlans) %>
<% end %>
{% endhighlight %}

There's a couple of things going on here. First, we're going to check the map of
data to see if it has a `vlans` key. We do this, so that if we have a device
that doesn't have any vlans it'll just skip this, rather than break the program.
If we do have a `vlans` key, we're going to pass it to a new function called
`Render.vlans`.

Let's have a look at this in action:

{% highlight elixir linenos %}
iex> defmodule Render do
...>   require EEx
...> 
...>   EEx.function_from_file(:def, :base,
...>                          Path.expand("./templates/base_example.conf.eex"),
...>                          [:assigns])
...> 
...>   EEx.function_from_file(:def, :vlans,
...>                          Path.expand("./templates/vlans.conf.eex"),
...>                          [:vlans])
...> end
iex:15: warning: redefining module Render
{:module, Render,
 <<70, 79, 82, 49, 0, 0, 17, 236, 66, 69, 65, 77, 69, 120, 68, 99, 0, 0, 0, 202, 131, 104, 2, 100, 0, 14, 101, 108, 105, 120, 105, 114, 95, 100, 111, 99, 115, 95, 118, 49, 108, 0, 0, 0, 4, 104, 2, ...>>,
 {:vlans, 1}}
{% endhighlight %}

We redefine our `Render` module, and call our `Render.base` function again:

{% highlight elixir linenos %}
iex(16)> Render.base(device)                                                       
"hostname ABC20\nno logging console\nusername admin secret password\naaa new-model\naaa authentication login default local\nclock timezone GMT 0 0\nclock summer-time BST recurring last Sun Mar 1:00 last Sun Oct 1:00\nip name-server 8.8.8.8\nno ip http server\nno ip http secure-server\nntp server uk.pool.ntp.org\nntp update-calendar\n!\n\n  \nvlan 999\n  name NATIVE\n\nvlan 100\n  name VOICE\n\nvlan 101\n  name DATA 1\n\nvlan 102\n  name DATA 2\n\nvlan 103\n  name DATA 3\n\n\n\n!\ninterface 0\nip address 192.168.1.20\nno shutdown\n"
{% endhighlight %}

And, once again, we easily render our template. So, this enables us to build out
a hierarchy of templates to cover all possibilities of data input we may have.
Which is pretty cool if you ask me. It's certainly going to make my job easier.

[j]: http://jinja.pocoo.org/
[ma]: https://www.youtube.com/watch?v=y9lNbNGbo24
[csv]: https://github.com/CargoSense/ex_csv

