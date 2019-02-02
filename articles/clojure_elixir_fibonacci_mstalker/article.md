# Clojure vs. Elixir, Part 2: Fibonacci Code Challenge

## A First Pass

We reviewed some [similarities and differences between Clojure and Elixir](https://blog.rentpathcode.com/elixir-vs-clojure-a-high-level-comparison-e5b79537d213) in the last blog post. Let's continue our comparison by diving into some code.

The Fibonacci sequence looks like this:

```
0, 1, 1, 2, 3, 5, 8, 13, 21, ...
```

The first two elements are `0` and `1`. After that, each element is the sum of the previous two elements.

Here's today's programming challenge:

> For any non-negative integer `n`, write a function to retrieve the `n`th Fibonacci number. Use a zero-based index.

For example, when `n` is `4`, our function should return `3`.

My goal in the following code exploration is to highlight some language features of Clojure and Elixir. I'll give examples of how someone might solve our programming challenge in either language, and then I'll explain some syntax or conventions. I'm not attempting to produce the most efficient or the cleverest code, as that may actually get in the way of giving you a taste for each language!


### Clojure

A straightforward solution to our problem looks like this in Clojure:

```clojure
(ns fibonacci)

(defn compute
  [n]
  (cond
    (= n 0) 0
    (= n 1) 1
    :else (+ (compute (- n 1))
             (compute (- n 2)))))
```

We created a `fibonacci` namespace on the first line, and then we used `defn` to define a `compute` function. The next line, `[n]`, indicates that the function has one argument, which we call `n`.

You'll notice all the parentheses. A pair of parentheses creates a list. The first element of a list is generally a function or macro, and the rest of the elements are the arguments.

[`cond`](http://clojuredocs.org/clojure.core/cond) begins a conditional. If `n` is equal to zero, the function returns `0`. If `n` is `1`, then `compute` returns `1`. Mathematical operators like `=`, `+`, and `-` are just functions in Clojure, and they follow the same syntactical rules as functions. That's why these operators appear before their arguments, rather than between them.

You might have observed that no commas separate function arguments. That's normal in Clojure. Commas are optional in collections.

Finally, we use the `:else` keyword to do something if `n` isn't `0` or `1`. We recursively call the `compute` function twice, passing in different arguments. This sums the two previous Fibonacci values. Putting the two `compute` function calls on separate lines is aesthetic. I could have written this:

```clojure
:else (+ (compute (- n 1)) (compute (- n 2)))
```

Clojure has no explicit `return`, so functions just return the last value they evaluate.

We can call our function like this:

```clojure
(fibonacci/compute 10)
;; This is a Clojure comment. 55 is the function's return value.
```

### Elixir

How does Elixir handle the simple implementation? Here is how we could rewrite the Clojure code in Elixir:

```elixir
defmodule Fibonacci do
  def compute(n) do
    cond do
      n == 0 -> 0
      n == 1 -> 1
      true -> compute(n - 1) + compute(n - 2)
    end
  end
end
```

First, I defined a module with `defmodule`. Modules are like Clojure's namespaces, and they contain zero or more functions. Then, I used `def` to define a function named `compute/1`. This function takes argument, `n`.

Did you notice that I called this function `compute/1` instead of `compute`? We use both the function name and the number of arguments when referring to functions in Elixir.

I added the module's contents inside a `do`/`end` block. Many syntactic structures in Elixir use `do` and `end` to mark a beginning and end. Modules, functions, and conditionals are just a few such structures.

Like in Clojure, `cond` sets up a multi-branch conditional. The "if" part of each conditional is to the left of `->`. We can write the "then" part either to the right of the `-> ` or below it.

Also like Clojure, Elixir has no explicit `return`. If `n` is `0`, the function returns `0`. If `n` is `1`, `compute/1` returns `1`. Otherwise, it recursively calls itself twice to figure out the previous two Fibonacci values. Unlike Clojure, we use `true` to introduce our "else" clause. That's just conventional, though; any value but `nil` or `false` [would have worked](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#cond/1).

We'll use our function like this:

```elixir
Fibonacci.compute(10)
# This is an Elixir comment. 55 is the function's return value.
```

The parentheses are optional, but I'd recommend using them for clarity.

That solution works, but it's arguably not very idiomatic Elixir. Let's try again.

```elixir
defmodule Fibonacci do
  def compute(0), do: 0
  def compute(1), do: 1
  def compute(n), do: compute(n - 1) + compute(n - 2)
end
```

This time, I wrote three different function clauses. This takes advantage of Elixir's pattern-matching. When you call the `compute/1` function, Elixir will look at each function clause from top to bottom, testing each line to see whether a given function clause will match the argument. If `n` is `0`, Elixir sees that the first function clause is meant to work with an argument of `0`, and it executes the function clause. This function returns `0`.

If `n` is `1`, Elixir tries to match this argument with the first function clause, and fails. It then tries to match `n` with the `1` in the signature of the second function clause. There's a match, so `compute/1` returns `1`.

If `n` is greater than `1`, Elixir executes the last function clause, since the first two function clauses only match `0` or `1`. The last function clause matches any argument value.

## Optimizing Our Functions

The functions above are fine for small values of `n`. If `n` is sufficiently large, though, these functions will slow down significantly. The Clojure version even raises a `StackOverflow Error`. Try calling these functions with an argument of `200000` and you'll see what I mean!

So far, our functions figured out a value by starting at a higher sequence index, and then calculating the values at lower indexes. We can speed up our function execution by starting an index of `0`, and then working our way up. Writing functions this way tends to be more complicated because they require one or more state variables that we didn't need in our simpler implementations above.


### Clojure

Let's take a look at a Clojure implementation.

```clojure
(ns fibonacci)

(defn- helper
  [n]
  (loop [current-index 2
         current-value 1
         previous-value 1]
    (if (= n current-index)
      current-value
      (recur (inc current-index)
             (+' current-value previous-value)
             current-value))))

(defn compute
  [n]
  (cond (= n 0) 0
        (= n 1) 1
        :else (helper n)))
```

The `compute` function looks very similar to our original implementation. We return `0` or `1` if `n` is `0` or `1`, respectively. Otherwise, we execute another function to calculate the value we want. In this case we use a function called `helper`.

`defn-` is a private function. Due to how Clojure compiles code, we have to define `helper` above any code that uses it.

Clojure isn't [tail-call-optimized](https://stackoverflow.com/a/310980), but it has a [`loop`](http://clojuredocs.org/clojure.core/loop)/[`recur`](http://clojuredocs.org/clojure.core/recur) construct that works around this. In `helper`, we set up a starting point for our recursion with `loop`, and we initialize some values. Our assumption is that if you're in this function, the index you're looking for is at least `2`, so we give `current-index` an initial value of `2`. `current-value` is `1` when the index is `2`. The previous Fibonacci value is `1`, and we call this `previous-value` in our loop.

As we construct our sequence, as long as our index hasn't reached `n`, we do a few things:
1. Increment our index
1. Calculate the _new_ "current value" at our incremented index by adding the _old_ to be the "current value" and "previous value."
1. Let the _old_ "current value" become the _new_ "previous value".

These three actions happen when we call `recur`. The function then jumps back up to `loop`, using our three new values for `current-index`, `current-value`, and `previous-value`. When our index reaches `n`, we return the current value.

You may notice that we add numbers together with `+'` instead of `+`. This casts numbers to a `clojure.lang.BigInt` data type, when necessary, so our function doesn't throw an exception when dealing with large numbers. (See [here](http://clojuredocs.org/clojure.core/+'#example-54cf5ee7e4b081e022073c3e) for a short demonstration.) Also, as a matter of style, we use hyphens in names.


### Elixir

How does a similar approach look in Elixir?

```elixir
defmodule Fibonacci do
  def compute(0), do: 0
  def compute(1), do: 1
  def compute(n), do: helper(n, 2, 1, 1)

  defp helper(n, current_index, current_value, _) when n == current_index do
    current_value
  end

  defp helper(n, current_index, current_value, previous_value) do
    helper(n, current_index + 1, current_value + previous_value, current_value)
  end
end
```

Like Clojure, we handle the `n == 0` and `n == 1` cases first. When `n >= 2`, we call a `helper/4` function.

`defp` defines a private function. Elixir doesn't require us to define our `helper/4` function higher up in the module than the code that calls it.

`when n == current_index` is a [guard clause](https://hexdocs.pm/elixir/guards.html#content). A guard clause adds a condition to the function clauses. If `n` is equal to `current_index`, Elixir executes the first function clause and returns `current_value`. Using an underscore for an argument is a way for us to indicate that we don't need it. We can't use an underscore for `n` or `current_index`, however. Although we don't use them in the function body, we use them in the guard clause.

If `n` and `current_value` don't meet the condition in the guard clause, Elixir executes the second `helper/4` function. This function clause does the same data transformations as the Clojure version above.

By convention, whereas Clojure uses kebab case, Elixir uses underscores in names.

I used two different syntaxes to define functions above. The following two function notations are equivalent:

Multi-line syntax:
```elixir
def my_function(argument) do
  something_interesting()
end
```

Single-line syntax:
```elixir
def my_function(argument), do: something_interesting()
```

For both Clojure and Elixir, we have looked at a simple recursive solution, and an iterative solution that won't require a lot of system resources. Does either language provide a more creative or concise way of solving our Fibonacci problem? I'm so glad you asked :)


## Elegant Solutions

### Clojure

A user of [ClojureDocs](http://clojuredocs.org/) named Bobby submitted [a way to generate the Fibonacci sequence](http://clojuredocs.org/clojure.core/iterate#example-542692cac026201cdc326b2b) with Clojure's [`iterate`](http://clojuredocs.org/clojure.core/iterate) function. I'll incorporate his work into a solution to our problem:

```clojure
(ns fibonacci)

(defn compute
  [n]
  (let [fib (map first (iterate (fn [[a b]] [b (+' a b)]) [0 1]))]
    (nth fib n)))
```

Let's interpret this from the inside out. `(fn [[a b]] [b (+' a b)])` is an anonymous function. It takes a two-element [vector](https://clojure.org/guides/learn/sequential_colls#_vectors) as its argument, and binds the first and second elements to `a` and `b`, respectively. It returns a two-element vector. The first element is `b`, and the second element is the sum of `a` and `b`.

[`iterate`](http://clojuredocs.org/clojure.core/iterate) generates a [lazy sequence](http://theatticlight.net/posts/Lazy-Sequences-in-Clojure/). Ignoring the actual implementation details of `iterate` and lazy sequences for the moment, we can think of the first element as `[0 1]`. `iterate` passes `[0, 1]` to the anonymous function to generate the second element, `[1 1]`. `iterate` generates the third sequence element by passing the second element, `[1 1]`, to the anonymous function, and so on.

We then [`map`](http://clojuredocs.org/clojure.core/map) over this infinite sequence, taking the first element of each vector in the lazy sequence. Our lazy sequence began like this:

```clojure
([0 1] [1 1] [1 2] [2 3])
```

Mapping over this with the [`first`](http://clojuredocs.org/clojure.core/first) function yields a new lazy sequence that begins like this:

```clojure
(0 1 1 2)
```

[`let`](http://clojuredocs.org/clojure.core/let) creates a local binding, and assigns the result of `map ` to `fib`. We return the `n`th element of the sequence on the next line. Clojure will compute the `0`th to `n`th values of the sequence in order to do that.

There are many more interesting and concise implementations of the Fibonacci sequence in Clojure. You can find some [here](https://en.wikibooks.org/wiki/Clojure_Programming/Examples/Lazy_Fibonacci). Konrad Garus wrote a [blog post explaining the version that uses corecursion](http://squirrel.pl/blog/2010/07/26/corecursion-in-clojure/).


### Elixir

Elixir has its own iteration function, [`Stream.iterate/2`](https://hexdocs.pm/elixir/Stream.html#iterate/2). `Stream` is the module that defines the function, and we include that with the function name for clarity, since different modules sometimes have functions with the same name. We can port the Clojure code above to Elixir like this:

```elixir
defmodule Fibonacci do
  def compute(n) do
    {0, 1}
    |> Stream.iterate(fn {a, b} -> {b, a + b} end)
    |> Stream.map(&elem(&1, 0))
    |> Enum.at(n)
  end
