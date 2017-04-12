# Killer Elixir-Tips

The collection of Elixir Tips and Tricks...

## Part 1

### 1. Multiple [ OR ]

This is just the other way of writing Multiple **OR** conditions. This is not the recommended approach because in regular approach when the condition evaluates to **true** , it stops executing the remaining conditions which saves time of evaluation unlike this approach which evaluates all conditions first in list. This is just bad but good for discoveries. 

<script src="https://gist.github.com/blackode/9e3d873ad803ef6d4f653d8bf2e1b028.js"></script>

### 2. i( term) Elixir Term Type and Meta Data

Prints information about the data type of any given term. Try that in `iex` and see the magic.

```elixir
iex> i(1..5)
```

### 3. iex Custom Configuration - iex Decoration

Save the following file as `.iex.exs` in your `~` home directory and see the magic.

<script src="https://gist.github.com/blackode/5728517116d7a4d08f0a4faddd8c145a.js"></script>



![img](https://cdn-images-1.medium.com/max/800/1*iy-IELdB8fjTo5H0sABlBQ.png)

### 4. Creating Custom Sigils and Documenting

Each `x` sigil call respective `sigil_x` definition

<script src="https://gist.github.com/blackode/15ddca326d604fbef68ae833504ac37e.js"></script>

### 5. Custom Error Definitions

5. Custom Error Definitions

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

### 6. Get a Value from Nested Maps Easily

<script src="https://gist.github.com/blackode/c19472093bedff864a5e6363784dd026.js"></script>

Getting a Value from Nested Maps

### 7. With Statement Benefits

The special form `with` is used to chain a sequence of matches in order and finally return the result of `do:` if all the clauses match. However, if one of the clauses does not match, its result of the miss matched expression is immediately returned.

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
## since  2 <- 3+1 is not matched so the result of 3+1 is returned.Writing Protocols**
```

### 8. Writing Protocols

#### Define a Protocol

A **Protocol** is a way to dispatch to a particular implementation of a function based on the type of the parameter.
The macros `defprotocol` and `defimpl` are used to define Protocols and Protocol implementations respectively  for different types in the following example.

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

Load the code into `iex` and execute

```elixir
iex> Triple.triple(3) 
9
Triple.triple([1, 2])
[1, 2, 1, 2,1,2]
```

### 9. Ternary Operator

There is no ternary operator like `true ? "yes" : "no"` . So, the following is suggested.

```elixir
"no" = if 1 == 0, do: "yes", else: "no"
```

### 10. Advantage of Kernel.||

When using pipelines, sometimes we break the pipeline for `or` operation. 
For example:

```elixir
result = :input
|> do_something
|> do_another_thing
```

```
# Bad
result = (result || :default_output)
|> do_something_else
```

Indeed, `||` is only a shortcut for `Kernel.||` . We can use `Kernel.||` in the pipeline instead to avoid breaking the pipeline.

The code above will be:

```elixir
result = :input
|> do_something
|> do_another_thing
|> Kernel.||(:default_output)  #<-- This line
|> do_something_else
```

This above tip is from [qhwa](https://medium.com/@qhwa_85848)



## Part 2

### **1  Code Grouping**

Code grouping stands for something great. It shows you how your code is grouped when you write multiple lines of code in single line with out using braces. It will be more clear with the following example.

```elixir
one 1 |> two()
```

If you want to see how this line of code is grouped into, you can check in the following format..

```elixir
quote(do: one 1 |> two()) |> Macro.to_string |> IO.puts
one(1 |> two())
```

So, by using the `quote` and `Macro.to_string` you can see how our code is grouped.

This tip came out in discussion with the creator of **Ecto** [**MichalMuskala**](https://elixirforum.com/users/michalmuskala) in the Elixir forum.

### 2  Elixir Short Circuit Operators && — ||

These replaces the nested complicated conditions. These are my best friends in the situations dealing with more complex comparisons. Trust me you gonna love this.

The `||` operator always returns the first expression which is true. Elixir doesn’t care about the remaining expressions, and won’t evaluate them after a match has been found.

#### ||

```elixir
false || nil || :blackode || :elixir || :jose
```

Here if you observe the first expression is false next `nil` is also false in elixir next `:blackode` which evaluates to true and its value is returned immediately with out evaluating the `:elixir` and `:jose` . Similarly if all the statements evaluates to `false` the last expression is returned.

#### &&

```elixir
iex> true && :true && :elixir && 5
5
iex> nil && 100
nil
iex> salary = is_login && is_admin && is_staff && 100_000
```

This `&&` returns the second expression if the first expression is `true` or else it returns the first expression with out evaluating the second expression. In the above examples the last one is the situation where we encounter to use the `&&` operator.

### 3  Comparing two different data types

I have self experience with this . When I am novice in elixir, I just compared `"5" > 4` unknowingly by an accident and to my surprise it returned with `true`.

In **Elixir** every term can compare with every other term. So one has to be careful in comparisons.

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

>  number < atom < reference < fun < port < pid < tuple < map < list < bitstring (binary)

### 4  Arithmetic Operators as Lambda functions

When I see this first time, I said to my self “**Elixir is Crazy” . **This tip really saves time and it resembles your smartness. In Elixir every operator is a macro. So, we can use them as lambda functions.

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

### 5  Binary pattern matching

This is my recent discovery. I always encounter a situation like converting `"$34.56"` which is a string and I suppose do arithmetic operations. I usually do something like this before binary pattern matching..

![img](https://cdn-images-1.medium.com/max/800/0*ipJJTjsFiaGmCBpc.)

```elixir
iex> value = "$34.56"              |>
iex ...        String.split("$")   |>  
iex ...         tl                 |>   
iex ...         List.first         |> 
iex ...         String.to_float
34.56
```

#### Tip Approach

This tip made my day easy. I recently used this is in one of my projects.

```elixir
iex> "$" <> value = "$34.56"
"$34.56"
iex> String.to_float value  
34.56
```

### 6  Recompiling Project

At beginning stage, I used to press `^c` `^c` twice and restart shell as `iex -S mix` whenever I make changes to the project files. If you are doing this now, stop it right now. You can just recompile the project.

```elixir
$ iex -S mix
iex> recompile() 
```

**Warning:** The changes in the `config/config.ex` are not reflected. You have to restart the shell again.

### 7  Logger Module

Logger is one of my favorite modules. This come in default and starts along with your application. You have to just `require` this module. When I am new to Elixir, I always used to write the console outputs as `IO.puts "This is value of data"` for code debugging but, those lines get mixed up with other lines of information and It became hard to trace those lines.

This `Logger` module solved my problem. It has many features but, I use three definitions very often `warn` `info` and `error` Each definition prints the information with different **colors **which is more easy to find the statement at a glance.

The best side of this module is it prints along with the **time**, means it also prints the time while executing your statement. So, you can know the direction of flow of execution.

Before using the `Logger` module one has to do `require Logger` so all macros will be loaded inside your working module.

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

This tip is from [Anwesh Reddy](https://medium.com/@kanishkablack)

### 8  Finding All Started Applications

We can check the all the applications which are started along with our application. Sometimes we have to check whether a particular application is started or not. So, it helps you in those situations.. If you are a beginner, you don’t feel of using this much. But I am pretty sure of this tip will become handy when you work with multiple applications.

```elixir
iex> Application.started_applications
[{:logger, 'logger', '1.4.0'}, {:iex, 'iex', '1.4.0'},
 {:elixir, 'elixir', '1.4.0'}, {:compiler, 'ERTS  CXC 138 10', '7.0.1'},
 {:stdlib, 'ERTS  CXC 138 10', '3.0.1'}, {:kernel, 'ERTS  CXC 138 10', '5.0.1'}]
```

### 9  Advantage of Map keys as :atoms and binary(strings)

Before I let you to use this tip, I just want to remind you that **:atoms** are not garbage collected. Atom keys are great! If you have a fixed number of them defined statically in your code, you are in no danger.What you should not do is convert user supplied input into atoms without sanitizing them first because it can lead to out of memory. **You should also be cautious if you create dynamic atoms in your code.**

But , you can use the `.` to retrieve the data from the keys as `map.key` unlike the usual notation like `map["key"]` . That really saves the typing. But, I don’t encourage this because, as a programmer we should really care about memory.

![img](https://cdn-images-1.medium.com/max/800/0*DQf-KHbpd6qcgEpz.)

```elixir
iex> map = %{name: "blackode", blog: "medium"}
%{blog: "medium", name: "blackode"}
iex> map.name
"blackode"
iex> map.blog
"medium"
```

Be sure that when you try to retrieve a key with `.` form which is not present in the map, it will raise an **key error **instead of returning the `nil`unlike the `map["key"]` which returns `nil` if `key` is not present in `map`

```elixir
iex> map["age"]
nil
```

```
iex> map.age
Bug Bug ..!!** (KeyError) key :age not found in: %{blog: "medium", name: "blackode"}
Bug Bug ..!!
```

### 10 Color Printing

Elixir `>=1.4.0` has **ANSI** color printing option to console. You can have great fun with colors. 
You can also provide **background colors**.

```elixir
iex> import IO.ANSI
iex> IO.puts red <> "red"<>green<>" green" <> yellow <> " yellow" <>   reset <> " normal"
red green yellow normal
```

The red prints in red color, green in green color, yellow in yellow color and normal in white. Have fun with colors…

For more details on color printing check [**Printex**](https://github.com/blackode/printex) module which I created for fun in Elixir.

![img](https://cdn-images-1.medium.com/max/800/0*DQf-KHbpd6qcgEpz.)

![img](https://cdn-images-1.medium.com/max/1000/0*Qskz94BcqMSyPAuH.png)



## Part 3

### 1. Functions as Guard Clauses

We cannot make use of the functions as guard clauses in elixir. It means, `when` cannot accept functions that returns Boolean values as conditions. Consider the following lines of code…

```elixir
defmodule Hello do
  def hello(name,age) when is_kid(age) do
    IO.puts "Hello Kid #{name}"
  end
  def hello(name,age) when is_adult(age) do
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

Here we defined a **module** `Hello` and a function `hello` that takes two parameters of `name` and `age`. So, based on age I am trying `IO.puts`accordingly. If you do so you will get an error saying….

```
** (CompileError) hello.ex:2: cannot invoke local is_kid/1 inside guard
    hello.ex:2: (module)
```

This is because **when** cannot accept functions as guards. We need to convert them to `macros` 
Lets do that…

````elixir
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

  def hello(name,age) when is_kid(age) do
    IO.puts "Hello Kid #{name}"
  end

  def hello(name,age) when is_adult(age) do
    IO.puts "Hello Mister #{name}"
  
   def hello(name,age) do
    IO.puts "Hello Youth #{name}"
  end

end
````

In the above lines of code, we wrapped all our guards inside a module `MyGuards` and make sure the module is top of the module `Hello` so, the macros first gets compiled. Now compile and execute you will see the following output..

```elixir
iex> Hello.hello "blackode",21
Hello Mister blackode
:ok
iex> Hello.hello "blackode",11
Hello Kid blackode
:ok
```

### 2. Finding the presence of Sub-String

Using `=~` operator we can find whether the **right** sub-string present in **left** string or not..

```elixir
iex> "blackode" =~ "kode" 
true  
iex> "blackode" =~ "medium" 
false  
iex> "blackode" =~ "" 
true
```

### 3. Finding whether Module is loaded or not

Sometimes, we have to make sure that certain module is loaded before making a call to the function. We are supposed to ensure the module is loaded.

```
Code.ensure_loaded? <Module>
```

```elixir
iex> Code.ensure_loaded? :kernel
true
iex> Code.ensure_loaded :kernel
{:module, :kernel}
```

Similarly we are having `ensure_compile` to check whether the module is compiled or not…

### 4. Binary to Capital Atom

Elixir provides a special syntax which is usually used for module names. What is called a module name is an **uppercase*** ASCII letter* followed by any number of **lowercase*** or ***uppercase*** ASCII letters*, *numbers*, or *underscores*.

This identifier is equivalent to an atom prefixed by `Elixir.`. So in the `defmodule Blackode` example `Blackode` is equivalent to `:"Elixir.Blackode"`

When we use `String.to_atom "Blackode"` it converts it into `:Blackode` But actually we need something like “**Blackode” **to **Blackode. **To do that we need to use `Module.concat`

```elixir
iex(2)> String.to_atom "Blackode"
:Blackode
iex(3)> Module.concat Elixir,"Blackode"
Blackode
```

In Command line applications whatever you pass they convert it into **binary**. So, again you suppose to do some casting operations …

### 5. Pattern match [ vs ]destructure.

We all know that `=` does the pattern match for left and right side. We cannot do `[a,b,c]=[1,2,3,4]` this raise a `MatchError`

```elixir
iex(11)> [a,b,c]=[1,2,3,4]
** (MatchError) no match of right hand side value: [1, 2, 3, 4]
```

We can use `destructure/2` to do the job.

```elixir
iex(1)> destructure [a,b,c],[1,2,3,4]
[1, 2, 3]
iex(2)> {a,b,c}
{1, 2, 3}
```

If the left side is having more entries than in right side, it assigns the `nil`value for remaining entries..

```elixir
iex> destructure([a, b, c], [1])
iex> {a, b, c} 
{1, nil, nil}
```

### 6. Data decoration [ inspect with :label ] option

We can decorate our output with `inspect` and `label` option. The string of `label` is added at the beginning of the data we are inspecting.

```elixir
iex(1)> IO.inspect [1,2,3],label: "the list "
the list : [1, 2, 3]
[1, 2, 3]
```

If you closely observe this it again returns the inspected data. So, we can use them as intermediate results in `|>` pipe operations like following……

```elixir
[1, 2, 3] 
|> IO.inspect(label: "before change") 
|> Enum.map(&(&1 * 2)) 
|> IO.inspect(label: "after change") 
|> length
```

You will see the following `output`

```elixir
before change: [1, 2, 3]
after change: [2, 4, 6]
3
```

### 7. Anonymous functions to pipe

We can pass the anonymous functions in two ways. One is directly using `&`like following..

```elixir
[1,2,3,4,5]
|> length
|> (&(&1*&1)).()
```

This is the most weirdest approach. How ever, we can use the reference of the anonymous function by giving its name.

```elixir
square = & &1 * &1
[1,2,3,4,5]
|> length
|> square.()
```

The above style is much better than previous . You can also use `fn` to define anonymous functions.

### 8. Retrieve Character Integer Codepoints — ?

We can use `?` operator to retrieve character integer codepoints.

```elixir
iex> ?a
97
iex> ?#
35
```

The following two tips are mostly useful for beginners…

### 9. Subtraction over Lists

We can perform the subtraction over lists for removing the elements in list.

```elixir
iex> [1,2,3,4.5]--[1,2]
[3, 4.5]
iex> [1,2,3,4.5,1]--[1]  
[2, 3, 4.5, 1]
iex> [1,2,3,4.5,1]--[1,1]
[2, 3, 4.5]
iex> [1,2,3,4.5]--[6]    
[1, 2, 3, 4.5]
```

We can also perform same operations on char lists too..

```elixir
iex(12)> 'blackode'--'ode'
'black'
iex(13)> 'blackode'--'z'    
'blackode'
```

If the element to subtract is not present in the list then it simply returns the list.

### 10. Using Previous results in IEx

When you are working with `iex` environment , you can see a number increment every time you evaluate an expression in the shell like `iex(2)>``iex(3)>`

Those numbers helps us to reuse the result with `v/1` function which has been loaded by default..

```elixir
iex(1)> list = [1,2,3,4,5]
[1, 2, 3, 4, 5]
iex(2)> double_lsit = Enum.map(list, &(&1*2))
[2, 4, 6, 8, 10]
iex(3)> v 1         
[1, 2, 3, 4, 5]
iex(4)> v(1) ++ v(2)
[1, 2, 3, 4, 5, 2, 4, 6, 8, 10]
```
