---
title: "Rust中的array、vector和slice 【译】"
categories: [ "Rust" ]
tags: [ "slice","array","rust","vector" ]
draft: false
slug: "array-vector-and-slice-in-rust"
date: "2020-10-29 20:29:00"
---

## 引言（Introduction）

在本文中，我将会介绍 Rust 中的 array、vector 和 slice。有 C 和 C++编程经验的程序员应该已经熟悉 array 和 vector，但因 Rust 致力于安全性（safety），所以与不安全的同类语言相比仍有一些区别。另外，slice 是一个全新且非常有用的概念。


<!--more-->


## Array

Array 是初学者最先接触的数据类型之一。一个 array 是一组相同类型的数据集合，这些数据位于连续的内存块中。例如，如果你像下面这样分配一个数组：

```
let array: [i32; 4] = [42, 10, 5, 2];
```

接着，所有的`i32`整数在栈上紧挨着彼此被分配：

![array-layout.svg][1]
在 Rust 中，array 的大小（size）是类型的一部分。例如，下面的代码将无法编译：

```
//error:expected an array with a fixed size of 4 elements,
//found one with 3 elements
let array: [i32; 4] = [0, 1, 2];
```

Rust 的严谨性避免了像 C/C++中的数组到指针的衰变问题：

```c++
//C++ code
#include <iostream>

using namespace std;

//Looks can be deceiving: arr is not a pointer
//to an array of 5 integers. It has decayed to
//a pointer to an integer.
void print_array_size(int (*arr)[5]) {
    //prints 8 (the size of a pointer)
    cout << "Array size in print_array_size function: " << sizeof(arr) << endl;
}

int main()
{
    int arr[5] = {1, 2, 3, 4, 5};
    //prints 20 (size of 5 4-byte integers)
    cout << "Array size in main function: " << sizeof(arr) << endl;
    print_array_size(&arr);
    return 0;
}
```

`print_array_size`函数打印出了 8 而不是期望的 20（5 个整数，每个整数 4 字节），因为`arr`已经从一个指向包含 5 个整数的数组（array）的指针衰退为指向一个整数的指针。相似的代码在 Rust 中能够正确运行：

```rust
use std::mem::size_of_val;

fn print_array_size(arr: [i32; 5]) {
    //prints 20
    println!("Array size in print_array_size function: {}", size_of_val(&arr));
}

fn main() {
    let arr: [i32; 5] = [1, 2, 3, 4, 5];
    //print 20
    println!("Array size in main function: {}", size_of_val(&arr));
    print_array_size(arr);
}
```

C/C++和 Rust 在 array 上的另一个区别是，在 Rust 中访问元素会进行边界检查。例如，在下面的 C++代码中，我们试图访问一个大小为 3 的 array 中的第 5 个元素，这导致了**未定义行为**[1]：

```c++
#include <iostream>

using namespace std;

int main()
{
    int arr[3] = {1, 2, 3};
    const auto index = 5;
    //arr[index] is undefined behaviour
    cout << "Integer at index " << index << ": " << arr[index] << endl;
    return 0;
}
```

而类似的代码在 Rust 中则会 panic：

```rust
fn main() {
    let arr: [i32; 3] = [1, 2, 3];
    let index = 5;
    //arr[index] panics with the following message:
    //index out of bounds: the len is 3 but the index is 5
    println!("Integer at index {}: {}", index, arr[index]);
}
```

你可能想知道，Rust 版本的代码怎么就比 C++版本的代码好了？因为 C++版本的代码表现出未定义行为，它给了编译器一个不受限制的许可，允许编译器以优化的名义来做任何事。在最糟糕的情况下，这可能会把信息泄露给攻击者。

与之相对，Rust 版本的代码总是会 panic。此外，因为进程由于 panic 而终止，程序员更有可能注意并修复这个 bug。相反，C++把问题掩盖起来并且进程可以像什么事都没发生一样继续运行。比起 C/C++的未定义行为，我宁愿使用 Rust 的 panic。

## Vector

Array 最大的一个限制是它的固定大小。与之相对，vector 可以在运行时扩容：

```rust
fn main() {
    //There are three elements in the vector initially
    let mut v: Vec<i32> = vec![1, 2, 3];
    //prints 3
    println!("v has {} elements", v.len());
    //but you can add more at runtime
    v.push(4);
    v.push(5);
    //prints 5
    println!("v has {} elements", v.len());
}
```

Vector 是如何做到在运行时扩容的呢？在其内部，vector 把所有的元素放在一个分配在堆（heap）上的 array 上。当一个新元素被 push 进来时，vector 检查 array 是否有足够的剩余空间。如果空间不足，vector 就分配一个更大的 array，将所有的元素都拷贝到这个新的 array 中，然后释放旧的 array。这可以在下面的代码中验证：

```
fn main() {
    let mut v: Vec<i32> = vec![1, 2, 3, 4];
    //prints 4
    println!("v's capacity is {}", v.capacity());
    println!("Address of v's first element: {:p}", &v[0]);//{:p} prints the address
    v.push(5);
    //prints 8
    println!("v's capacity is {}", v.capacity());
    println!("Address of v's first element: {:p}", &v[0]);
}
```

