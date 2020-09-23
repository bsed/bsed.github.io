---
title: Elixir基础学习: 控制语句
date: 2016-07-08 21:17:00
updated: 2016-07-09 21:26:57
tags: 
- elixir
- testing
categories: 
- elixir

---
# if 和 unless  

你之前可能遇到过 `if/2` 了，如果你使用过 Ruby，也会很熟悉 `unless`。它们在 Elixir 使用方式也一样，只不过它们在 Elixir 里是宏定义，不是语言本身的语句。你可以在 [Kernel](http://elixir-lang.org/docs/stable/elixir/#!Kernel.html) 模块 找到它们的实现。

需要注意的是，Elixir 中唯一为假的值是 nil 和 布尔值 false。
```erlang  
iex> if String.valid?("Hello") do  
...>   "Valid string!"  
...> else  
...>   "Invalid string."  
...> end  
"Valid string!"  

iex> if "a string value" do  
...>   "Truthy"  
...> end  
"Truthy"  
```


<!--more-->


`unless/2` 使用方法和 `if/2` 一样，不过只有当判断为否定才会继续执行：
```erlang  
iex> unless is_integer("hello") do  
...>   "Not an Int"  
...> end  
"Not an Int"  

# case  
如果需要匹配多个模式，我们可使用 case：
```erlang
iex> case {:ok, "Hello World"} do
...>   {:ok, result} -> result
...>   {:error} -> "Uh oh!"
...>   _ -> "Catch all"
...> end
"Hello World"
```
_ 变量是 `case` 语句重要的一项，如果没有 _，所有模式都无法匹配的时候会抛出异常：
```erlang
iex> case :even do
...>   :odd -> "Odd"
...> end
** (CaseClauseError) no case clause matching: :even

iex> case :even do
...>   :odd -> "Odd"
...>   _ -> "Not Odd"
...> end
"Not Odd"
```
可以把 `_` 想象成最后的 `else`，会匹配任何东西。因为 `case` 依赖模式匹配，所以之前所有的规则和限制在这里都适用。
如果你想匹配已经定义的变量，一定要使用 pin 操作符 `^`：
```erlang
iex> pie = 3.41
3.41
iex> case "cherry pie" do
...>   ^pie -> "Not so tasty"
...>   pie -> "I bet #{pie} is tasty"
...> end
"I bet cherry pie is tasty"
```
case 还有一个很酷的特性：它支持[卫兵表达式](http://elixir-lang.org/getting-started/case-cond-and-if.html#expressions-in-guard-clauses)：

这个例子直接取自 Elixir 官方指南的[上手教程](http://elixir-lang.org/getting-started/case-cond-and-if.html#case)
```erlang
iex> case {1, 2, 3} do
...>   {1, x, 3} when x > 0 ->
...>     "Will match"
...>   _ ->
...>     "Won't match"
...> end
"Will match"
```
参考官方的文档来看卫兵支持的表达式

# cond
当我们需要匹配条件而不是值的时候，可以使用 `cond` ，这和其他语言的 `eles if` 或者 `elif` 相似：

这个例子直接取自 Elixir 官方教程的 [上手指南](Getting Started](http://elixir-lang.org/getting-started/case-cond-and-if.html#cond)
```erlang
iex> cond do
...>   2 + 2 == 5 ->
...>     "This will not be true"
...>   2 * 2 == 3 ->
...>     "Nor this"
...>   1 + 1 == 2 ->
...>     "But this will"
...> end
"But this will"
```
和 case 一样，cond 语句如果没有任何匹配会抛出异常。我们可以定义一个 true 的条件放到最后，来处理这种意外：
```erlang
iex> cond do
...>   7 + 1 == 0 -> "Incorrect"
...>   true -> "Catch all"
...> end
"Catch all"
```