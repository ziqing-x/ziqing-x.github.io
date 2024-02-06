---
title: rust 闭包之什么是闭包？
tags: [lamda, closure, 闭包]
category: rust
image:
    path: /assets/img/headers/lambda.webp
---

在 rust 语言中，闭包是可以捕获周围作用域中变量的匿名函数。闭包的语法和其他语言的 lambda 表达式类似，它有能力捕获上下文中的变量，这使得它非常适合用作回调函数或临时的内联函数。 
闭包通常使用一对垂直线 || 来定义，这些线内是闭包的参数，后面跟着闭包体。闭包可以捕获变量的方式有三种： 
- 1. 通过引用捕获（不可变借用），使用 Fn 特征。 
- 2. 通过可变引用捕获（可变借用），使用 FnMut 特征。 
- 3. 通过值捕获（移动所有权），使用 FnOnce 特征。 
闭包在 rust 中的使用非常广泛，尤其是在迭代器、高阶函数和异步编程中。闭包可以使代码更加简洁。下面是几种闭包常用的常用示例：

## 常见示例

```rust
fn main() {
    let a = 10;
    let b = 15;
    let add = || a + b;
    println!("{}", add());
}
```

## 使用单个闭包作为参数传递且闭包无返回值
```rust
fn function<F: Fn()>(f: F) {
    f();
}

fn main() {
    let a = 10;
    let b = 20;
    let print = || println!("{a}, {b}");
    function(print);
}
```
等效于
```rust
fn function<F>(f: F)
where
    F: Fn(),
{
    f();
}

fn main() {
    let a = 10;
    let b = 20;
    let print = || println!("{a}, {b}");
    function(print);
}
```

## 使用单个闭包作为参数传递且闭包有返回值
```rust
fn function<F: Fn() -> i32>(f: F) -> i32 {
    f()
}

fn main() {
    let a = 10;
    let b = 20;
    let add = || a + b;
    println!("{}", function(add));
}
```
等效于
```rust
fn function<F>(f: F) -> i32
where
    F: Fn() -> i32,
{
    f()
}

fn main() {
    let a = 10;
    let b = 20;
    let add = || a + b;
    println!("{}", function(add));
}
```

## 使用多个闭包作为参数传递且闭包有返回值
```rust
fn add<F1: Fn() -> i32, F2: Fn() ->i32>(f1: F1, f2: F2) -> i32 {
    f1() + f2()
}

fn main() {
    let a = 10;
    let b = 20;
    let add = || a + b;
    println!("{}", crate::add(add, add));
}
```
这种写法有个问题，如果闭包参数太多，看起来很不爽，所以参与另一种约束方式会更好，如下
```rust
fn add<F1, F2>(f1: F1, f2: F2) -> i32
where
    F1: Fn() -> i32,
    F2: Fn() -> i32,
{
    f1() + f2()
}

fn main() {
    let a = 10;
    let b = 20;
    let add = || a + b;
    println!("{}", crate::add(add, add));
}
```

## 使用多个闭包作为参数传递且闭包有参数和返回值
```rust
fn add<F1: Fn(i32, i32) -> i32, F2: Fn(i32, i32) ->i32>(f1: F1, f2: F2, a: i32, b: i32) -> i32 {
    f1(a, b) + f2(a, b)
}

fn main() {
    let a = 10;
    let b = 20;
    let add = |a: i32, b: i32| a + b;
    println!("{}", crate::add(add, add, a, b));
}
```
就问你这个写法看着难不难受？所以还是乖乖用下面这种约束方式吧，简洁美观

```rust
fn add<F1, F2>(f1: F1, f2: F2, a: i32, b: i32) -> i32
where
    F1: Fn(i32, i32) -> i32,
    F2: Fn(i32, i32) -> i32,
{
    f1(a, b) + f2(a, b)
}

fn main() {
    let a = 10;
    let b = 20;
    let add = |a: i32, b: i32| a + b;
    println!("{}", crate::add(add, add, a, b));
}
```