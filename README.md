# Killer Elixir-Tips
[![All Contributors](https://img.shields.io/badge/all_contributors-2-orange.svg?style=flat-square)](#contributors)

Elixir Tips and Tricks from the Experience of Development. Each part consists of 10 Unique Tips and Tricks with a clear explanation with live examples and outputs. These tips will speed up your development and save you time in typing code as well.

You can read specific parts with following links...

[Part 1](./#part-1) [Part 2](./#part-2) [Part 3](./#part-3) [Part 4](./#part-4) [Part 5](./#part-5) [Part 6](./#part-6) [Part 7](./#part-7) [Part 8](./#part-8)

## Part 1

### 1. Multiple \[ OR \]

This is just the other way of writing Multiple **OR** conditions. This is not the recommended approach because in the regular approach when the condition evaluates to **true** , it stops executing the remaining conditions which saves evaluation time unlike this approach which evaluates all conditions first in list. This is just bad but good for discoveries.

```elixir
# Regular Approach
find = fn(x) when x>10 or x<5 or x==7 -> x end 

# Our Hack
hell = fn(x) when true in [x>10,x<5,x==7] -> x end
```

### 2. i\( term\) Elixir Term Type and Meta Data

Prints information about the data type of any given term. Try that in `iex` and see the magic.

```elixir
iex> i(1..5)
```

### 3. iex Custom Configuration - iex Decoration

Copy the content into a file and save the file as `.iex.exs` in your `~` home directory and see the magic. You can also download the file [HERE](https://gist.github.com/blackode/5728517116d7a4d08f0a4faddd8c145a)

```elixir
# IEx.configure colors: [enabled: true]
# IEx.configure colors: [ eval_result: [ :cyan, :bright ] ]
IO.puts IO.ANSI.red_background() <> IO.ANSI.white() <> " ❄❄❄ Good Luck with Elixir ❄❄❄ " <> IO.ANSI.reset
Application.put_env(:elixir, :ansi_enabled, true)
IEx.configure(
 colors: [
   eval_result: [:green, :bright] ,
   eval_error: [[:red,:bright,"Bug Bug ..!!"]],
   eval_info: [:yellow, :bright ],
 ],
 default_prompt: [
   "\e[G",    # ANSI CHA, move cursor to column 1
    :white,
    "I",
    :red,
    "❤" ,       # plain string
    :green,
    "%prefix",:white,"|",
     :blue,
     "%counter",
     :white,
     "|",
    :red,
    "▶" ,         # plain string
    :white,
    "▶▶"  ,       # plain string
      # ❤ ❤-»" ,  # plain string
    :reset
  ] |> IO.ANSI.format |> IO.chardata_to_string

)
```

![img](https://cdn-images-1.medium.com/max/800/1*iy-IELdB8fjTo5H0sABlBQ.png)

### 4. Creating Custom Sigils and Documenting

Each `x` sigil calls its respective `sigil_x` definition

Defining Custom Sigils

```elixir
defmodule MySigils do
  #returns the downcasing string if option l is given then returns the list of downcase letters
  def sigil_l(string,[]), do: String.downcase(string)
  def sigil_l(string,[?l]), do: String.downcase(string) |> String.graphemes

  #returns the upcasing string if option l is given then returns the list of downcase letters
  def sigil_u(string,[]), do: String.upcase(string)
  def sigil_u(string,[?l]), do: String.upcase(string) |> String.graphemes
end
```

#### usage

Load the module into iex

```elixir
iex> import MySigils
iex> ~l/HELLO/
"hello"
iex> ~l/HELLO/l
["h", "e", "l", "l", "o"]
iex> ~u/hello/
"HELLO"
iex> ~u/hello/l
["H", "E", "L", "L", "O"]
```

### 5. Custom Error Definitions

#### Define Custom Error

```elixir
defmodule BugError do
   defexception message: "BUG BUG .." # message is the default
end
```

**Usage**

```elixir
$ iex bug_error.ex
iex> raise BugError
** (BugError) BUG BUG ..
iex> raise BugError, message: "I am Bug.." #here passing the message dynamic
** (BugError) I am Bug..
```

### 6. Get a Value from Nested Maps Easily

The `get_in` function can be used to retrieve a nested value in nested maps using a list of keys.

```elixir
nested_map = %{ name: %{ first_name: "blackode"} }     # Example of Nested Map
first_name = get_in(nested_map, [:name, :first_name])  # Retrieving the Key

# Returns nil for missing value 
nil = get_in(nested_map, [:name, :last_name])              # returns nil when key is not present
```

Read docs: [Kernel.get\_in/2](http://elixir-lang.org/docs/stable/elixir/Kernel.html#get_in/2)

### 7. With Statement Benefits

The special form `with` is used to chain a sequence of matches in order and finally return the result of `do:` if all the clauses match. However, if one of the clauses does not match, the result of the miss matched expression is immediately returned.

```elixir
iex> with 1 <- 1+0,
          2 <- 1+1,
          do: IO.puts "all matched"
"all matched"
```

```elixir
iex> with 1 <- 1+0,
          2 <- 3+1,
          do: IO.puts "all matched"
4
## since  2 <- 3+1 is not matched so the result of 3+1 is returned
```

### 8. Writing Protocols

#### Define a Protocol

A **Protocol** is a way to dispatch to a particular implementation of a function based on the type of the parameter. The macros `defprotocol` and `defimpl` are used to define Protocols and Protocol implementations respectively for different types in the following example.

```elixir
defprotocol Triple do    
  def triple(input)  
end  

defimpl Triple, for: Integer do    
  def triple(int) do     
    int * 3   
  end  
end   

defimpl Triple, for: List do
  def triple(list) do
    list ++ list ++ list   
  end  
end
```

#### Usage

Load the code into `iex` and execute

```elixir
iex> Triple.triple(3) 
9
Triple.triple([1, 2])
[1, 2, 1, 2, 1, 2]
```

### 9. Ternary Operator

There is no ternary operator like `true ? "yes" : "no"` . So, the following is suggested.

```elixir
"no" = if 1 == 0, do: "yes", else: "no"
```

### 10. Advantage of Kernel.\|\|

When using pipelines, sometimes we break the pipeline for `or` operation. For example:

```elixir
result = :input
|> do_something
|> do_another_thing
```

```text
# Bad
result = (result || :default_output)
|> do_something_else
```

Indeed, `||` is only a shortcut for `Kernel.||` . We can use `Kernel.||` in the pipeline instead to avoid breaking the pipeline.

The code above will be:

```elixir
result = :input
|> do_something
|> do_another_thing
|> Kernel.||(:default_output)  #<-- This line
|> do_something_else
```

This above tip is from [qhwa](https://medium.com/@qhwa_85848)

[Part 1](./#part-1) [Part 2](./#part-2) [Part 3](./#part-3) [Part 4](./#part-4) [Part 5](./#part-5) [Part 6](./#part-6) [Part 7](./#part-7)

## Part 2

### **1  Code Grouping**

Code grouping stands for something great. It shows you how your code is grouped when you write multiple lines of code in single line with out using braces. It will be more clear with the following example.

```elixir
one 1 |> two()
```

If you want to see how this line of code is grouped into, you can check in the following format..

```elixir
quote(do: one 1 |> two()) |> Macro.to_string |> IO.puts
one(1 |> two())
```

So, by using the `quote` and `Macro.to_string` you can see how our code is grouped.

This tip came out in discussion with the creator of **Ecto** [**MichalMuskala**](https://elixirforum.com/users/michalmuskala) in the Elixir forum.

### 2  Elixir Short Circuit Operators && — \|\|

These replaces the nested complicated conditions. These are my best friends in the situations dealing with more complex comparisons. Trust me you gonna love this.

The `||` operator always returns the first expression which is true. Elixir doesn’t care about the remaining expressions, and won’t evaluate them after a match has been found.

#### \|\|

```elixir
false || nil || :blackode || :elixir || :jose
```

Here if you observe the first expression is false next `nil` is also false in elixir next `:blackode` which evaluates to true and its value is returned immediately with out evaluating the `:elixir` and `:jose` . Similarly if all the statements evaluates to `false` the last expression is returned.

#### &&

```elixir
iex> true && :true && :elixir && 5
5
iex> nil && 100
nil
iex> salary = is_login && is_admin && is_staff && 100_000
```

This `&&` returns the second expression if the first expression is `true` or else it returns the first expression with out evaluating the second expression. In the above examples the last one is the situation where we encounter to use the `&&` operator.

### 3  Comparing two different data types

I have self experience with this. When I was a novice in elixir, I just compared `"5" > 4` unknowingly by an accident and to my surprise it returned with `true`.

In **Elixir** every term can compare with every other term. So one has to be careful in comparisons.

![img](https://cdn-images-1.medium.com/max/800/0*SOFSiJHylCKMOb-9.)

```elixir
iex> x = "I am x "
"I am x "
iex> x > 34
true
iex> x > [1,2,3]
true
iex>▶ [1,2,3] < 1234567890
false
```

**Order of Comparison**

> number &lt; atom &lt; reference &lt; fun &lt; port &lt; pid &lt; tuple &lt; map &lt; list &lt; bitstring \(binary\)

### 4  Arithmetic Operators as Lambda functions

When I see this first time, I said to my self “**Elixir is Crazy**”. This tip really saves time and it resembles your smartness. In Elixir every operator is a macro. So, we can use them as lambda functions.

```elixir
iex> Enum.reduce([1,2,3], 0, &+/2)
6
iex> Enum.reduce([1,2,3], 0, &*/2)
0
iex> Enum.reduce([1,2,3], 3, &*/2)
18
iex> Enum.reduce([1,2,3], 3, &-/2)
-1
iex> Enum.reduce([1,2,3], 3, &//2)
0.5
```

### 5  Binary pattern matching

This is my recent discovery. I always encounter a situation like converting `"$34.56"` which is a string and I suppose do arithmetic operations. I usually do something like this before binary pattern matching..

![img](https://cdn-images-1.medium.com/max/800/0*ipJJTjsFiaGmCBpc.)

```elixir
iex> value = "$34.56"
iex ...      |> String.split("$")
iex ...      |> tl
iex ...      |> List.first
iex ...      |> String.to_float
34.56
```

#### Tip Approach

This tip makes my day easy. I recently used this is in one of my projects.

```elixir
iex> "$" <> value = "$34.56"
"$34.56"
iex> String.to_float value  
34.56
```

### 6  Recompiling Project

At beginning stage, I used to press `^c` `^c` twice and restart shell as `iex -S mix` whenever I make changes to the project files. If you are doing this now, stop it right now. You can just recompile the project.

```elixir
$ iex -S mix
iex> recompile
```

**Warning:** The changes in the `config/config.ex` are not reflected. You have to restart the shell again.

### 7  Logger Module

Logger is one of my favorite modules. This come by default and starts along with your application. You have to just `require` this module. When I was new to Elixir, I always used to write the console outputs as `IO.puts "This is value of data"` for code debugging but, those lines get mixed up with other lines of information and It became hard to trace those lines.

This `Logger` module solved my problem. It has many features but, I use three definitions very often `warn` `info` and `error` Each definition prints the information with different **colors** which is more easy to find the statement at a glance.

The best side of this module is that it prints along with the **time**, that means it also prints the time while executing your statement. So, you can know the direction of flow of execution.

Before using the `Logger` module one has to do `require Logger` so all macros will be loaded inside your working module.

![img](https://cdn-images-1.medium.com/max/800/0*DQf-KHbpd6qcgEpz.)

```elixir
iex> require Logger
Logger
iex> Logger.info "This is the info"
15:04:33.102 [info]  This is the info
:ok
iex> Logger.warn "This is warning"
15:04:56.712 [warn]  This is warning
:ok
iex> Logger.error "This is error"
15:05:19.570 [error] This is error
:ok
```

This tip is from [Anwesh Reddy](https://medium.com/@kanishkablack)

### 8  Finding All Started Applications

We can check the all the applications which are started along with our application. Sometimes we have to check whether a particular application is started or not. So, it helps you in those situations. If you are a beginner, you don’t feel will be using this much. But I am pretty sure of this tip will become handy when you work with multiple applications.

```elixir
iex> Application.started_applications
[{:logger, 'logger', '1.4.0'}, {:iex, 'iex', '1.4.0'},
 {:elixir, 'elixir', '1.4.0'}, {:compiler, 'ERTS  CXC 138 10', '7.0.1'},
 {:stdlib, 'ERTS  CXC 138 10', '3.0.1'}, {:kernel, 'ERTS  CXC 138 10', '5.0.1'}]
```

### 9  Advantage of Map keys as :atoms and binary\(strings\)

Before I let you to use this tip, I just want to remind you that **:atoms** are not garbage collected. Atom keys are great! If you have a fixed number of them defined statically in your code, you are in no danger. What you should not do is convert user supplied input into atoms without sanitizing them first because it can lead to out of memory. **You should also be cautious if you create dynamic atoms in your code.**

But, you can use the `.` to retrieve the data from the keys as `map.key` unlike the usual notation like `map["key"]` . That really saves on typing. But, I don’t encourage this because, as programmers we should really care about memory.

![img](https://cdn-images-1.medium.com/max/800/0*DQf-KHbpd6qcgEpz.)

```elixir
iex> map = %{name: "blackode", blog: "medium"}
%{blog: "medium", name: "blackode"}
iex> map.name
"blackode"
iex> map.blog
"medium"
```

Be sure that when you try to retrieve a key with `.` form which is not present in the map, it will raise an **key error** instead of returning the `nil` unlike the `map["key"]` which returns `nil` if `key` is not present in `map`

```elixir
iex> map["age"]
nil
```

```text
iex> map.age
Bug Bug ..!!** (KeyError) key :age not found in: %{blog: "medium", name: "blackode"}
Bug Bug ..!!
```

### 10 Color Printing

Elixir `>=1.4.0` has **ANSI** color printing option to console. You can have great fun with colors. You can also provide **background colors**.

```elixir
iex> import IO.ANSI
iex> IO.puts red <> "red" <> green <> " green" <> yellow <> " yellow" <> reset <> " normal"
iex> IO.puts Enum.join [red, "red", green, " green", yellow, " yellow", reset, " normal"]
red green yellow normal
```

The red prints in red color, green in green color, yellow in yellow color and normal in white. Have fun with colors…

For more details on color printing check [**Printex**](https://github.com/blackode/printex) module which I created for fun in Elixir.

![img](https://cdn-images-1.medium.com/max/800/0*DQf-KHbpd6qcgEpz.)

![img](https://cdn-images-1.medium.com/max/1000/0*Qskz94BcqMSyPAuH.png)

[Part 1](./#part-1) [Part 2](./#part-2) [Part 3](./#part-3) [Part 4](./#part-4) [Part 5](./#part-5) [Part 6](./#part-6) [Part 7](./#part-7)

## Part 3

### 1. Functions as Guard Clauses

We cannot make use of the functions as guard clauses in elixir. It means, `when` cannot accept functions that returns Boolean values as conditions. Consider the following lines of code…

```elixir
defmodule Hello do
  def hello(name, age) when is_kid(age) do
    IO.puts "Hello Kid #{name}"
  end
  def hello(name, age) when is_adult(age) do
    IO.puts "Hello Mister #{name}"
  end
  def is_kid age do
    age < 12
  end
  def is_adult age do
    age > 18
  end
end
```

Here we defined a **module** `Hello` and a function `hello` that takes two parameters of `name` and `age`. So, based on age I am trying `IO.puts`accordingly. If you do so you will get an error saying….

```text
** (CompileError) hello.ex:2: cannot invoke local is_kid/1 inside guard
    hello.ex:2: (module)
```

This is because **when** cannot accept functions as guards. We need to convert them to `macros` Lets do that…

```elixir
defmodule MyGuards do

  defmacro is_kid age do
    quote do: unquote(age) < 12
  end

  defmacro is_adult age do
    quote do: unquote(age) > 18
  end

end
# order of module matters here.....
defmodule Hello do

  import MyGuards

  def hello(name, age) when is_kid(age) do
    IO.puts "Hello Kid #{name}"
  end

  def hello(name, age) when is_adult(age) do
    IO.puts "Hello Mister #{name}"

   def hello(name, age) do
    IO.puts "Hello Youth #{name}"
  end

end
```

In the above lines of code, we wrapped all our guards inside a module `MyGuards` and make sure the module is top of the module `Hello` so, the macros first gets compiled. Now compile and execute you will see the following output..

```elixir
iex> Hello.hello "blackode", 21
Hello Mister blackode
:ok
iex> Hello.hello "blackode", 11
Hello Kid blackode
:ok
```

Starting on Elixir v1.6, you can use [defguard/1](https://hexdocs.pm/elixir/Kernel.html#defguard/1).

The `defguard` is also a macro. You can also create private guards with `defguardp`. Hope, you got the point here.  
Consider the following example.

**NOTE**: The `defguard` and `defguardp` should reside inside the module like other macros. It raises a compile time error, if some thing that don't fit in the guard clause section `when`.

Suppose, you want to check the given number is either `three` or `five`, you can define the guard as following.

```elixir
defmodule Number.Guards do
  defguard is_three_or_five(number) when (number===3) or (number===5)
end
```

### Usage

```elixir
import Number.Guards
defmodule Hello do
  def check_favorite_number(num) when is_three_or_five(num) do
    IO.puts "The given #{num} is on of my favourite numbers"
  end
  def check_favorite_number(_num), do: IO.puts "Not my favorite number"
end
```

You can also use them inside your code logic as they results `boolean` value.

```elixir
iex> import Number.Guards
Number.Guards

iex> is_three_or_five(5)
true

iex> is_three_or_five(3)
true

iex> is_three_or_five(1)
false
```

Check the following execution screen shot.

![ScreenShot Defguard Execution](.gitbook/assets/defguard%20%281%29.png)

### 2. Finding the presence of Sub-String

Using `=~` operator we can find whether the **right** sub-string present in **left** string or not..

```elixir
iex> "blackode" =~ "kode" 
true  
iex> "blackode" =~ "medium" 
false  
iex> "blackode" =~ "" 
true
```

### 3. Finding whether Module is loaded or not

Sometimes, we have to make sure that certain module is loaded before making a call to the function. We are supposed to ensure the module is loaded.

```text
Code.ensure_loaded? <Module>
```

```elixir
iex> Code.ensure_loaded? :kernel
true
iex> Code.ensure_loaded :kernel
{:module, :kernel}
```

Similarly we are having `ensure_compile` to check whether the module is compiled or not…

### 4. Binary to Capital Atom

Elixir provides a special syntax which is usually used for module names. What is called a module name is an _**uppercase ASCII letter**_ followed by any number of _lowercase_ or _uppercase ASCII letters_, _numbers_, or _underscores_.

This identifier is equivalent to an atom prefixed by `Elixir.` So in the `defmodule Blackode` example `Blackode` is equivalent to `:"Elixir.Blackode"`

When we use `String.to_atom "Blackode"` it converts it into `:Blackode` But actually we need something like “**Blackode” to Blackode**. To do that we need to use `Module.concat`

```elixir
iex(2)> String.to_atom "Blackode"
:Blackode
iex(3)> Module.concat Elixir,"Blackode"
Blackode
```

In Command line applications whatever you pass they convert it into **binary**. So, again you suppose to do some casting operations …

### 5. Pattern match \[ vs \] destructure.

We all know that `=` does the pattern match for left and right side. We cannot do `[a, b, c] = [1, 2, 3, 4]` this raise a `MatchError`

```elixir
iex(11)> [a, b, c] = [1, 2, 3, 4]
** (MatchError) no match of right hand side value: [1, 2, 3, 4]
```

We can use `destructure/2` to do the job.

```elixir
iex(1)> destructure [a, b, c], [1, 2, 3, 4]
[1, 2, 3]
iex(2)> {a, b, c}
{1, 2, 3}
```

If the left side is having more entries than in right side, it assigns the `nil` value for remaining entries..

```elixir
iex> destructure([a, b, c], [1])
iex> {a, b, c} 
{1, nil, nil}
```

### 6. Data decoration \[ inspect with :label \] option

We can decorate our output with `inspect` and `label` option. The string of `label` is added at the beginning of the data we are inspecting.

```elixir
iex(1)> IO.inspect [1, 2, 3], label: "the list "
the list : [1, 2, 3]
[1, 2, 3]
```

If you closely observe this it again returns the inspected data. So, we can use them as intermediate results in `|>` pipe operations like following……

```elixir
[1, 2, 3] 
|> IO.inspect(label: "before change") 
|> Enum.map(&(&1 * 2)) 
|> IO.inspect(label: "after change") 
|> length
```

You will see the following `output`

```elixir
before change: [1, 2, 3]
after change: [2, 4, 6]
3
```

### 7. Anonymous functions to pipe

We can pass the anonymous functions in two ways. One is directly using `&`like following..

```elixir
[1, 2, 3, 4, 5]
|> length()
|> (&(&1*&1)).()
```

This is the most weirdest approach. How ever, we can use the reference of the anonymous function by giving its name.

```elixir
square = & &1 * &1
[1, 2, 3, 4, 5]
|> length()
|> square.()
```

The above style is much better than previous . You can also use `fn` to define anonymous functions.

### 8. Retrieve Character Integer Codepoints — ?

We can use `?` operator to retrieve character integer codepoints.

```elixir
iex> ?a
97
iex> ?#
35
```

The following two tips are mostly useful for beginners…

### 9. Subtraction over Lists

We can perform the subtraction over lists for removing the elements in list.

```elixir
iex> [1, 2, 3, 4.5] -- [1, 2]
[3, 4.5]
iex> [1, 2, 3, 4.5, 1] -- [1]  
[2, 3, 4.5, 1]
iex> [1, 2, 3, 4.5, 1] -- [1, 1]
[2, 3, 4.5]
iex> [1, 2, 3, 4.5] -- [6]
[1, 2, 3, 4.5]
```

We can also perform same operations on char lists too..

```elixir
iex(12)> 'blackode' -- 'ode'
'black'
iex(13)> 'blackode' -- 'z'    
'blackode'
```

If the element to subtract is not present in the list then it simply returns the list.

### 10. Using Previous results in IEx

When you are working with `iex` environment , you can see a number increment every time you evaluate an expression in the shell like `iex(2)>` `iex(3)>`

Those numbers helps us to reuse the result with `v/1` function which has been loaded by default..

```elixir
iex(1)> list = [1, 2, 3, 4, 5]
[1, 2, 3, 4, 5]
iex(2)> double_lsit = Enum.map(list, &(&1*2))
[2, 4, 6, 8, 10]
iex(3)> v 1
[1, 2, 3, 4, 5]
iex(4)> v(1) ++ v(2)
[1, 2, 3, 4, 5, 2, 4, 6, 8, 10]
```

[Part 1](./#part-1) [Part 2](./#part-2) [Part 3](./#part-3) [Part 4](./#part-4) [Part 5](./#part-5) [Part 6](./#part-6) [Part 7](./#part-7)

## Part 4

### 1. Running Multiple Mix Tasks

```elixir
mix do deps.get,compile
```

You can run multiple tasks by separating them with coma `,`

How ever you can also create aliases in your mix project in a file called `mix.exs` .

The project definition looks like the following way when you create one using a `mix` tool.

```elixir
def project do
    [app: :project_name,
     version: "0.1.0",
     elixir: "~> 1.4-rc",
     build_embedded: Mix.env == :prod,
     start_permanent: Mix.env == :prod,
     deps: deps()]
  end
```

You are also allowed to add some extra fields…

Here you have to add the `aliases` field.

```elixir
[
 aliases: aliases()
]
```

Don’t forget to add `,` at the end when you add this in the middle of `list` .

The `aliases()` should return the `key-value` list.

```elixir
defp aliases do
  [
    "ecto.setup": ["ecto.create", "ecto.migrate", "ecto.seed"]
  ]
end
```

So, whenever you run the `mix ecto.setup` the three tasks `ecto.create`, `ecto.migrate` and `ecto.seed` will run one after the other.

You can also add them directly as following unlike I did with private function.

```elixir
def project do
    [app: :project_name,
     version: "0.1.0",
     aliases: ["ecto.setup": ["ecto.create", "ecto.migrate", "ecto.seed"]]      
.....
  end
```

### 2. Accessing the Documentation

Elixir stores the documentation inside the `bytecode` in a memory. You access the documentation with the help of `Code.get_docs/2` function . This means, the documentation accessed when it is required, but not when it is loaded in the virtual machine like `iex`

Suppose you defined a module in memory like ones you defined in **IEx**, cannot have their documentation accessed as they do not have their bytecode written to disk.

Let us check this…

Create a module with name `test.ex` with the following code. You can copy and paste it.

```elixir
defmodule Test do
  @moduledoc """
  This is the test module docs
  """

  @doc """
  This is the documentation of hello function
  """
  def hello do
    IO.puts "hello"
  end
end
```

Now stay in the directory where your file exists and run the command

```elixir
$ iex test.ex
```

Now you can access the function definitions but not the documentation.

```elixir
iex> Test.hello
hello
:ok
```

That means the code is compiled but documentation is not stored in the memory. So, you cannot access the docs. Lets check that…

```elixir
iex> Code.get_docs Hello, :moduledoc
nil
```

You will see the output as `nil` when you are trying to access the **docs** of the module you have created so far. This is because, the `bytecode` is not available in disk. In simple way `beam` file is not present. Lets do that...

Press `Ctrl+C` twice so you will come out of the shell and this time you run the command as

```elixir
$ elixirc test.ex
```

After running the command, you will see a file with name `Elixir.Test.beam` . Now the `bytecode` for the module `Test` is available in memory. Now you can access the documentation as follows...

```elixir
$ iex
iex> Code.get_docs Test, :moduledoc
{3, "This is the test module docs\n"}
```

The output is tuple with two elements. The first element is the line number of the documentation it starts and second element is the actual documentation in the binary form.

You can read more about this function [here](https://hexdocs.pm/elixir/Code.html#get_docs/2)

### 3. Verbose Testing

When you go with `mix test` it will run all the tests defined and gives you the time of testing. However, you can see more verbose output like which test you are running with the `--trace` option like following…

```elixir
mix test --trace
```

It will list out the all tests with names you defined as `test "test_string"` here `test_string` is the name of the test.

### 4. Dynamic Function Name in Elixir Macro

```elixir
defmacro gen_function(fun_name) do
  quote do 
    def unquote(:"#{fun_name}")() do
      # your code...
    end
  end
end
```

To be simple the name of the function should be an **atom** instead of binary.

### **5. Run Shell Commands in Elixir**

```elixir
System.cmd(command, args, options \\ [])
```

Executes the given command with args.

* **command** is expected to be an executable available in PATH unless an absolute path is given.
* **args** must be a list of binaries which the executable will receive as its

  arguments as is. This means that:

#### Examples

```elixir
iex> System.cmd "echo", ["hello"]
    {"hello\n", 0}
```

```elixir
iex> System.cmd "echo", ["hello"], into: []
    {["hello\n"], 0}
```

Get help from `iex` with `h System.cmd`

Checkout the documentation about `System` for more information and also check [Erlang os Module](http://www.erlang.org/doc/man/os.html).

### 6. Printing List as List without ASCII-Encoding

You know that when the list contains all the numbers as **ASCII** values, it will list out those values instead of the original numbers. Lets check that…

```elixir
iex> IO.inspect [97, 98]
'ab'
'ab'
```

The code point of `a` is `97` and `b` is `98` hence it is listing out them as `char_list`. However you can tell the `IO.inspect` to list them as list itself with option `char_lists: :as_list` .

```elixir
iex> IO.inspect [97, 98], charlists: :as_lists
[97, 98]
'ab'
```

Open `iex` and type `h Inspect.Opts`, you will see that Elixir does this kind of thing with other values as well, specifically **structs** and **binaries**.

### 7. Accessing file name and line number etc…

```elixir
defmacro __ENV__()
```

This macro gives the current environment information. You can get the information like current `filename` `line` `function` and others…

```elixir
iex(4)> __ENV__.file
"iex"

iex(5)> __ENV__.line
5
```

### 8. Creating Manual Pids

You can create the pid manually in Elixir with `pid` function. This comes with two flavors.

#### def pid\(string\)

Creates the pid from the string.

```elixir
iex> pid("0.21.32")
#PID<0.21.32>
```

#### def pid\(a, b, c\)

Creates a **PID** with 3 non negative integers passed as arguments to the function.

```elixir
iex> pid(0, 21, 32)
#PID<0.21.32>
```

#### Why do you create the pids manually?

Suppose you are writing a library and you want to test one of your functions for the type pid, then you can create one and test over it.

You cannot create the pid like assigning `pid = #PID<0.21.32>` because `#` is considered as comment here.

```elixir
iex(6)> pid = #PID<0.21.32>
...(6)>
```

When you do like above, **iex** shell just wait for more input as `#PID<0.21.32>` is treated as comment.

Now you enter another data to complete the expression. The entered value is the value of the pid. Lets check that…

```elixir
iex(6)> pid = #PID<0.21.32>      # here expression is not complete
...(6)> 23    # here we are giving the value 23
23            # expression is complete
iex(7)> pid
23
```

### 9. Replacing the String with global option

The `String.replace` function will replace the given the pattern with replacing pattern. By default, it replaces all the occurrences of the pattern. Lets check that…

```elixir
iex(1)> str = "hello@hi.com, blackode@medium.com"    
"hello@hi.com, blackode@medium.com"

iex(2)> String.replace str,"@","#"
"hello#hi.com, blackode#medium.com
```

The `String.replace str, "@", "#"`is same as `String.replace str, "@", "#", global: true`

But, if you want to replace only the first occurrence of the pattern, you need to pass the option `global: false` . So, it replaces only the first occurrence of `@` . Lets check that…

```elixir
iex(3)> String.replace str, "@", "#", global: false
"hello#hi.com, blackode@medium.com"
```

Here only first `@` is replaced with `#`.

### 10.Memory Usage

You can check the memory usage \(in bytes\) with `:erlang.memory`

```elixir
iex(1)> :erlang.memory
[total: 16221568, processes: 4366128, processes_used: 4364992, system: 11855440,
 atom: 264529, atom_used: 250685, binary: 151192, code: 5845369, ets: 331768]
```

However, you can pass option like `:erlang.memory :atom` to get the memory usage of atoms.

```elixir
iex(2)> :erlang.memory :atom
264529
```

[Part 1](./#part-1) [Part 2](./#part-2) [Part 3](./#part-3) [Part 4](./#part-4) [Part 5](./#part-5) [Part 6](./#part-6) [Part 7](./#part-7)

## Part 5

### 1. Fetching the default Mix Compilers list

```elixir
iex> Mix.compilers
```

Returns the default compilers used by Mix. The output will look something similar to `[:yecc, :leex, :erlang, :elixir, :xref, :app]` .  
It can be used in your `mix.exs` to prepend or append new compilers to Mix:

```elixir
#mix.exs
def project do
 [compilers: Mix.compilers ++ [:gettext]
end
```

### 2. Picking out the elements in List

We all know that a proper list is a combination of `head` and `tail` like `[head | tail]` . We can use the same principle for picking out the elements in the list like the following way…

```elixir
iex> [first | [second | [third | [ fourth | _rest ]]]] = [1, 2, 3, 4, 5, 6, 7]
[1, 2, 3, 4, 5, 6, 7]
iex> first
1
iex> {second, third, fourth}
{2, 3, 4}
iex(5)>
```

We can also use simplified syntax for the same job:

```elixir
iex> [first, second, third, fourth | _rest] = [1, 2, 3, 4, 5, 6, 7]
[1, 2, 3, 4, 5, 6, 7]
iex> first
1
iex> {second, third, fourth}
{2, 3, 4}
```

### 3. get\_in /Access.all\(\)

We all know that the get\_in function is used to extract the key which is deeper inside the map by providing the list with keys like a following way…

```elixir
iex> user = %{"name" => {"first_name" => "blackode", "last_name" => "john" }}
%{"name" => %{"first_name" => "blackode", "last_name" => "john"}}
iex > get_in user, ["name", "first_name"]
"blackode"
```

But, if there is a list of maps `[maps]` where you have to extract `first_name` of the each map, generally we go for `enum` . We can also achieve this by using the `get_in` and `Access.all()`

```elixir
iex> users=[%{"user" => %{"first_name" => "john", "age" => 23}},
            %{"user" => %{"first_name" => "hari", "age" => 22}},
            %{"user" => %{"first_name" => "mahesh", "age" => 21}}]
# that is a list of maps 
iex> get_in users, [Access.all(), "user", "age"]
[23, 22, 21]
iex> get_in users, [Access.all(), "user", "first_name"]
["john", "hari", "mahesh"]
```

**Note:** If the key is not present in map, then it returns nil **Warning:** When you use the `get_in` along with `Access.all()` , as the first value in the list of keys like above, the users datatype should be list. If you pass the map, it returns the error.

```elixir
iex(17)> list = [%{name: "john"}, %{name: "mary"}, %{age: 34}]
[%{name: "john"}, %{name: "mary"}, %{age: 34}]
iex(18)> get_in(list, [Access.all(), :name])
["john", "mary", nil]
```

In the above lines of code returns the `nil` for key which is not in the `map`.

```elixir
iex(19)> get_in(%{name: "blackode"}, [Access.all(), :name])
** (RuntimeError) Access.all/0 expected a list, got: %{name: "blackode"}
    (elixir) lib/access.ex:567: Access.all/3
```

In the above lines of code returns the error for passing map .

However, you can change the position of the Access.all\(\) in the list. But the before key should return the list. Check the following lines of code.

**Deep Dive**

We can also use the Access.all\(\) with functions like update\_in, get\_and\_update\_in, etc..  
For instance, given a user with a list of books, here is how to deeply traverse the map and convert all book names to uppercase:

```elixir
iex> user = %{name: "john", books: [%{name: "my soul", type: "tragedy"}, %{name: "my heart", type: "romantic"}, %{name: "my enemy", type: "horror"}]}
iex> update_in user, [:books, Access.all(), :name], &String.upcase/1
%{books: [%{name: "MY SOUL", type: "tragedy"}, %{name: "MY HEART", type: "romantic"}, %{name: "MY ENEMY", type: "horror"}], name: "john"}
iex> get_in user, [:books, Access.all(), :name]
["my soul", "my heart", "my enemy"]
```

Here, user is not a list unlike in the previous examples where we passed the users as a list. But, we changed the position of `Access.all()` and inside the list of keys `[:books, Access.all(), :name]`, the value of the key `:books` should return the list, other wise it raises an error.

### 4. Data Comprehension along with filters

We achieve the data comprehension through `for x <- [1, 2, 3], do: x + 1` . But we can also add the comprehension along with filter.

**General Usage**

```elixir
iex> for x <- [1, 2, 3, 4], do: x + 1
[2, 3, 4, 5]
# that is how we use in general lets think out of the box
```

**With filters**

Here I am using two lists of numbers and cross product over the lists and filtering out the product which is a odd number.

```elixir
iex> for x <- [1, 2, 3, 4], y <- [5, 6, 7, 8], rem(x * y, 2) == 0, do: {x, y, x * y}
[{1, 5, 5}, {1, 7, 7}, {3, 5, 15}, {3, 7, 21}]
#here rem(x * y, 2) is acting as a filter.
```

## 5. Comprehension with binary strings.

Comprehension with binary is little different. You supposed to wrap inside `<<>>`

Lets check that…

```elixir
iex> b_string = <<"blackode">>
"blackode"
iex> for << x <- b_string >>, do: x + 1
'cmbdlpef'
#here it is printing out the letter after every letter in the "blackode"
```

Did you observe that `x <- b_string` is just changed something like `<< x <- b_string >>` to make the sense.

## 6. Advanced Comprehension IO.stream

Here we are taking the elixir comprehension to the next level.  
We read the input from the keyboard and convert that to upcase and after that it should wait for another entry.

```elixir
for x <- IO.stream(:stdio, :line), into: IO.stream(:stdio, :line), do: String.upcase(x)
```

Basically `IO.stream(:stdio, :line)` will the read a line input from the keyboard.

```elixir
iex> for x <- IO.stream(:stdio, :line), into: IO.stream(:stdio, :line), do: String.upcase(x)
hello
HELLO
hi
HI
who are you?
WHO ARE YOU?
blackode
BLACKODE
^c ^c # to break
```

## 7. Single Line Multiple module aliasing

We can also alias multiple modules in one line:

```elixir
alias Hello.{One,Two,Three}
#The above line is same as the following 
alias Hello.One
alias Hello.Two
alias Hello.Three
```

## 8. Importing Underscore Functions

By default the functions with \_ are not imported. However, you can do that by importing them with `:only` explicitly.

```elixir
import File.Stream, only: [__build__: 3]
```

## 9. Sub string in Elixir

There is no direct `sub_str` like function in elixir. However you can achieve that by `String.slice/2`

```elixir
iex> String.slice("blackode", 1..-1)
"lackode"
iex> String.slice("blackode", 0..-4)
"black"
```

## 10. String Concatenation

We can do the string concatenation in two ways.

```elixir
iex> str1 = "hello"
iex> str2 = "blackode"
```

I am taking above lines of code for example…

**String Interpolation**

```elixir
iex> mystring = "#{str1}#{str2}"
helloblackode
```

**Using &lt;&gt; operator**

```elixir
iex> mystring = str1 <> str2
helloblackode
```

This is the best style and recommended one.

If you are having the list of strings `["hello", "blackode"]` then use `Enum.join`

```elixir
iex> mystrings = ["hello", "blackode"]
["hello", "blackode"]
iex> Enum.join(mystrings)
"helloblackode"
```

[Part 1](./#part-1) [Part 2](./#part-2) [Part 3](./#part-3) [Part 4](./#part-4) [Part 5](./#part-5) [Part 6](./#part-6) [Part 7](./#part-7)

## Part 6

```text
Elixir version 1.5.1 & Erlang otp version 20
```

### 1 Extracting Project Information

```elixir
Mix.Project.config[:version] # returns the version
Mix.Project.config[:app] # returns app name
```

You have to be inside the `mix project` when you are trying. See this in action… [![asciicast](https://asciinema.org/a/g75QG1GmKR1Ret0PMThjxDd8u.png)](https://asciinema.org/a/g75QG1GmKR1Ret0PMThjxDd8u)

### 2 Inner Binary Representation of String

This is a common trick in `elixir` . You have to concatenate the null byte `<<0>>` to a string that you want to see its inner binary representation like in the following way…

```elixir
iex> “hello” <> <<0>>
<<104, 101, 108, 108, 111, 0>>
```

### 3 Initialisation of Multiple with Same value

```elixir
iex> x = y = z = 5
5
iex> x
5
iex> y
5
iex> z
5
```

See this in action here...

[![asciicast](https://asciinema.org/a/135066.png)](https://asciinema.org/a/135066)

### 4 Not Null implementation in Structs

This is much like adding a not null constraint to the structs. When you try to define the struct with the absence of that key in the struct, it should raise an exception. Lets do that…  
You have to use `@enforce_keys [<keys>]` while defining the struct…

```elixir
# Defining struct
defmodule Employee do
   @enforce_keys [:salary]
   defstruct name: nil, salary: nil
end
# Execution 
iex> employee = %Employee{name: "blackode"} # Error 
iex> employee = %Employee{name: "blackode",salary: 12345}
%Employee{name: "john", salary: 12345}
```

See this in action... [![asciicast](https://asciinema.org/a/dYmXUDeUoQ2gneSwM7tiIhbNE.png)](https://asciinema.org/a/dYmXUDeUoQ2gneSwM7tiIhbNE)

**Warning** Keep in mind `@enforce_keys` is a simple compile-time guarantee to aid developers when building structs. It is not enforced on updates and it does not provide any sort of value-validation. The above warning is from the [ORIGINAL DOCUMENTATION](https://hexdocs.pm/elixir/Kernel.html#defstruct/1-enforcing-keys)

### 5 Check Whether Function is Exported or not

Elixir provides `function_exported?/3` to achieve this…

```elixir
# Defining the module with one exported function and private one
defmodule Hello do
  def hello name do
   IO.puts name
  end
  defp hellop name do
    IO.puts name 
  end
end
# Execution Copy and paste above lines of code in iex> 
iex> function_exported? Hello, :hello,1
true
iex> function_exported? Hello, :hellop, 1
false
```

See this in action... [![asciicast](https://asciinema.org/a/135080.png)](https://asciinema.org/a/135080)

### 6 Splitting the string with Pattern

We all know how to split a string with `String.split/2 function`. But you can also pass a **pattern** to match that over and over and splitting the string whenever it matches the **pattern**.

```elixir
"Hello Blackode! Medium-is-5*"
```

If you observe the above string, it comprises of **two blank spaces** , **one exclamation mark** `!` , **two minus — symbols** `-` and a **asterisk** `*` symbol. Now we are going to split that string with all of those.

```elixir
string = "Hello Blackode! Medium-is-5*"
String.split string, [" ", "!", "-", "*"]
#output
["Hello", "Blackode", "", "Medium", "is", "5", ""]
```

The pattern is generated at run time. You can still validate with `:binary.compiled`

### 7 Checking the closeness of strings

You can find the distance between the two strings using `String.jaro_distance/2`. This gives a float value in the range `0..1` Taking the `0` for no close and `1` is for exact closeness.

```elixir
iex> String.jaro_distance "ping", "pong"
0.8333333333333334
iex> String.jaro_distance "color", "colour"
0.9444444444444445
iex> String.jaro_distance "foo", "foo"
1.0
```

For the **FUN**, you can find your closeness with your name and your partner or lover in case if aren’t married. Hey… ! I am just kidding…. It is just an algorithm which is predefined where our love is undefined. Cheers …….. :\)

### 8 last and first for Strings

We know that `first` and `last` for `lists` gets you the element first and last respectively in the given list. Similarly, the strings give you the first and last `graphemes` in the given string.

```elixir
iex> string = "blackode medium"
"blackode medium"
iex> String.first string
"b"
iex> String.last string
"m"
```

See this in action… [![asciicast](https://asciinema.org/a/vc3j3LXurrwWdskBME5vTDsYF.png)](https://asciinema.org/a/vc3j3LXurrwWdskBME5vTDsYF)

### 9 Executing code Immediately after loading a Module

Elixir provides `@on_load` which accepts `atom` as function name in the same module or a `tuple` with function\_name and its arity like `{function_name, 0}`.

```elixir
#Hello module 
defmodule Hello do
@on_load :onload     # this executes after module gets loaded 
  def onload do
    IO.puts "#{__MODULE__} is loaded successfully"
  end
end
# Execution .... Just copy and paste the code in the iex terminal
# You will see the output something like this ....
Elixir.Hello is loaded successfully  
{:module, Hello,
 <<70, 79, 82, 49, 0, 0, 4, 72, 66, 69, 65, 77, 65, 116, 85, 56, 0, 0, 0, 130,
   0, 0, 0, 12, 12, 69, 108, 105, 120, 105, 114, 46, 72, 101, 108, 108, 111, 8,
   95, 95, 105, 110, 102, 111, 95, 95, 9, ...>>, {:onload, 0}}
```

You can see this in live here… [![asciicast](https://asciinema.org/a/GGmD9jUuRQQNherMh3fY5Zn4e.png)](https://asciinema.org/a/GGmD9jUuRQQNherMh3fY5Zn4e)

### 10 Chain of \[ or \] ’ s  in guards

This is about multiple guards in the same clause and writing `or` conditions with out using `or` We all know that `or` is used as a conjunction for two conditions resulting true if either one of them is true. Many of us writing the or conditions in the guard as following way…

```elixir
def print_me(thing) when is_integer(thing) or is_float(thing) or is_nil(thing), do: "I am a number"
```

You can also do this in bit more clear format as the following way…

```elixir
def print_me(thing)
  when is_integer(thing)
  when is_float(thing)
  when is_nil(thing) do
 "I am a number "
end
```

See this in action… [![asciicast](https://asciinema.org/a/S64h7ydwzfMXsPgOeSfcRXejA.png)](https://asciinema.org/a/S64h7ydwzfMXsPgOeSfcRXejA)

See also [Elixir Style Guide](https://github.com/christopheradams/elixir_style_guide)

## Part 7

[Part 1](./#part-1) [Part 2](./#part-2) [Part 3](./#part-3) [Part 4](./#part-4) [Part 5](./#part-5) [Part 6](./#part-6) [Part 7](./#part-7)

### 10\|&gt; Pretty Printing quoted Expression

#### Macro.to\_string \|&gt; IO.puts

In general, when you pass the quote expression to `Macro.to_string` , it returns the actual code in a string format and we all knew this.

The weird thing is, it gives the output in a single line along with **\n** characters inside the string.

Check it down

```elixir
    iex(2)> q = quote do
    ...(2)> 1+2
    ...(2)> name = "blackode"
    ...(2)> end

    {:__block__, [],
     [
       {:+, [context: Elixir, import: Kernel], [1, 2]},
       {:=, [], [{:name, [], Elixir}, "blackode"]}
     ]}

    iex(3)> Macro.to_string q
    "(\n  1 + 2\n  name = \"blackode\"\n)"
```

To print the new lines, you pipe the string output from `Macro.to_string` to `IO.puts` like in the following code snippet. It gives the clean output by printing in new line.

```elixir
    iex(4)> Macro.to_string(q) |> IO.puts
    (
      1 + 2
      name = "blackode"
    )
    :ok
```

![](https://cdn-images-1.medium.com/max/2000/1*dZ3sOdnXY2Ra7onjxW-0lA.png)

#### Check this in live

[![asciicast](https://asciinema.org/a/161913.png)](https://asciinema.org/a/161913)

### 9\|&gt; Finding the loaded files in iex and dynamic loading files on-fly

1. **Finding the loaded files**

#### Code.loaded\_files

In elixir, we are having a definition `loaded_files` in the module `Code` used to find the loaded files.

Let’s see this.

Run the command `iex` inside your terminal and call the function `Code.loaded_files` .

It gives an empty list `[]` as output because we haven’t loaded any files so far.

```elixir
    $ iex
    iex> Code.loaded_files
    []
```

#### File Loading

Let’s create a file `hello.ex` with a single function definition `hello` which just prints a string **“Welcome to the world”** for the demo purpose.

```elixir
    # hello.ex

    defmodule Hello do
      def hello do
        IO.puts "Welcome to the world"
      end
    end
```

Save the file after editing.

Now, make sure you are in the same directory of where the file exists and run the command

```elixir
    $ iex hello.ex
```

It opens the **Elixir** interactive shell by loading the `hello.ex` file as well.

Now, you can see the loaded file by calling `Code.loaded_files` . It outputs the path to the file.

```elixir
    iex> Code.loaded_files
    ["/home/john/hello.ex"]
```

That is one way of loading files. You can also load them on-fly in `iex session`

#### Dynamic loading on-fly

**Code.load\_file/1**

Unlike loading files with `iex hello.ex` , you can load them on-fly in `iex`session with the help of `Code.load_file "path/to/file"` .

Let us assume that we have a file `hello.ex` in our current directory and open `iex` session from the same directory.

```elixir
    $ iex

    iex> Code.load_file "hello.ex"
    [
      {Hello,
       <<70, 79, 82, 49, 0, 0, 4, 72, 66, 69, 65, 77, 65, 116, 85, 56, 0, 0, 0, 140,
         0, 0, 0, 15, 12, 69, 108, 105, 120, 105, 114, 46, 72, 101, 108, 108, 111,
         8, 95, 95, 105, 110, 102, 111, 95, 95, 9, ...>>}
    ]
```

#### Let's check this in action

[![asciicast](https://asciinema.org/a/161920.png)](https://asciinema.org/a/161920)

### 8\|&gt; Providing deprecation reason with

**@deprecated**

You might be noticed the warnings of using deprecated functions in a library along with some useful hint text. Today, we build this from scratch.

Suppose, you have written a library and want to update the name of one function in your next build release and if the user tried to access the old function then you have to warn him of using deprecated function instead of updated one.

To see this in action, you need to create new mix project.

Let’s do that.

```elixir
    mix new hello
```

Next change the directory to the project created.

```elixir
    cd hello
```

Now, edit the file `lib/hello.ex` in the project with the following code

```elixir
    #lib/hello.ex

    defmodule Hello do
      def hello do 
        Printee.print()
      end
    end

    defmodule Printee do

      @deprecated "print/0 is deprecated use show/0" 
      def print do
        IO.puts "hello blackode"
      end

      def show do
        IO.puts "hello blackode"
      end

    end
```

 File Scrennshot Vim Editor

![](https://cdn-images-1.medium.com/max/2000/1*n0RNxQCvWwy3ZQeIKeG_ZQ.png)

This file comprises of two modules `Hello` and `Printee` . The `Printee` module comprises of two functions `print/0` and `show/0` . Here purposely, `print/0` is considered as a deprecated function used inside the `Hello` module.

The mix compiler automatically looks for calls to deprecated modules and emit warnings during compilation.

So, when you compile the project `mix compile`, it gives a warning saying

> **“print/0 is deprecated use show/0”**

like in the following screenshot.

 Deprecated Function Warning

![image](https://cdn-images-1.medium.com/max/2000/1*iF5ROIflyq_UPuNJBtTrHA.png)

#### Check the live coding

[![asciicast](https://asciinema.org/a/161939.png)](https://asciinema.org/a/161939)

### 7\|&gt; Module Creation with create function

#### **Module.create/3**

As we all know `defmodule` is used to create a module. In similar, `Module.create/3` is also used to create a module.

The only difference in defining a module with `Module.create/3` is the definition inside the module is quoted expression.

However, we can use the help of `quote` to create a quoted expression.

We have to pass three parameters to `Moduel.create` .

The first one is **name** of the module. The second one is module definition in quoted expression. The third one is location. The location is given in `keyword list` like `[file: String.t, line: Integer ]` or else you can just take the help of `Macro.Env.location(__ENV__)` which returns the same.

![image](https://cdn-images-1.medium.com/max/2000/1*nXADw371CsJkTGlkWoXVYw.png)

location of loc

```elixir
    iex> module_definition = quote do: def hello, do: IO.puts "hello"
```

The above line of code gives the context of the module definition and collected in `module_definition` . We can use this `module_defintion` to pass as second parameter to `Module.create/3` function.

 module definition context

![image](https://cdn-images-1.medium.com/max/2000/1*w1vIuGh7xfyLkUq_-odq4w.png)

Let’s put all together and see the magic

```elixir
    iex(8)> Module.create Hello, module_definition, [file: "iex", line: 8 ]
```

![image](https://cdn-images-1.medium.com/max/2000/1*Or7UIFfZ0Vw8MjqiffM51w.png)

Creating another module `Foo` with same module function

```elixir
    iex(10)> Module.create Foo, module_definition, Macro.Env.location(__ENV__)
```

![image](https://cdn-images-1.medium.com/max/2000/1*UtnhjKAKmJ5pCKy-BhJBUA.png)

#### Check out the live execution

[![asciicast](https://asciinema.org/a/162255.png)](https://asciinema.org/a/162255)

### 6\|&gt; Finding the list of bound values in iex

#### binding

```elixir
    iex> binding
    []
    iex> name = "blackode"
    "blackode"
    iex> blog = "medium"
    "medium"
    iex> binding
    [blog: "medium", name: "blackode"]
```

![image](https://cdn-images-1.medium.com/max/2000/1*HWgNsJ7k0beakedw-Z_XeQ.png)

This is used in debugging purpose inside the function.

It is also very common to use `IO.inspect/2` with `binding()`, which returns all variable names and their values:

```elixir
def fun(a, b) do
  IO.inspect binding()
  ...
end
```

When `fun/2` is invoked with `:laughing`, `"time"` it prints:

```elixir
[a: :laughing, b: "time"]
```

You can also give context to the `binding`

If the given context is **nil \***\(by default it is\), \*the binding for the current  
 context is returned.

```elixir
    iex> var!(x, :foo) = 1
    1
    iex> binding(:foo)
    [x: 1]
```

#### Check out the live execution

[![asciicast](https://asciinema.org/a/162257.png)](https://asciinema.org/a/162257)

### 5\|&gt; Fun with Char\_Lists

We can convert any integer to charlist using `Integer.char_list` . It can be used in two ways either passing the base the base value or not passing.

Let’s try with base and with out base.

#### without base

```elixir
    iex> Integer.to_charlist(882681651)
    '882681651'
```

#### with base

```elixir
    iex> Integer.to_charlist(882681651, 36)
    'ELIXIR'
```

Try your own names as well and find your value with base

```elixir
    iex> List.to_integer 'BLACKODE', 36
    908344015970

    iex> Integer.to_charlist(908344015970, 36)
    'BLACKODE'
```

### 4\|&gt; Extracting Process Information

With the help of `Process.info` , we can extract the process information like linked processes etc…

It is used in two different ways.

**Note: \***We get the information if and only if process is alive\*

Here, we try to create a process and linking it to the current process using `spawn_link` and will try to extract only linked processes. With no surprise, it should give the `self` output pid which is the current process in our case.

```elixir
    iex> pid = spawn_link fn -> receive do :name -> IO.puts "Hello Medium" end end
    #PID<0.210.0>
    iex> Process.info pid, :links                                                 
    {:links, [#PID<0.85.0>]}
    iex> self
    #PID<0.85.0>
```

![image](https://cdn-images-1.medium.com/max/2000/1*hn9b7mxHSZkqxRfhnRMcJA.png)

#### Extracting whole information

```elixir
    iex> Process.info pid
```

![image](https://cdn-images-1.medium.com/max/2000/1*0GfghGYjk_lqKuggf23sOA.png)

### 3\|&gt; Forcing Keys in a Map parameter in functions

Consider that you need to pass a **map** to a function and you have to ensure certain **keys** in the map then allow it to use the function body.

You can achieve this using structs with `@enforce_keys` attribute. But, you can still use the pattern matching to the keys of a map.

```elixir
    defmodule Hello do
      def hello(%{name: _name, blog: _blog} = map) do
        IO.inspect map
      end
    end
```

Here, we don’t care how many keys present inside the map but we need atleast two keys `name`, `blog` to be present inside the map with any values.

#### Execution screenshot

![image](https://cdn-images-1.medium.com/max/2000/1*Me9434rZFV1lEmwZd2toNA.png)

If you observe the screenshot, we tried to access the function by sending a map with single key parameter where it is not allowed then with two keys map and the keys are exactly pattern matched where it is allowed to use the function then eventually tried with more keys still it worked.

> Thanks to pattern matching.

### 2\|&gt; Universal Code base formatter

Code formatting is easy now with the help of mix task `mix format`. It will take care of all you care about cleaning and refactoring as well. It can assure a clean code base or simply universal code base which maintains some useful standards. This really saves a lot of time.

```elixir
    mix format filename
```

To know more about the codebase formatting, check out my recent article on

#### \[Code Formatter The Big Feature in Elixir

1.6\]\([https://medium.com/blackode/code-formatter-the-big-feature-in-elixir-v1-6-0-f6572061a4ba](https://medium.com/blackode/code-formatter-the-big-feature-in-elixir-v1-6-0-f6572061a4ba)\)

which explains all about using `mix format` task and its configuration in detail with screen shots of vivid examples.

### 1\|&gt; Alias multiple modules in one line

```elixir
    alias Mod.{One, Two, Three}
```

is same as following

```elixir
    alias Mod.One
    alias Mod.Two
    alias Mod.Three
```

In similar fashion, you can alias your current module like

```elixir
    defmodule User.Authentication do
      defstruct [:key, :token]

      alias __MODULE__, as: Auth

      .....
    end
```

In general, with out using `alias __MODULE__` , we have to type full module name like `%User.Authentication{key: "key", token: ".."}` to define the struct but now simply `%Auth{key: "key", token: ".."}` will be the better approach.

## Part 8

I am using the following development stack at the moment of writing this article.

```elixir
Hex:    0.18.1
Elixir: 1.7.3
OTP:    21.1.1
```

## 1— Replacing a Key in Map

I know, you might be thinking why some one would do that. I too had the same feeling until I met with a requirement in one of my previous projects where I was asked to replace `address1` with `address` and some other keys as well. It is a huge one. I have to do change many keys as well.

I’m here with a simple `map` to keep it understandable. But, the technique is same.

> The thing to remember is; be smart and stay long;

```elixir
details = %{name: "john", address1: "heaven", mobile1: "999999999"}
```

The above map comprises of **two** weird keys address1 and mobile1 where we do workout on to take over them.

```elixir
iex> details = %{name: "john", address1: "heaven", mobile1: "999999999"}
%{address1: "heaven", mobile1: "999999999", name: "john"}

iex> Map.new(details, fn  
  {:address1, address} -> {:address, address}
  {:mobile1, mobile} -> {:phone, mobile} 
  x -> x 
end)

%{address: "heaven", name: "john", phone: "999999999"} #output
```

### What’s the magic here❓

Nothing , just pattern\_matching, the boon for functional programmers. The magic lies inside Map.new and the **anonymous** function where we used our **magic band** to see the actual **logic**.

![](https://cdn-images-1.medium.com/max/3816/1*rlUV3ulHJ_VN7bsOTsVi3A.png)

## 2— Exchanging the Two values

This is just to know things can be done in **elixir** for beginners.

```elixir
iex> x=5
5
iex> y=7
7
iex> {x,y}={y,x}
{7,5}

iex>x
7
iex>y
5
```

## 3 — Filtering With match?

As we know, `Enum.filter` will do the job based on the evaluation of a condition. But still, we can use the **pattern** to **filter** things.

Let me make you clear with the following example.

I have a list of **users** where their names are mapped to their preferred programming language.

```elixir
iex> user_and_programming_languages = 
 %{john: "elixir", latha: "elixir", hari: "erlang", toy: "perl"}

%{hari: "erlang", john: "elixir", latha: "elixir", toy: "perl"}
```

Like above, the **user\_names** are mapped with their **languages**. Now, our job is to filter the _**users**_ of **elixir** language.

Just let me know which one you do prefer after checking out the following…

```elixir
iex> user_and_programming_languages |>
Enum.filter(fn {_, lang} -> lang=="elixir" end )

[john: "elixir", latha: "elixir"]  
#output
```

OR, using match? for pattern based

```elixir
user_and_programming_languages = 
 %{john: "elixir", latha: "elixir", hari: "erlang", toy: "perl"}

user_and_programming_languages |>
Enum.filter(&match?({_, "elixir"}, &1) )

[john: "elixir", latha: "elixir"]  
#output
```

Checkout the screenshots of _evaluation_

![](https://cdn-images-1.medium.com/max/3830/1*AAW1xh_NrmGCxWWZPM3eAw.png)

![](https://cdn-images-1.medium.com/max/3844/1*yLgMcHqdSgWhNQFhXqjXgw.png)

I took the simpler lines of code to give a demo of using match? but, it’s usages are limit less. Just think about the **pattern** and match over it. Think out of the box guys. But don’t blow up your mind and don’t blame me for that.

**Warning: ⚠️**

Make sure of your **pattern** as the **first** parameter to the match? otherwise, you have to pay for the compiler. Just kidding 😃

```elixir
iex> user_and_programming_languages |>
Enum.filter(&match?(&1, {_, "elixir"},) )
```

![](https://cdn-images-1.medium.com/max/3838/1*OWhJ_6CgVOr42-7vvyXWJg.png)

Let’s bump for more.

## 4 — Bangpipe &lt;\|&gt; Left and Right

As we know elixir is free language, where you can build anything you need. But, be careful It is just to show you something is achievable.

It is a **LEFT** and **RIGHT** Pipe

We usually end up with some sort of results in the format {:ok, result} . This can be found anywhere in your project sooner or later.

If you pipe it to the function obviously you will end up with an unknown expectation. I am talking about run-time errors. Our \|&gt; is not smart enough to check it for whether is a direct result or {:ok, result} .

Let’s build it for the **GOD** sake but not for your **client**.

⚠️ _**Don’t use this ever and don’t do write macros until you write new framework.**_

```elixir
iex> defmodule Bangpipe do
  defmacro left <|> right do
    quote do
      unquote(left)
      |> case do
        {:ok, val} -> val 
        val -> val
      end
      |> unquote(right)
    end
  end
end
```

This Bangpipe I mean &lt;\|&gt; is defined to do the task. Before sending the result to the function in right, it is performing the pattern matching over the result to identify what kinda result it is. I hope you understood what actually the result I am talking about. [**The Secret behind Elixir Operator Re-Definitions : + to -** \*Just for Fun](https://medium.com/blackode/the-secret-behind-elixir-operator-overriding-to-a564fd6c0dd6)\*

We can also do re definitions on elixir operators. Check above URL.

How to use ❓

Just import **Bangpipe** the left-right pipe will be loaded. Copy and paste to experience it in live and fast into iex

```elixir
iex[6]  import Bangpipe
Bangpipe

iex[7]  data = {:ok, [1,2,3,4,5]} 
{:ok, [1, 2, 3, 4, 5]}

iex[8]  data <|> length
5
```

> “Good programmers write the code & great programmers steal the code” This tip is copied from the [Elixir Lightening Talk](https://www.youtube.com/watch?v=2vocLGYFj8Q&t=25s)

**\*Bangpipe** **&lt;\|&gt;** **execution\***

![\*\*Bangpipe\*\* \*\*&amp;lt;\|&amp;gt;\*\* \*\*execution\*\*](https://cdn-images-1.medium.com/max/3820/1*FDNBw77-79TpQNyoqphuZw.png)

## 5 — mix hex.info

This is highly recommended command to check out **what hex version** is installed and how it is **build** with.

### Why is that useful?

I have had experienced a bug in the project in my machine but not from my co-programmer though we work on the same project. The actual problem is we are using different hex versions. I came to know this by running mix hex.info in both systems. That saved a lot of time.

```elixir
mix hex.info
```

That gives the information about the hex and how it is built with.

_mix hex.info_

![mix hex.info](https://cdn-images-1.medium.com/max/3830/1*3AaPhLgTwlYDPX-tfkbslg.png)

Similarly, we can find the information of any package like mix hex.info package\_name

```elixir
mix hex.info typex
```

_mix hex.info typex_

![mix hex.info typex](https://cdn-images-1.medium.com/max/3822/1*KdnXWiWt8IiORNUgJIrUxQ.png)

You can be more specific too by mentioning the version of the package.

```elixir
mix hex.info <<package_name>> <<version>>
```

Replace _**&lt;&gt;**_ and _**&lt;&gt;**_ as you required.

![](https://cdn-images-1.medium.com/max/3816/1*wMQpaAe5P2ldTzCs09TzVw.png)

## 6 — Finding the function callers in a project.

_Let’s create a brief story here._

The moment when GOD’S against you, your manager will come and give you a vast legacy project code and asked you to find out _**all the places where a particular function**_ is called. In a sentence, the **callers** of a particular function.

**What will you do?** I don’t think it is a smart way of surfing in each and every file. I don’t. So, you don’t.

```elixir
mix xref callers Module.function

example

mix xref callers Poison.decode
```

Prints all callers of the given **CALLEE**, which can be one of: **Module**, **Module.function**, or **Module.function/arity**.

### Examples:

```elixir
mix xref callers ***Module***
mix xref callers ***Module.fun***
mix xref callers ***Module.fun/3***
```

_mix xref callers Poison.decode_

![mix xref callers Poison.decode](https://cdn-images-1.medium.com/max/3818/1*5pDTNeE2I0lrqBlR18inOQ.png)

You can add function arity to be more specific, in this case mix xref callers Poison.decode/1 .

**BOOM !** Now, you can surprise the manager with results before he reaches his cabin. Be fast and smart.

I solely experienced this when my co-worker I better say **co-programmer** asked for help regarding the situation but there's no manager in my case.

Explore more about **xref**

```elixir
mix help **xref**
```

## 7 — Integer to Float Conversion

You need this in rare cases to meet the type specifications while evaluating some kinda conditional code logic which results to a Boolean value.

```elixir
iex[12] 5.0==5             # types are not considered here
true
iex[13] 5.0===5          # types are strict considered here
false
```

To convert integer to float I just divide the value by 1 using Kernel./ .

```elixir
iex[2] val = 5
5
iex[3] is_integer val
true
iex[4] val = val/1
5.0
iex[5] is_integer val
false
iex[6] is_float val
true
```

_Kernel./ for convert anything to float_

![Kernel./ for convert anything to float](https://cdn-images-1.medium.com/max/3830/1*P-jCkiLl8XWBiUmsgEpF1Q.png)

## **8 — Sigils & Interpolation**

As you already know sigils will help us in less typing for creative stuff. Consider the following example of defining a list of strings.

```elixir
sentence = ["I", "Love", "coding", "in", "elixir"]
```

That is a lot of typing here. We can improve this without typing any " marks with the help of ~w **sigil**.

It returns a list of “words” split by whitespace. Character unescaping and **interpolation** happens for each word.

```elixir
sentence = ~w(I Love coding in elixir)
```

We can still use the interpolation as well. Suppose, I would like to make the language dynamic in the above sentence, I mean one loves elixir and other loves some other languages. So, let’s put the language in a variable lang and interpolate it over.

```elixir
iex[6] lang = "haskel"
"haskel"
iex[7] sentence = **~w(I Love #{lang})**
["I", "Love", "haskel"]
iex[8] lang = "Rust"
"Rust"
iex[9] sentence = ~w(I Love #{lang})
["I", "Love", "Rust"]
```

What if you don’t want the interpolation? just use ~W a capital form.

```elixir
iex[10] sentence = ~W(I Love #{lang})
["I", "Love", "\#{lang}"]
```

## 9 — Grouping of things

I have a collection of hotel\_bookings and I need to group them based on their booking status.

To keep this simple, I am just using some false random collection.

```elixir
iex> hotel_bookings = [ 
%{name: "John", country: "usa", booking_status: :success},
%{name: "Hari", country: "india", booking_status: :fail},
%{name: "Mahesh", country: "austria", booking_status: :pending},
%{name: "Ruchi", country: "paris", booking_status: :success},
%{name: "Nitesh", country: "malasia", booking_status: :success},
%{name: "Manoj", country: "japan", booking_status: :fail},
%{name: "Akhilesh", country: "china", booking_status: :pending},
%{name: "Rajesh", country: "india", booking_status: :fail},
%{name: "Payal", country: "london", booking_status: :success},
%{name: "Kumar", country: "france", booking_status: :fail}
]
```

Let’s group the things.

```elixir
iex> Enum.group_by(hotel_bookings, &Map.get(&1, :booking_status))
%{
  fail: [
    %{booking_status: :fail, country: "india", name: "Hari"},
    %{booking_status: :fail, country: "japan", name: "Manoj"},
    %{booking_status: :fail, country: "india", name: "Rajesh"},
    %{booking_status: :fail, country: "france", name: "Kumar"}
  ],
  pending: [
    %{booking_status: :pending, country: "austria", name: "Mahesh"},
    %{booking_status: :pending, country: "china", name: "Akhilesh"}
  ],
  success: [
    %{booking_status: :success, country: "usa", name: "John"},
    %{booking_status: :success, country: "paris", name: "Ruchi"},
    %{booking_status: :success, country: "malasia", name: "Nitesh"}, 
    %{booking_status: :success, country: "london", name: "Payal"}
  ]
}
```

This is well and good but I need only name in the list instead of map. I mean some thing like %{fail: \["Hari", "Manoj"\], ...}

We just need to tell the group\_by what to return in return of grouping

```elixir
iex>  Enum.group_by(hotel_bookings, &Map.get(&1, :booking_status), &Map.get(&1, :name))
%{
  fail: ["Hari", "Manoj", "Rajesh", "Kumar"],
  pending: ["Mahesh", "Akhilesh"],
  success: ["John", "Ruchi", "Nitesh", "Payal"]
}
```

## 10 — Running your previous failed test only

```elixir
mix test --failed
```

## BONUS

### Documentation Metadata

In a project, there will be many programmers who write different modules. It will hard to find who has written what.

To keep the track of such things, we can add metadata to the @moduledoc. So, we come to know the developer whom we can blame to queries that hit to your mind and kept eating it while using the module.

```elixir
@moduledoc "Wrong code leads you to the right bug "
@moduledoc authors: ["blackode", "doe"], since: "1.1.2"
```

## Note

Check out our Medium Publication

[![Medium Blog Image](.gitbook/assets/medium_blog.jpg)](https://medium.com/blackode)

If you wish to join our telegram channel, here it is

[![Telegram Blog Image](.gitbook/assets/telegram_channel.png)](https://t.me/blackoders)

## How to contribute ?

Contribute to `Part-9` creating a new branch `Part-9` and make a pull request with atleast 3 Unique tips.

### Thanks for Reading.


## Contributors ✨

Thanks goes to these wonderful people ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore -->
<table>
  <tr>
    <td align="center"><a href="https://blackode.in"><img src="https://avatars2.githubusercontent.com/u/9107477?v=4" width="100px;" alt="Ankanna"/><br /><sub><b>Ankanna</b></sub></a><br /><a href="https://github.com/blackode/elixir-tips/commits?author=blackode" title="Code">💻</a> <a href="https://github.com/blackode/elixir-tips/commits?author=blackode" title="Tests">⚠️</a> <a href="#infra-blackode" title="Infrastructure (Hosting, Build-Tools, etc)">🚇</a></td>
    <td align="center"><a href="https://praveenperera.com"><img src="https://avatars0.githubusercontent.com/u/1775346?v=4" width="100px;" alt="Praveen Perera"/><br /><sub><b>Praveen Perera</b></sub></a><br /><a href="https://github.com/blackode/elixir-tips/issues?q=author%3Apraveenperera" title="Bug reports">🐛</a></td>
  </tr>
</table>

<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!