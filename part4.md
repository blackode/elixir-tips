## Part 4

### 1. Running Multiple Mix Tasks

```elixir
mix do deps.get,compile
```

You can run multiple tasks by separating them with coma `,`

How ever you can also create aliases in your mix project in a file called `mix.exs` .

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

Don’t forget to add `,` at the end when you add this in the middle of `list` . 

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

Elixir stores the documentation inside the `bytecode` in a memory. You access the documentation with the help of `Code.get_docs/2` function . This means, the documentation accessed when it is required, but not when it is loaded in the virtual machine like  `iex`

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

You will see the output as `nil` when you are trying to access the **docs** of the module you have created so far. This is because, the  `bytecode` is not available in disk. 
In simple way `beam` file is not present. Lets do that...

Press `Ctrl+C` twice so you will come out of the shell and this time you run the command as

```elixir
$ elixirc test.ex
```

After running the command, you will see a file with name `Elixir.Test.beam` . Now the `bytecode` for the module `Test` is available in memory. Now you can access the documentation as follows...

```elixir
$ iex
iex> Code.get_docs Test, :moduledoc
{3, "This is the test module docs\n"}
```

The output is tuple with two elements. The first element is the line number of the documentation it starts and second element is the actual documentation in the binary form.

 You can read more about this function [here](https://hexdocs.pm/elixir/Code.html#get_docs/2)

### 3. Verbose Testing

When you go with `mix test` it will run all the tests defined and gives you the time of testing. However, you can see more verbose output like which test you are running with the `--trace` option like following…

```elixir
mix test --trace
```

It will list out the all tests with names you defined as `test "test_string"` here `test_string` is the name of the test.

### 4. Dynamic Function Name in Elixir Macro

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

### **5. Run Shell Commands in Elixir**

```elixir
System.cmd(command, args, options \\ [])
```

Executes the given command with args.

- **command** is expected to be an executable available in PATH unless an absolute path is given.
- **args** must be a list of binaries which the executable will receive as its
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

Checkout the documentation about `System` for more information and 
also check [Erlang os Module](http://www.erlang.org/doc/man/os.html).

### 6. Printing List as List without ASCII-Encoding

You know that when the list contains all the numbers as **ASCII** values, it will list out those values instead of the original numbers. Lets check that…

```elixir
iex> IO.inspect [97, 98]
'ab'
'ab'
```

The code point of `a` is `97` and `b` is `98` hence it is listing out them as `char_list`. However you can tell the `IO.inspect` to list them as list itself with option `char_lists: :as_list` .

```elixir
iex> IO.inspect [97, 98], charlists: :as_lists
[97, 98]
'ab'
```

Open `iex` and type `h Inspect.Opts`, you will see that Elixir does this kind of thing with other values as well, specifically **structs** and **binaries**.

### 7. Accessing file name and line number etc…

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

### 8. Creating Manual Pids

You can create the pid manually in Elixir with `pid` function. This comes with two flavors.

#### def pid(string)

Creates the pid from the string.

```elixir
iex> pid("0.21.32")
#PID<0.21.32>
```

#### def pid(a, b, c)

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

### 9. Replacing the String with global option

The `String.replace` function will replace the given the pattern with replacing pattern. By default, it replaces all the occurrences of the pattern. 
Lets check that…

```elixir
iex(1)> str = "hello@hi.com, blackode@medium.com"    
"hello@hi.com, blackode@medium.com"

iex(2)> String.replace str,"@","#"
"hello#hi.com, blackode#medium.com
```

The 
`String.replace str, "@", "#"`is same as 
`String.replace str, "@", "#", global: true`

But, if you want to replace only the first occurrence of the pattern, you need to pass the option `global: false` . So, it replaces only the first occurrence of `@` . Lets check that…

```elixir
iex(3)> String.replace str, "@", "#", global: false
"hello#hi.com, blackode@medium.com"
```

Here only first `@` is replaced with `#`.

### 10.Memory Usage

You can check the memory usage (in bytes) with `:erlang.memory` 

```elixir
iex(1)> :erlang.memory
[total: 16221568, processes: 4366128, processes_used: 4364992, system: 11855440,
 atom: 264529, atom_used: 250685, binary: 151192, code: 5845369, ets: 331768]
```

However, you can pass option like `:erlang.memory :atom` to get the memory usage of atoms.

```elixir
iex(2)> :erlang.memory :atom
264529
```
