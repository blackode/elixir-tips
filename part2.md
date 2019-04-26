# Part 2

## **1  Code Grouping**

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

## 2  Elixir Short Circuit Operators && — \|\|

These replaces the nested complicated conditions. These are my best friends in the situations dealing with more complex comparisons. Trust me you gonna love this.

The `||` operator always returns the first expression which is true. Elixir doesn’t care about the remaining expressions, and won’t evaluate them after a match has been found.

### \|\|

```elixir
false || nil || :blackode || :elixir || :jose
```

Here if you observe the first expression is false next `nil` is also false in elixir next `:blackode` which evaluates to true and its value is returned immediately with out evaluating the `:elixir` and `:jose` . Similarly if all the statements evaluates to `false` the last expression is returned.

### &&

```elixir
iex> true && :true && :elixir && 5
5
iex> nil && 100
nil
iex> salary = is_login && is_admin && is_staff && 100_000
```

This `&&` returns the second expression if the first expression is `true` or else it returns the first expression with out evaluating the second expression. In the above examples the last one is the situation where we encounter to use the `&&` operator.

## 3  Comparing two different data types

I have self experience with this . When I am novice in elixir, I just compared `"5" > 4` unknowingly by an accident and to my surprise it returned with `true`.

In **Elixir** every term can compare with every other term. So one has to be careful in comparisons.

![img](https://cdn-images-1.medium.com/max/800/0*SOFSiJHylCKMOb-9.)

```elixir
iex> x = "I am x "
"I am x "
iex> x > 34
true
iex> x > [1,2,3]
true
iex> [1,2,3] < 1234567890
false
```

**Order of Comparison**

> number &lt; atom &lt; reference &lt; fun &lt; port &lt; pid &lt; tuple &lt; map &lt; list &lt; bitstring \(binary\)

## 4  Arithmetic Operators as Lambda functions

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

## 5  Binary pattern matching

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

### Tip Approach

This tip made my day easy. I recently used this is in one of my projects.

```elixir
iex> "$" <> value = "$34.56"
"$34.56"
iex> String.to_float value  
34.56
```

## 6  Recompiling Project

At beginning stage, I used to press `^c` `^c` twice and restart shell as `iex -S mix` whenever I make changes to the project files. If you are doing this now, stop it right now. You can just recompile the project.

```elixir
$ iex -S mix
iex> recompile
```

**Warning:** The changes in the `config/config.ex` are not reflected. You have to restart the shell again.

## 7  Logger Module

Logger is one of my favorite modules. This come in default and starts along with your application. You have to just `require` this module. When I am new to Elixir, I always used to write the console outputs as `IO.puts "This is value of data"` for code debugging but, those lines get mixed up with other lines of information and It became hard to trace those lines.

This `Logger` module solved my problem. It has many features but, I use three definitions very often `warn` `info` and `error` Each definition prints the information with different **colors** which is more easy to find the statement at a glance.

The best side of this module is it prints along with the **time**, means it also prints the time while executing your statement. So, you can know the direction of flow of execution.

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

## 8  Finding All Started Applications

We can check the all the applications which are started along with our application. Sometimes we have to check whether a particular application is started or not. So, it helps you in those situations.. If you are a beginner, you don’t feel of using this much. But I am pretty sure of this tip will become handy when you work with multiple applications.

```elixir
iex> Application.started_applications
[{:logger, 'logger', '1.4.0'}, {:iex, 'iex', '1.4.0'},
 {:elixir, 'elixir', '1.4.0'}, {:compiler, 'ERTS  CXC 138 10', '7.0.1'},
 {:stdlib, 'ERTS  CXC 138 10', '3.0.1'}, {:kernel, 'ERTS  CXC 138 10', '5.0.1'}]
```

## 9  Advantage of Map keys as :atoms and binary\(strings\)

Before I let you to use this tip, I just want to remind you that **:atoms** are not garbage collected. Atom keys are great! If you have a fixed number of them defined statically in your code, you are in no danger. What you should not do is convert user supplied input into atoms without sanitizing them first because it can lead to out of memory. **You should also be cautious if you create dynamic atoms in your code.**

But , you can use the `.` to retrieve the data from the keys as `map.key` unlike the usual notation like `map["key"]` . That really saves the typing. But, I don’t encourage this because, as programmers we should really care about memory.

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

## 10 Color Printing

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