end
```

We start off with a two-element [tuple](https://elixir-lang.org/getting-started/basic-types.html#tuples). The [pipe operator](https://hexdocs.pm/elixir/Kernel.html#%7C%3E/2), `|>`, takes a value and passes it as the first argument of the following function. In this case, the first pipe causes Elixir to evaluate

```elixir
Stream.iterate({0, 1}, fn {a, b} -> {b, a + b} end)
```

`fn {a, b} -> {b, a + b} end` is an anonymous function that returns a new tuple. `Stream.iterate/2` uses this to produce a stream of tuples. This is similar to what we did with Clojure's `iterate` function.

Our resulting stream begins like this:

```elixir
{0, 1}, {1, 1}, {1, 2}, {2, 3}, ...
```

Then, we [map](https://hexdocs.pm/elixir/1.6.6/Enum.html#map/2) over the stream with the `Kernel.elem/2` function. `&` is a [shorthand syntax](https://hexdocs.pm/elixir/Kernel.SpecialForms.html#&/1) for an anonymous function, and `&1` refers the first argument. In our case, it refers to a tuple in our stream above. This creates a new stream with the first element of each tuple in the original stream:

```elixir
0, 1, 1, 2, ...
```

Finally, [`Enum.at/3`](https://hexdocs.pm/elixir/Enum.html#at/3) converts the stream to a list, computing elements up through the `n`th element. The arity of `at` is `3`, rather than `2`, due to an optional third argument. `Enum.at/3` then returns the list's `n`th element.

We could have written the same function without pipes:

```elixir
defmodule Fibonacci do
  def compute(n) do
    tuples = Stream.iterate({0, 1}, fn {a, b} -> {b, a + b} end)
    fib = Stream.map(tuples, fn tuple -> elem(tuple, 0) end)

    Enum.at(fib, n)
  end
