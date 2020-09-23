---
title: Rust 学习指南 - Rust 基础
date: 2019-09-17 19:54:00
updated: 2019-09-19 17:13:12
tags: 
- const
- let
- 块级作用
- JS基础增强
categories: 
- js

---
## 环境准备

安装Rust的方式有很多种，最简单的方式是使用官方提供的[rustup](https://rustup.rs/)工具安装。如果是Linux或者Mac直接执行`curl https://sh.rustup.rs -sSf | sh`即可，如果是windows，MSVC架构 需要安装Visual C++ Build Tools 2013 或者高于2013的版本。（**注意**：笔者windows下已切换为gnu架构） 使用rustup工具可以很容易的在**stable**，**beta**，**nightly**版本中间进行切换，同时可以方便的升级rust。rustup会安装rustc，cargo，rsutup和其他的基本工具到Cargo的bin目录下，如果使用Linux或者Mac，需要将`$HOME/.cargo/bin` 加入到PATH中，如果使用window，则需要将`%USERPROFILE%.cargo\bin`添加的path中。以后使用`cargo install` 安装工具的时候也会默认安装在这个目录。更多信息可以参考[rustup](https://github.com/rust-lang/rustup.rs)项目。 安装成功之后可以通过`rustc --version` 或者`rustc -v` 验证安装是否成功。

## Hello World


<!--more-->


```rust
fn main(){
	println!("Hello world!");
}
```

`fn` 表示声明函数。`main`函数是Rust程序的入口函数。`println!`表示一个宏，会对输入进行检查，检查无误会输出到控制台。rust程序以 .rs 结尾，将上述代码保存到`main.rs` 文件中，然后使用 `rustc main.rs` 编译。编译无误会生成一个`main`的可执行文件，直接执行文件就可以看到控制台输出`Hello world!`。 更多println!的用法如下

```rust
fn main() {
    println!("{}, {}!", "Hello", "world"); // Hello, world!
    println!("{0}, {1}!", "Hello", "world"); // Hello, world!
    println!("{greeting}, {name}!", greeting="Hello", name="world"); // Hello, world!
    println!("{:?}", [1,2,3]); // [1, 2, 3]
    println!("{:#?}", [1,2,3]);
    /*
        [
            1,
            2,
            3
        ]
    */
    // format! 宏用于存储格式化字符串
    let x = format!("{}, {}!", "Hello", "world");
    println!("{}", x); // Hello, world!
    let y = String::from("Hello, ") + "world!";
    println!("{}", y); // Hello, world!
}
```

## Cargo, Crates ，Rust项目结构

cargo是rust默认的包管理工具，主要作用如下：

- 创建项目 `cargo new`

- 更新项目依赖 `cargo update`

- 编译项目 `cargo build`

- 编译运行项目 `cargo run`

- 运行测试 `cargo test`

- 生成文档 `cargo doc` 
- 其他的功能： 
	- cargo login 登录crates.io 
	- cargo package 创建本地包 
	- cargo publish 将本地包上传到crates.io中 crate既可以是一个可执行文件，也可以是一个库，换句话说就是可以是一个二进制的crate，也可以是一个库crate。 
	- cargo new crate_name --bin 创建一个可执行的 craete,文件目录如下。

```bash
├── Cargo.toml
└── src
└── main.rs
```

  `cargo new crate_name --lib` 或者直接使用`cargo new crate_name` 创建一个库。文件目录如下：

  ```bash
  ├── Cargo.toml
  └── src
     └── lib.rs
  ```

  - Cargo.toml: 是配置文件，可以将配置信息，依赖信息配置到`Cargo.toml`中。
  - src: 文件夹是存放rust源码的目录 每个crate都有一个默认的入口文件，main.rs 是可执行crate的入口文件，lib.rs是库crate的入口文件。 使用cargo build 默认生成的是debug版本，保存

  ```
  target/debug/
  ```

  目录下，如果要生成release版本，则需要使用

  ```
  cargo build --release
  ```

会在target目录下生成release目录。 一个典型的cargo项目如下：

```bash
├── Cargo.lock
├── Cargo.toml
├── benches
│   └── large-input.rs
├── examples
│   └── simple.rs
├── src
│   ├── bin
│   │   └── another_executable.rs
│   ├── lib.rs
│   └── main.rs
└── tests
    └── some-integration-tests.rs
```

源文件保存在src文件夹下。 默认的库文件是src/lib.rs。 默认的可执行文件入口是src/main.rs。 其他可执行文件可以放在src/bin/*.rs 中。 测试文件可以放在tests文件夹下。 示例文件可以放在examples 文件夹下。 性能测试文件可以放在 benches文件夹下。

## 注释和文档

rust支持两种注释格式，

```bash
// Line comments
/* Block comments */
```

文档注释支持Markdown格式，通过cargo doc 命令可以从文档注释中生成HTML文档。

```bash
/// This module contains tests
mod test {
    // ...
}
mod test {
    //! This module contains tests
    // ...
}
```

注意，只有在crate和module级别的文档的时候使用`//!`，其他情况下使用`///`形式。

## 变量绑定，常量，静态变量

在rust中，变量默认是不可变的，所以在rust中将变量赋值叫做变量绑定。如果变量需要被改变，可以使用mut 关键字使其可变。 rust是静态类型语言，在编译期间检查类型是否正确。但是在声明变量绑定的时候并不需要显示声明其类型，rust编译器可以通过上下文推断出变量的类型，但是对于常量和静态变量，则必须声明其类型。

### 变量绑定

```rust
let a = true;
let b: bool = true;
let (x, y) = (1,2)
let mut z = 5;
z = 6;
```

### 常量

```rust
const N:i32 = 5;
```

### 静态变量

```rust
static N:i32 = 5;
```

关键字let用于变量绑定表达式。rust中可以将变量名绑定到一个值或者函数。关键字const用来定义一个常量。它在程序的整个生命周期中都会存在，但是固定内存地址。关键字static用来定义一个静态变量。

## 函数

函数通过 fn 定义，函数默认返回() 如果需要返回其他类型可以使用 -> 指定返回类型

```rust
fn main() {
    let b = plus_one;
    println!("b: {}", b(3)); // b: 4
    let c = b(5);
    println!("c: {}", c);  // c: 6
    
    let b: fn(i32) -> i32 = plus_one; // 会覆盖之前定义的b
    println!("b function: {}", b(3)); // b function: 4
    let c = b(5);
    println!("c function: {}", c);  // c function: 6

    let b = plus_one;
    println!("b function ref: {}", plus_three(b, 2)); // b function ref: 4
}


fn print_sum(a: i8, b: i8) {
    println!("sum is {}", a + b);
}

fn plus_one(a: i32) -> i32 {
    a + 1 // 不加分号，等同于 return a + 1
}

fn plus_two(a: i32) -> i32 {
    return a + 2;
}

fn plus_three(b: fn(i32) -> i32, x: i32) -> i32 {
    b(b(x))
}
```

## 原生类型

### bool

```rust
let x = true;
let y: bool = false;
```

### char

char类型是一个unicode字符

```rust
let x = 'x';
let y = 'smiling';
```

### 数值类型

i8,i16,i32,i64，分别表示8位，16位，32位，64位有符号整数 u8,u16,u32,u64,分别表示8位，16位，32位，64位无符号整数，其中u8相当于其他语言的byte f32，f64，分别表示32位，64位的浮点数 isize，usize表示的是指针大小，其位数与具体平台相关。

### 数组

```rust
let aa = [1, 2, 3]; // a[0] = 1, a[1] = 2, a[2] = 3
let mut b = [1, 2, 3];
let cc: [i32; 3] = [1, 2, 3]; //[Type; NO of elements]
let ee: [i32; 0] = []; //empty array
println!("{:?}", aa); //[1, 2, 3]
println!("{:#?}", aa);
```

### 元组

rust中元组的概念和python很像，即以小括号括起来。

```rust
let b: (i32, f64) = (1, 1.5);
println!("b:{:?}", b);  // b:(1, 1.5)
println!("b: {:#?}", b);
/*
b: (
1,
1.5,
)
*/
let (c, d) = b; 
println!("c = {:?}, d = {:?}", c, d); // c = 1, d = 1.5

let (e, _, _, _, f) = a; 
println!("{:?}, {:?}", e, a);  // 1, (1, 1.5, true, 'a', "Hello, world!")

let g = (0,); //single-element tuple
println!("g:{:?}", g); // g:(0,)

let h = (b, (2, 4), 5); 
println!("h:{:?}", h); // h:((1, 1.5), (2, 4), 5)
```

### 切片

```rust
let a: [i32; 4] = [1, 2, 3, 4];//Parent Array
let b: &[i32] = &a; //Slicing whole array
let c = &a[0..4]; // From 0th position to 4th(excluding)
let d = &a[..]; //Slicing whole array
let e = &a[1..3]; //[2, 3]
let e = &a[1..]; //[2, 3, 4]
let e = &a[..3]; //[1, 2, 3]
```

### 字符串 str

str是一个UTF-8 编码的字符串切片

```Java
let a = "Hello, world."; //a: &'static str
let b: &str = "こんにちは, 世界!";
```

### 函数

在rust中函数也可以是一种类型

```rust
fn plus_one(a: i32) -> i32 {
    a + 1
}
let b = plus_one;
let c = b(5); //6
let b: fn(i32) -> i32 = plus_one; //same, with type inference
let c = b(5); //6
let b = plus_one;
fn plus_two(b: fn(i32) -> i32, x: i32) -> i32 {
    b(b(x))
}
println!("{}", plus_two(b, 2)); //4
```

## 操作符

### 算数操作符 + - * / %

```rust
let a = 5;
let b = a + 1; //6
let c = a - 1; //4
let d = a * 2; //10
let e = a / 2; // 2 not 2.5
let f = a % 2; //1
let g = 5.0 / 2.0; //2.5
```

### 比较操作符 == != < > <= >=

```rust
let a = 1;
let b = 2;
let c = a == b; //false
let d = a != b; //true
let e = a < b; //true
let f = a > b; //false
let g = a <= a; //true
let h = a >= a; //true
// 
let i = true > false; //true
let j = 'a' > 'A'; //true
```

### 逻辑操作符：! && ||

```rust
let a = true;
let b = false;
let c = !a; //false
let d = a && b; //false
let e = a || b; //true
let a = !-2; //1
let b = !-1; //0
let c = !0; //-1
let d = !1; //-2
```

### 位操作符 & | ^ << >>

```rust
let a = 1;
let b = 2;
let c = a & b; //0  (01 && 10 -> 00)
let d = a | b; //3  (01 || 10 -> 11)
let e = a ^ b; //3  (01 != 10 -> 11)
let f = a << b; //4  (add 2 positions to the end -> '01'+'00' -> 100)
let g = a >> a; //0  (remove 2 positions from the end -> o̶1̶ -> 0)
```

### 组合操作符

```Java
let mut a = 2;
a += 5; //2 + 5 = 7
a -= 2; //7 - 2 = 5
a *= 5; //5 * 5 = 25
a /= 2; //25 / 2 = 12 not 12.5
a %= 5; //12 % 5 = 2
a &= 2; //10 && 10 -> 10 -> 2
a |= 5; //010 || 101 -> 111 -> 7
a ^= 2; //111 != 010 -> 101 -> 5
a <<= 1; //'101'+'0' -> 1010 -> 10
a >>= 2; //101̶0̶ -> 10 -> 2
```

注意 rust中没有++ -- 操作符，如果要++或者-- 可以使用 a+=1 a-=1 代替。

### 类型转换 as

```Java
let a = 15;
let b = (a as f64) / 2.0; //7.5
```

rust中所有的类型转换必须显示表示。

## 控制流

### if else

```rust
let team_size = 7;
if team_size < 5 {
    println!("Small");
} else if team_size < 10 {
    println!("Medium");
} else {
    println!("Large");
}
// partially refactored code
let team_size = 7;
let team_size_in_text;
if team_size < 5 {
    team_size_in_text = "Small";
} else if team_size < 10 {
    team_size_in_text = "Medium";
} else {
    team_size_in_text = "Large";
}
println!("Current team size : {}", team_size_in_text);
//optimistic code
let team_size = 7;
let team_size_in_text = if team_size < 5 {
    "Small" //no ;
} else if team_size < 10 {
    "Medium"
} else {
    "Large"
};
println!("Current team size : {}", team_size_in_text);
let is_below_eighteen = if team_size < 18 { true } else { false };

/**
 *  Medium
    Current team size : Medium
    Current team size : Medium
    */
```

### match

```rust
println!("{}", tshirt_size); // L
let is_allowed = false;
let list_type = match is_allowed {
    true => "Full",
    false => "Restricted",
};
println!("{}", list_type); // Restricted
let marks_paper_a: u8 = 25;
let marks_paper_b: u8 = 30;
let output = match (marks_paper_a, marks_paper_b) {
    (50, 50) => "Full marks for both papers",
    (50, _) => "Full marks for paper A",
    (_, 50) => "Full marks for paper B",
    (x, y) if x > 25 && y > 25 => "Good",
    (_, _) => "Work hard",
};
println!("{}", output); // Work hard
/**
 * 
 *  L
    Restricted
    Work hard
    */
```

### while

```rust
let mut a = 1;
while a <= 10 {
    println!("Current value : {}", a);
    a += 1; //no ++ or -- in Rust
}
// Usage of break and continue
let mut b = 0;
while b < 5 {
    if b == 0 {
        println!("Skip value : {}", b);
        b += 1;
        continue;
    } else if b == 2 {
        println!("Break At : {}", b);
        break;
    }
    println!("Current value : {}", b);
    b += 1;
}
// Outer break
let mut c1 = 1;
'outer_while: while c1 < 6 {
    //set label outer_while
    let mut c2 = 1;
    'inner_while: while c2 < 6 {
        println!("Current Value : [{}][{}]", c1, c2);
        if c1 == 2 && c2 == 2 {
            break 'outer_while;
        } //kill outer_while
        c2 += 1;
    }
    c1 += 1;
}


/**
 * 
Current value : 1
Current value : 2
Current value : 3
Current value : 4
Current value : 5
Current value : 6
Current value : 7
Current value : 8
Current value : 9
Current value : 10
Skip value : 0
Current value : 1
Break At : 2
Current Value : [1][1]
Current Value : [1][2]
Current Value : [1][3]
Current Value : [1][4]
Current Value : [1][5]
Current Value : [2][1]
Current Value : [2][2]
 */
```

### loop

```rust
loop {
    println!("Loop forever!");
}
// Usage of break and continue
let mut a = 0;
loop {
    if a == 0 {
        println!("Skip Value : {}", a);
        a += 1;
        continue;
    } else if a == 2 {
        println!("Break At : {}", a);
        break;
    }
    println!("Current Value : {}", a);
    a += 1;
}
// Outer break

let mut b1 = 1;
'outer_loop: loop {
    //set label outer_loop
    let mut b2 = 1;
    'inner_loop: loop {
        println!("Current Value : [{}][{}]", b1, b2);
        if b1 == 2 && b2 == 2 {
            break 'outer_loop; // kill outer_loop
        } else if b2 == 5 {
            break;
        }
        b2 += 1;
    }
    b1 += 1;
}

/**
     * 
Loop forever!
Loop forever!
Loop forever!
...
...
...
 */
```

loop 是一个无限循环，和while true的区别就在与编译分析。

### for

```rust
for a in 0..10 {
        //(a = 0; a <10; a++) // 0 to 10(exclusive)
        println!("Current value : {}", a);
    }
    // Usage of break and continue
    for b in 0..6 {
        if b == 0 {
            println!("Skip Value : {}", b);
            continue;
        } else if b == 2 {
            println!("Break At : {}", b);
            break;
        }
        println!("Current value : {}", b);
    }
    // Outer break
    'outer_for: for c1 in 1..6 {
        //set label outer_for
        'inner_for: for c2 in 1..6 {
            println!("Current Value : [{}][{}]", c1, c2);
            if c1 == 2 && c2 == 2 {
                break 'outer_for;
            } //kill outer_for
        }
/*
*
Current value : 0
Current value : 1
Current value : 2
Current value : 3
Current value : 4
Current value : 5
Current value : 6
Current value : 7
Current value : 8
Current value : 9
Skip Value : 0
Current value : 1
Break At : 2
Current Value : [1][1]
*/
```