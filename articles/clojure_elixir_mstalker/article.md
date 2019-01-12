# Elixir vs. Clojure: A High-Level Comparison

I've spent a bit of time thinking through what language to invest more time in: Elixir or Clojure. They're very similar in many ways. Both support safe concurrency. Both are functional and favor immutability. Clojure and Elixir are both somewhat niche languages. If you're deciding to learn or use one over the other, here are some considerations. Other people have weighed in on this choice [here](https://elixirforum.com/t/opinions-on-elixir-vs-clojure/208), [here](https://www.reddit.com/r/Clojure/comments/5wxgko/elixir_or_clojure/), and [here](https://smashingboxes.com/blog/choosing-your-future-tech-stack-clojure-vs-elixir-vs-go/), but I wanted to offer my own observations.


## Language Design

### Clojure

Rich Hickey is Clojure's creator. He is brilliant, and he has designed Clojure very well. (As an aside, watch his videos. [Here's a list](https://github.com/tallesl/Rich-Hickey-fanclub#talks). I'd recommend "Simple Made Easy," in particular.)

Clojure is a Lisp, so you'll see `(lots (of (parentheses)))`. It also uses prefix notation instead of infix notation. To add numbers, you'd write

```clojure
(+ 1 2)
```

rather than

```elixir
1 + 2
```

This takes a bit of getting used to. However, once you do, it affords you some nice advantages. The structure of your code represents the abstract syntax tree (AST) very closely. Moreover, some editing tools like [ParEdit](https://www.emacswiki.org/emacs/ParEdit) or [Smartparens](https://github.com/Fuco1/smartparens) let you manipulate your source code very quickly. Clojure also lets you pass a variable number of arguments functions like `+`:

```clojure
(+ 1 2 3 4 5)
```

This will return `15`. This is handy when you don't know how many arguments a function will need to handle.

A few more observations:

* Clojure's core library is larger than Elixir's.
* Clojure offers function overloading, so you can define a function that does different things if you pass in different numbers of arguments. The language also allows you to pass in a [variable number of arguments](https://clojure.org/guides/learn/functions#_variadic_functions), binding some of them to a "rest argument."
* Rich Hickey mentioned that he [doesn't like pattern-matching](https://youtu.be/2V1FtfBDsLU?t=2722). Instead, Clojure uses [protocols](https://clojure.org/reference/protocols) and [multimethods](https://clojure.org/reference/multimethods) to perform dynamic function dispatch.


### Elixir

José Valim made Elixir. He was on the [Ruby on Rails Core Team](https://rubyonrails.org/community/), and he won a [Ruby Hero Award in 2010](https://rubyheroes.com/heroes/2010). José gave Elixir a syntax that looks similar to Ruby, but it works more like Erlang.

Some Elixir functions, like those in the `Enum` module, operate on many different data types. Types tend to have their own modules and type-specific functions, though. For instance, if you want to work with integers, you might use functions from the `Integer` module:

```elixir
Integer.mod(3, 2)
```

In Clojure, you would just execute the following function without needing to specify its namespace.

```clojure
(mod 3 2)
```

I think Elixir's pattern-matching is a wonderful language feature. It allows you to remove many conditionals from your code. You can use Elixir's pattern-matching to destructure data, bind variables in function signatures or function bodies, and handle errors. Pattern matching also affords us a nice way to bind the head and tail of a list:

```elixir
[h|t] = [1, 2, 3]
# [1, 2, 3]

h
# 1

t
# [2, 3]
```

In the code above, `#` denotes a code comment that shows the result of evaluating the previous line. `[]` is a [list](https://elixir-lang.org/getting-started/basic-types.html#linked-lists), which is like an immutable array.

You can also write several versions of a function to handle different arguments. Let's say we wanted a utility function to join collections together. In our case, we'll concatenate lists and merge maps. We could write this:

```elixir
def add(a, b) when is_list(a) and is_list(b), do: a ++ b
def add(%{} = a, %{} = b), do: Map.merge(a, b)
```

That first line uses a guard clause, which tells Elixir to execute it only if both arguments are lists. The second line matches the arguments against a map pattern.

Like Clojure, Elixir exposes the AST. However, the connection between the shape of your code and the AST is not as obvious as it is in Clojure:

```elixir
1 + 2
```

has an AST that looks like this:

```elixir
{:+, [context: Elixir, import: Kernel], [1, 2]}
```

That's a [tuple](https://elixir-lang.org/getting-started/basic-types.html#tuples) with three elements: an [atom](https://elixir-lang.org/getting-started/basic-types.html#atoms) representing the function, some metadata, and the function's arguments. You can read more [here](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#quote/2-explanation).


## VM

Elixir [compiles down to BEAM byte code](https://elixir-lang.org/crash-course.html#adding-elixir-to-existing-erlang-programs). Clojure [runs on the Java Virtual Machine](https://clojure.org/about/rationale) (JVM). If you need one virtual machine or the other, your choice between Elixir and Clojure has already been made.


## Community

My impression is that most people using either language have made an intentional choice. Most people in either community seem excited about their language.

Both communities have Slack groups ([Elixir](https://elixir-slackin.herokuapp.com/), [Clojure](https://clojurians.herokuapp.com/)). Both have active forums ([Elixir](https://elixirforum.com/), [Clojure](https://groups.google.com/forum/#!forum/clojure)). Both have IRC channels ([Elixir](http://irc.lc/freenode/elixir), [Clojure](http://irc.lc/freenode/clojure)).


## Documentation

Both languages have some very good documentation and learning resources. Clojure has been around longer, and there are more [books written about it](https://clojure.org/community/books) than [books written about Elixir](https://elixir-lang.org/learning.html#books). Clojure and Elixir both have a [plethora](https://www.imdb.com/title/tt0092086/quotes?qt0400524) of ways to learn. Here are a few:

### Elixir

* Elixir's official documentation excels at clarity. You can read the official [guides](https://elixir-lang.org/getting-started/introduction.html) and [API documentation](https://hexdocs.pm/elixir/Kernel.html). I'll give Elixir bonus points for making their API documentation searchable and for including examples.
* [Elixir School](https://elixirschool.com) is a community effort led by [Sean Callan](https://github.com/doomspork). It has lessons on the Elixir language and some of its tooling. I'd recommend checking it out!

### Clojure

* The language has official [guides](https://clojure.org/guides/getting_started) and [API documentation](https://clojure.org/api/api).
* [_Clojure for the Brave and True_](https://www.braveclojure.com/clojure-for-the-brave-and-true/) by [Daniel Higginbotham](https://github.com/flyingmachine) is clever, fun, and full of rich content.
* [ClojureDocs](http://clojuredocs.org/) by [Zachary Kim](https://github.com/zk) provides unofficial documentation for Clojure's API. It's searchable, and it has user-submitted examples to show how to use various functions. As a bonus, typing `ctrl+s` makes the cursor jump to the search input.
* [4Clojure](http://www.4clojure.com/) helps you learn Clojure by solving various programming challenges.


## Concurrency
Elixir's concurrency model involves various [processes communicating by sending messages to one another](https://elixir-lang.org/getting-started/processes.html).

Clojure's concurrency works in the context of Java's thread system, and the language offers a few different ways to share state between threads. Read more [here](https://clojure.org/about/concurrent_programming).


## Tooling

### Elixir

Elixir [supports](https://elixir-lang.org/getting-started/mix-otp/introduction-to-mix.html) Erlang's Open Telecom Platform ([OTP](https://github.com/erlang/otp)). This gives you the ability to create supervision trees easily. Supervisor processes monitor child processes, and can restart the children if they crash. OTP also has other goodies in it. I'd encourage you to check it out.

[Phoenix](https://phoenixframework.org/) is Elixir's alternative to [Ruby on Rails](https://rubyonrails.org/).


### Clojure

For creating web apps, Clojure generally favors small libraries instead of big web frameworks.

[ClojureScript](https://clojurescript.org/) is a joy to use. It's a variation of Clojure that compiles down to JavaScript. [ElixirScript](https://github.com/elixirscript/elixirscript) seems similar, but I haven't used it.

A real strength of Clojure is its use of the REPL (Read-Eval-Print Loop). Even though Elixir offers [IEx](https://elixir-lang.org/getting-started/introduction.html#interactive-mode), another REPL, many Clojure developers tend to lean on the REPL as a way to write code. The Clojure website offers [lots of documentation](https://clojure.org/guides/repl/introduction) on how to do this.


## Interop

### Elixir

It's pretty easy to use Erlang functions from Elixir. Want to get the value of pi?

```elixir
:math.pi()
```

That calls the `pi/0` function in Erlang's `math` module.

### Clojure

Clojure interop feels a little clunkier, maybe due to the difference in Erlang's and Java's language paradigms. The [syntax varies a bit](https://clojure.org/reference/java_interop), depending on whether you want to access an instance method, an instance field, a static method, or a static field.


## Summary

I have a very slight preference for Clojure. The syntax seems simpler to me, and I like the fact that it's a Lisp. However, both languages are solid and fun to use. We use both languages here at RentPath.

Whichever you go with, I'd urge you to learn functional programming concepts. It can be tempting to write in a functional language using non-functional idioms. This might not always be bad. However, if done unintentionally, it can lead to messy code.

Would you like to see some code? In our next article, we'll look at how you might implement the Fibonacci sequence in both Elixir and Clojure.

Happy coding!


## Acknowledgements
Many thanks to Rich Hickey and José Valim for creating such elegant languages. Sam Brauer, Sean Callan, Dan McGuire, and Kevin Marth all reviewed this article.
