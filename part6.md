# Part 6

```text
Elixir version 1.5.1 & Erlang otp version 20
```

## 1 Extracting Project Information

```elixir
Mix.Project.config[:version] # returns the version
Mix.Project.config[:app] # returns app name
```

You have to be inside the `mix project` when you are trying. See this in action… [![asciicast](https://asciinema.org/a/g75QG1GmKR1Ret0PMThjxDd8u.png)](https://asciinema.org/a/g75QG1GmKR1Ret0PMThjxDd8u)

## 2 Inner Binary Representation of String

This is a common trick in `elixir` . You have to concatenate the null byte `<<0>>` to a string that you want to see its inner binary representation like in the following way…

```elixir
iex> “hello” <> <<0>>
<<104, 101, 108, 108, 111, 0>>
```

## 3 Initialisation of Multiple with Same value

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

## 4 Not Null implementation in Structs

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

## 5 Check Whether Function is Exported or not

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

## 6 Splitting the string with Pattern

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

## 7 Checking the closeness of strings

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

## 8 last and first for Strings

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

## 9 Executing code Immediately after loading a Module

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

## 10 Chain of \[ or \] ’ s  in guards

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

Thanks for Reading.

If you feel they are useful...