最开始，`v`内部的 array 容量（capacity）为 4：

![vec-capacity-4.svg][2]

接着，一个新元素被 push 到 vector 中，这使得 vector 把所有元素拷贝到一个新的容量为 8 的内部 array 中：

![vec-capacity-8.svg][3]

上面这段代码还会打印出，在放入一个元素之前和放入之后，vector 里的 array 中的第一个元素的地址。这两个地址会互不相同。地址的变化清楚地证明了其幕后分配了一个容量为 8 的新 array。

> 如果你在 vector 中 push 进了一个元素但是却没有看到不同的地址，这可能是因为原始的 buffer 尾部还有足够的空间，因此新旧 buffer 拥有相同的起始地址。尝试 push 更多的元素，你就会看到不同的地址。阅读 C 的库函数`realloc`来理解这是如何运作的。

## Slice

Slice 就像一个 array 或 vector 的临时视图（temporary views）。例如，如果你有一个 array 如下:

```
let arr: [i32; 4] = [10, 20, 30, 40];
```

你可以像下面这样，创建一个包含第二个和第三个元素的 slice：

```
let s = &arr[1..3];
```

`[1..3]`语法创建一个区间，从索引 1（包含）到 3（不包含）（译注：即左闭右开）。如果你省略区间的第一个数（`[..3]`），它会默认从 0 开始，如果你省略最后一个数（`[1..]`），它会默认为到数组的长度。如果你打印 slice `[1..3]`中的元素，你将会得到 20 和 30：

```
//prints 20
println!("First element in slice: {:}", s[0]);
//prints 30
println!("Second element in slice: {:}", s[1]);
```

但是如果你尝试访问 slice 范围之外的元素，它会 panic：

```
//panics: index out of bounds
println!("Third element in slice: {:}", s[2]);
```

但， slice 是如何知道它只有两个元素呢？这是因为 slice 不是一个简单的指向 array 的指针，它还在一个额外的长度字段中标记了 slice 中的元素数量。

> 除了指向对象的地址外，还带有某些额外数据的指针称为胖指针（fat pointer）。Slice 不是 Rust 中唯一的胖指针类型。还有例如，trait 对象，除了指向对象的指针外，还有一个虚表指针。

例如，你可以创建一个 vector 的 slice：

```
let v: Vec<i32> = vec![1, 2, 3, 4];
let s = &v[1..3];
```

除了有一个指针指向`v`的 buffer 中的第二个元素之外，`s`还有一个长度为 8 字节的字段（length），其值为 2：

![slice-of-vec.svg][4]

长度字段（length）的存在可以通过下面的代码来看到，在这段代码中，一个 slice（`&[i32]`）的大小为 16 字节（8 字节为 buffer pointer，8 字节为长度字段）：

```rust
use std::mem::size_of;

fn main() {
    //prints 8
    println!("Size of a reference to an i32: {:}", size_of::<&i32>());
    //print 16
    println!("Size of a slice: {:}", size_of::<&[i32]>());
}
```

Array 的 slice 也是类似，但是 buffer pointer 不是指向堆（heap）上的 buffer，而是指向栈（stack）上的 array。

因为 slice 借用自底层的数据结构，所有的常见借用规则都在此适用。例如，下面的代码会被编译器拒绝：

```rust
fn main() {
    let mut v: Vec<i32> = vec![1, 2, 3, 4];
    let s = &v[..];
    v.push(5);
    println!("First element in slice: {:}", s[0]);
}
```

为什么呢？因为当 slice 被创建时，它指向 vector 内部 buffer 的第一个元素并且当一个新元素被 push 进 vector 时，它（指 vector）会分配一个新的 buffer 并且旧的 buffer 会被释放。这就导致 slice 指向了一个无效的内存地址，如果访问这个无效地址则会导致未定义行为。Rust 再一次从灾难中拯救了你。

> 因为 array 和 vector 都可以创建 slice，它们（指 slice）是非常强大的抽象。因此，对于函数中的参数，默认的选择应该是接收一个 slice 而不是一个 array 或 vector。事实上，很多函数，像`len`、`is_empty`等，都是作用于 slice 而非 vector 或 array。

## 总结（Conclusion）

Array 和 vector 作为新手程序员学习过程中最先接触的数据类型之一，Rust 支持它们也不足为奇。但是，正如我们所见，Rust 的安全性保证不允许程序员对这些基础数据类型进行滥用。Slice 是 Rust 中的一个新概念，但是因为它们（指 slice）是这样一个给力的抽象，你会发现它们在任意的 Rust 代码库里都被普遍使用。

原文链接：https://hashrust.com/blog/arrays-vectors-and-slices-in-rust/
原文标题：Arrays, vectors and slices in Rust
翻译：Praying


  [1]: https://imgs.gnux.cn/usr/uploads/2020/10/3566294662.svg
  [2]: https://imgs.gnux.cn/usr/uploads/2020/10/3449431351.svg
  [3]: https://imgs.gnux.cn/usr/uploads/2020/10/3175240348.svg
  [4]: https://imgs.gnux.cn/usr/uploads/2020/10/1597653078.svg