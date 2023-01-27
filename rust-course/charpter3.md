## 生命周期
```rust
{
    let r;                // ---------+-- 'a
                          //          |
    {                     //          |
        let x = 5;        // -+-- 'b  |
        r = &x;           //  |       |
    }                     // -+       |
                          //          |
    println!("r: {}", r); //          |
}                         // ---------+
```
`rust`会检查变量的存活时间，对于变量`r`，其生命周期要长于`x`。`Rust`编译器会拒绝长生命周期变量被短生命周期的值的赋值行为。因为潜在`drop`的风险。

需要让编译器知道生命周期的具体信息，否则会因为混淆情况的存在而无法顺利编译。
```rust
// first和second parameter以及返回值至少生命周期有'a这么长。因此'a是相对较小的那一个。
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```
生命周期存在三大规则：
1. 每一个引用参数都会获得独自的生命周期

例如一个引用参数的函数就有一个生命周期标注: fn foo<'a>(x: &'a i32)，两个引用参数的有两个生命周期标注:fn foo<'a, 'b>(x: &'a i32, y: &'b i32), 依此类推。

2. 若只有一个输入生命周期(函数参数中只有一个引用类型)，那么该生命周期会被赋给所有的输出生命周期，也就是所有返回值的生命周期都等于该输入生命周期

例如函数 fn foo(x: &i32) -> &i32，x 参数的生命周期会被自动赋给返回值 &i32，因此该函数等同于 fn foo<'a>(x: &'a i32) -> &'a i32

3. 若存在多个输入生命周期，且其中一个是 &self 或 &mut self，则 &self 的生命周期被赋给所有的输出生命周期

拥有 &self 形式的参数，说明该函数是一个 方法，该规则让方法的使用便利度大幅提升。
## 迭代器
函数式编程中的一个很重要的组成部分，其中的next方法在python中也有较多的使用。
```rust
fn main() {
    let arr = [1, 2, 3];
    let mut arr_iter = arr.into_iter();

    assert_eq!(arr_iter.next(), Some(1));
    assert_eq!(arr_iter.next(), Some(2));
    assert_eq!(arr_iter.next(), Some(3));
    assert_eq!(arr_iter.next(), None);
}
```
手搓for loop进行循环的方法
```rust
#![allow(unused)]
fn main() {
let values = vec![1, 2, 3];

{
    let result = match IntoIterator::into_iter(values) {
        mut iter => loop {
            match iter.next() {
                Some(x) => { println!("{}", x); },
                None => break,
            }
        },
    };
    result
}
}
```
迭代器一般有三种使用方式，如下所示：
1. into_iter 会夺走所有权
2. iter 是借用
3. iter_mut 是可变借用

一些有趣的函数：
`collect(), iter(), fold()`

这一部分的Rustlings习题可能需要对函数式编程比较熟悉才比较好写。
## 多线程：
## 智能指针：
Box指针指向的东西位于堆上。为了应对递归类型的数据结构大小不可知的情况，采用`Box`进行管理，链接堆上的数据。
```rust
enum List {
    Cons(i32, Box<List>), // 至少在栈上的大小是已知的了。
    Nil,
}
```
Rc指针用于单线程。如果我们希望在堆上分配一个对象供程序的多个部分使用且无法确定哪个部分最后一个结束时，就可以使用 Rc 成为数据值的所有者。
```rust
use std::rc::Rc;
fn main() {
    let a = Rc::new(String::from("hello, world"));
    let b = Rc::clone(&a); // 浅拷贝，仅发生在栈上的拷贝。

    assert_eq!(2, Rc::strong_count(&a));
    assert_eq!(Rc::strong_count(&a), Rc::strong_count(&b))
}
```
逆操作则是利用drop主动释放，如果不是这一类型的指针，rust编译器会自动给其添上drop释放之。Rc指针的应用例子：多个工具都有一个主人。

为了实现多线程的共享，采用aRc指针帮忙。

```rust
use std::sync::Arc;
use std::thread;

fn main() {
    let s = Arc::new(String::from("多线程漫游者"));
    for _ in 0..10 {
        let s = Arc::clone(&s);
        let handle = thread::spawn(move || {
           println!("{}", s)
        });
    }
}
```
它的API和Rc是一致的，所以使用起来也就很方便哈哈。

Cow指针：clone-on-write functionality