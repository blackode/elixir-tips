# Part 3

## 1. Functions as Guard Clauses

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

## Usage

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

## 2. Finding the presence of Sub-String

Using `=~` operator we can find whether the **right** sub-string present in **left** string or not..

```elixir
iex> "blackode" =~ "kode" 
true  
iex> "blackode" =~ "medium" 
false  
iex> "blackode" =~ "" 
true
```

## 3. Finding whether Module is loaded or not

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

## 4. Binary to Capital Atom

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

## 5. Pattern match \[ vs \] destructure.

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

## 6. Data decoration \[ inspect with :label \] option

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

## 7. Anonymous functions to pipe

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

## 8. Retrieve Character Integer Codepoints — ?

We can use `?` operator to retrieve character integer codepoints.

```elixir
iex> ?a
97
iex> ?#
35
```

The following two tips are mostly useful for beginners…

## 9. Subtraction over Lists

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

## 10. Using Previous results in IEx

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