end
```

However, I find the version with pipes easier to understand.

Tim Morgan used [`Stream.unfold/2`](https://hexdocs.pm/elixir/Stream.html#unfold/2) to generate a [stream of Fibonacci numbers](https://github.com/seven1m/30-days-of-elixir/blob/master/24-stream.exs#L24). I'll use his approach to simplify our `compute/1` function:

```elixir
defmodule Fibonacci do
  def compute(n) do
    {0, 1}
    |> Stream.unfold(fn {a, b} -> {a, {b, a + b}} end)
    |> Enum.at(n)
  end
end
```

You can see some another creative solution to the Fibonacci sequence using multiple processes in Dave Thomas's _Programming Elixir 1.3_.


## Summary

We've solved our coding challenge with both Clojure and Elixir. The basic approach was the same for many of our solutions, even though we used some features specific to either language. Now, it's your turn. How would you have tackled the coding challenge in Clojure or Elixir?


## Acknowledgements
The following people reviewed this article: Kevin Marth, Sean Callan, Michael Pelz-Sherman, Trevor Brown, and Moncef Maiza. Eric Caspary reviewed some of the Clojure code.

I got the idea for the iterative approach to the Fibonacci sequence from [_Structure and Interpretation of Computer Programs_](https://mitpress.mit.edu/sites/default/files/sicp/full-text/book/book-Z-H-11.html#%_sec_1.2.2) by Harold Abelson and Gerald Jay Sussman, with Julie Sussman. The book is phenomenal. Stop reading this post and go read that book. The simple Clojure version of the `compute` function in this post looks very similar to how they approached the recursive version in Scheme, as well.

The first two Elixir implementations appeared in Dave Thomas's [2014 ElixirConf keynote](https://youtu.be/5hDVftaPQwY?t=473), although he and I named things differently. Dave also discusses using `Stream.unfold/2` to generate a Fibonacci sequence in _Programming Elixir 1.3_. I'm not sure whether he or Tim Morgan came up with that approach first.
