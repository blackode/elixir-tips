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
### 3. get_in /Acess.all()
We all know that the get_in function is used to extract the key which is deeper inside the map by providing the list with keys like a following way…        

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
**Note:** If the key is not present in map, then it returns nil 
**Warning:** When you use the `get_in` along with `Access.all()` , as the first value in the list of keys like above, the users datatype should be list. If you pass the map, it returns the error.
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

However, you can change the position of the Access.all() in the list. But the before key should return the list. Check the following lines of code.
##### Deep Dive
We can also use the Access.all() with functions like update_in, get_and_update_in, etc..               
For instance, given a user with a list of books, here is how to deeply
traverse the map and convert all book names to uppercase:

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
##### General Usage
```elixir
iex> for x <- [1, 2, 3, 4], do: x + 1
[2, 3, 4, 5]
# that is how we use in general lets think out of the box
```
##### With filters

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
By default the functions with _ are not imported. However, you can do that by importing them with `:only` explicitly.  
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
##### String Interpolation
```elixir
iex> mystring = "#{str1}#{str2}"
helloblackode
```

##### Using <> operator
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
