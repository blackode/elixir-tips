
<img class="elixirtip-img" src="assets/images/parts/tips7.jpg"/>

## 10\|&gt; Pretty Printing quoted Expression

### Macro.to\_string \|&gt; IO.puts

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

### Check this in live

[![asciicast](https://asciinema.org/a/161913.png)](https://asciinema.org/a/161913)

## 9\|&gt; Finding the loaded files in iex and dynamic loading files on-fly

1. **Finding the loaded files**

### Code.loaded\_files

In elixir, we are having a definition `loaded_files` in the module `Code` used to find the loaded files.

Let’s see this.

Run the command `iex` inside your terminal and call the function `Code.loaded_files` .

It gives an empty list `[]` as output because we haven’t loaded any files so far.

```elixir
    $ iex
    iex> Code.loaded_files
    []
```

### File Loading

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

### Dynamic loading on-fly

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

### Let's check this in action

[![asciicast](https://asciinema.org/a/161920.png)](https://asciinema.org/a/161920)

## 8\|&gt; Providing deprecation reason with

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

![](https://cdn-images-1.medium.com/max/2000/1*n0RNxQCvWwy3ZQeIKeG_ZQ.png) File Scrennshot Vim Editor

This file comprises of two modules `Hello` and `Printee` . The `Printee` module comprises of two functions `print/0` and `show/0` . Here purposely, `print/0` is considered as a deprecated function used inside the `Hello` module.

The mix compiler automatically looks for calls to deprecated modules and emit warnings during compilation.

So, when you compile the project `mix compile`, it gives a warning saying

> **“print/0 is deprecated use show/0”**

like in the following screenshot.

![image](https://cdn-images-1.medium.com/max/2000/1*iF5ROIflyq_UPuNJBtTrHA.png) Deprecated Function Warning

### Check the live coding

[![asciicast](https://asciinema.org/a/161939.png)](https://asciinema.org/a/161939)

## 7\|&gt; Module Creation with create function

### **Module.create/3**

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

![image](https://cdn-images-1.medium.com/max/2000/1*w1vIuGh7xfyLkUq_-odq4w.png) module definition context

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

### Check out the live execution

[![asciicast](https://asciinema.org/a/162255.png)](https://asciinema.org/a/162255)

## 6\|&gt; Finding the list of bound values in iex

### binding

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

When `fun/3` is invoked with `:laughing`, `"time"` it prints:

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

### Check out the live execution

[![asciicast](https://asciinema.org/a/162257.png)](https://asciinema.org/a/162257)

## 5\|&gt; Fun with Char\_Lists

We can convert any integer to charlist using `Integer.char_list` . It can be used in two ways either passing the base the base value or not passing.

Let’s try with base and with out base.

### without base

```elixir
    iex> Integer.to_charlist(882681651)
    '882681651'
```

### with base

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

## 4\|&gt; Extracting Process Information

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

### Extracting whole information

```elixir
    iex> Process.info pid
```

![image](https://cdn-images-1.medium.com/max/2000/1*0GfghGYjk_lqKuggf23sOA.png)

## 3\|&gt; Forcing Keys in a Map parameter in functions

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

### Execution screenshot

![image](https://cdn-images-1.medium.com/max/2000/1*Me9434rZFV1lEmwZd2toNA.png)

If you observer the screenshot, we tried to access the function by sending a map with single key parameter where it is not allowed then with two keys map and the keys are exactly pattern matched where it is allowed to use the function then evenutally tried with more keys still it worked.

> Thanks to pattern matching.

## 2\|&gt; Universal Code base formatter

Code formatting is easy now with the help of mix task `mix format` . It will take care of all you care about cleaning and refactoring as well. It can assure a clean code base or simply universal code base which maintains some useful standards. This really saves a lot of time.

```elixir
    mix format filename
```

To know more about the codebase formatting, check out my recent article on

### \[Code Formatter The Big Feature in Elixir

1.6\]\([https://medium.com/blackode/code-formatter-the-big-feature-in-elixir-v1-6-0-f6572061a4ba](https://medium.com/blackode/code-formatter-the-big-feature-in-elixir-v1-6-0-f6572061a4ba)\)

which explains all about using `mix format` task and its configuration in detail with screen shots of vivid examples.

## 1\|&gt; Alias multiple modules in one line

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

## Happy Coding!

[Prev](part6.md) [Next](part8.md)
