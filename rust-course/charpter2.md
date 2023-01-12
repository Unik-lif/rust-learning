# Charpter2： Rust基本知识整理
主要对照Rust Course内的信息做的知识整理，此外还包含个人写的题解。

<!-- vscode-markdown-toc -->
* 1. [变量与常量](#)
	* 1.1. [零碎知识点](#-1)
	* 1.2. [题解：](#-1)
* 2. [基本类型](#-1)
	* 2.1. [数值类型](#-1)
		* 2.1.1. [零碎知识点](#-1)
		* 2.1.2. [题解：](#-1)
	* 2.2. [字符、布尔、单元类型](#-1)
		* 2.2.1. [零碎知识点](#-1)
		* 2.2.2. [题解：](#-1)
	* 2.3. [语句和表达式](#-1)
		* 2.3.1. [零碎知识点](#-1)
		* 2.3.2. [题解](#-1)
	* 2.4. [函数](#-1)
		* 2.4.1. [零碎知识点](#-1)
		* 2.4.2. [题解](#-1)
* 3. [所有权和借用](#-1)
	* 3.1. [所有权](#-1)
		* 3.1.1. [零碎知识点](#-1)
	* 3.2. [题解：](#-1)
	* 3.3. [引用与借用](#-1)
		* 3.3.1. [零碎知识点](#-1)
		* 3.3.2. [题解](#-1)
* 4. [复合类型](#-1)
	* 4.1. [字符串与切片](#-1)
		* 4.1.1. [零碎知识点](#-1)
		* 4.1.2. [题解：](#-1)
	* 4.2. [元祖](#-1)
		* 4.2.1. [零碎知识点](#-1)
		* 4.2.2. [题解：](#-1)
	* 4.3. [结构体](#-1)
		* 4.3.1. [零碎知识点](#-1)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc -->
---
##  1. <a name=''></a>变量与常量
###  1.1. <a name='-1'></a>零碎知识点
Rust的赋值更确切来说是绑定，任何内存对象都是有主人的，而且一般情况下完全属于它的主人。

此外，Rust默认变量是不可变的，需要在使用`mut`关键字的情况下才能让变量变为可变。不可变会带来更多的安全性，而可变则会让灵活性得到提升。

采用`_`放在变量名前可以避免编译报变量被定义但未被使用过的错误，甚至可以避免初始化。

Rust允许利用`let`多次声明变量来对后续的变量进行屏蔽。
```rust
fn main() {
    let x = 5;
    // 在main函数的作用域内对之前的x进行遮蔽
    let x = x + 1;

    {
        // 在当前的花括号作用域内，对之前的x进行遮蔽
        let x = x * 2;
        println!("The value of x in the inner scope is: {}", x);
    }

    println!("The value of x is: {}", x);
}
// The value of x in the inner scope is: 12
// The value of x is: 6
```
与使用`mut`不同，此处生成了完全不同的形变量，两个变量只是恰好名字相同。尝试打印了他们的地址，发现确实是不一样的。

即便采用了`mut`关键字，Rust对类型的要求还是比较严格的，但如果用多次`let`就不会有这个问题。

利用`let`且没有`_`提前的变量是要好好初始化的。

**遗留问题**：解构式赋值暂时还不会使用。
###  1.2. <a name='-1'></a>题解：
```rust
// 1. 绑定和可变性
// 修复下面代码的错误并尽可能少的修改
fn main() {
    let x: i32 = 2; // 未初始化，但被使用
    let _y: i32; // 未初始化，也未被使用
    println!("x is equal to {}", x); 
}

// 完形填空，让代码编译
fn main() {
    let mut x = 1;
    x += 2; 
    
    println!("x = {}", x); 
}

// 2. 变量作用域
// 修复下面代码的错误并使用尽可能少的改变
fn main() {
    let y: i32 = 4;
    let x: i32 = 10;
    {
        let y: i32 = 5;
        println!("x 的值是 {}, y 的值是 {}", x, y);
    }
    println!("x 的值是 {}, y 的值是 {}", x, y); 
}

// 修复错误
fn main() {
    let x = "hello";
    println!("{}, world", x); 
}
// 3. 变量遮蔽
// 只允许修改 `assert_eq!` 来让 `println!` 工作(在终端输出 `42`)
fn main() {
    let x: i32 = 5;
    {
        let x = 12;
        assert_eq!(x, 12);
    }

    assert_eq!(x, 5);

    let x = 42;
    println!("{}", x); // 输出 "42".
}

fn main() {
    let mut x: i32 = 1;
    x = 7;
    // 遮蔽且再次绑定
    x += 3;


    let y = 4;
    // 遮蔽
    let y = "I can also be bound to text!"; 
}

// #[allow(unused_variables)] 宏如果存在也可以哦。
fn main() {
    let _x = 1;
    // const x: u32 = 1;
}

// 修复下面代码的错误并尽可能少的修改
fn main() {
    let (mut x, y) = (1, 2);
    x += 2;

    assert_eq!(x, 3);
    assert_eq!(y, 2);
}

fn main() {
    let (x, y);
    (x,..) = (3, 4);
    [.., y] = [1, 2];
    // 填空，让代码工作
    assert_eq!([x,y], [3, 2]);
} 
```

##  2. <a name='-1'></a>基本类型
###  2.1. <a name='-1'></a>数值类型
####  2.1.1. <a name='-1'></a>零碎知识点
Rust在一定程度上可以推测变量的类型，但在有可能出现模糊的时候，会报错。
```rust
let guess = "42".parse().expect("Not a number!");
```
针对整型溢出的情况，Rust默认情况下panic，如果用`--release`编译则会漠视该错误，只是以补码进位的方式来处理。Rust提供一部分相关字来显式处理可能的溢出行为。

对于浮点数的比较要小心，因为很有可能没有实现比较相等这一特性，从而无法进行比较。（可以把它当做用Java Object封装好的东东，如果要实现比较可能要自己加上一些规则）。此外，本身浮点数存在着精度问题，考虑一下IEEE 754标准中对于浮点数的实现，这个想法确实比较自然起来。

```rust
fn main() {
    let x = (-42.0_f32).sqrt();
    if x.is_nan() {
        println!("未定义的数学行为")
    }
}
```
对于数学上未作定义的行为，很有可能会报NaN的错误，可以利用`is_nan`来handle这件事。

Rust采用相当精简的方式来描述连续的数值：但这样的操作仅限于字符类型和数字。
```rust
for i in 1..=5 {
    println!("{}",i);
}
```
####  2.1.2. <a name='-1'></a>题解：
```rust
// 移除某个部分让代码工作
fn main() {
    let x: i32 = 5;
    let mut y: u32 = 5;

    // y = x;
    
    let z = 10; // 这里 z 的类型是? 默认采用i32
}

// 填空
fn main() {
    let v: u16 = 38_u8 as u16;
}

// 修改 `assert_eq!` 让代码工作
fn main() {
    let x = 5;
    assert_eq!("i32".to_string(), type_of(&x));
}

// 以下函数可以获取传入参数的类型，并返回类型的字符串形式，例如  "i8", "u8", "i32", "u32"
fn type_of<T>(_: &T) -> String {
    format!("{}", std::any::type_name::<T>())
}

// 填空，让代码工作
fn main() {
    assert_eq!(i8::MAX, 127); 
    assert_eq!(u8::MAX, 255); 
}


// 解决代码中的错误和 `panic`
fn main() {
   let v1 = 251_u8.wrapping_add(8);
   let v2 = u8::wrapping_add(251, 8);
   println!("{},{}",v1,v2);
}

// 修改 `assert!` 让代码工作
fn main() {
    let v = 1_024 + 0xff + 0o77 + 0b1111_1111;
    assert!(v == 1597);
}

// 将 ? 替换成你的答案
fn main() {
    let x = 1_000.000_1; // f64 默认如此
    let y: f32 = 0.12; // f32
    let z = 0.01_f64; // f64
}

fn main() {
    assert!(0.1_f32 + 0.2_f32 == 0.3_f32);
    assert!((0.1_f64 + 0.2 - 0.3).abs() < 0.00001);
}

fn main() {
    let mut sum = 0;
    for i in -3..=2 {
        sum += i
    }

    assert!(sum == -3);

    for c in 'a'..='z' {
        println!("{:?}", c as u8);
    }   
}

// 填空
use std::ops::{Range, RangeInclusive};
fn main() {
    assert_eq!((1..5), Range{ start: 1, end: 5 });
    assert_eq!((1..=5), RangeInclusive::new(1, 5));
}


// 填空，并解决错误
fn main() {
    // 整数加法
    assert!(1u32 + 2 == 3u32);
    
    // 整数减法
    assert!(1i32 - 2 == -1i32); // 对于signed类型，能够自己wrapping
    assert!(1u8.wrapping_sub(2) == 0xffu8); // 对于unsigned类型，不能够自己wrapping，需要依赖其他函数。
    // 1u8 - 2本来是不被允许的操作。
    assert!(3 * 50 == 150);

    assert!((9.6 / 3.2 - 3.0 as f32).abs() < 0.00001); // 可能有不明确的地方，要加入as关键字让其明确起来。
    assert!(24 % 5 == 4);
    
    // 逻辑与或非操作
    
    assert!(true && false == false);
    assert!(true || false == true);
    assert!(!true == false);
    
    // 位操作
    println!("0011 AND 0101 is {:04b}", 0b0011u32 & 0b0101);
    println!("0011 OR 0101 is {:04b}", 0b0011u32 | 0b0101);
    println!("0011 XOR 0101 is {:04b}", 0b0011u32 ^ 0b0101);
    println!("1 << 5 is {}", 1u32 << 5);
    println!("0x80 >> 2 is 0x{:x}", 0x80u32 >> 2);
}
```
###  2.2. <a name='-1'></a>字符、布尔、单元类型
####  2.2.1. <a name='-1'></a>零碎知识点
Rust中的布尔类型占用内存的大小为1个字节。

单元类型就是`()`，其可以用作`main`函数的返回值，也可以用作`map`的值，表示我们不关注具体的值，仅关注`key`。这种类型的数据是不占字节的。

Rust内的字符大小均为4个字节，是Unicode类型。
####  2.2.2. <a name='-1'></a>题解：
```rust
// 修改2处 `assert_eq!` 让代码工作
// size_of_val会返回类型所占的字节数目。
use std::mem::size_of_val;
fn main() {
    let c1 = 'a';
    assert_eq!(size_of_val(&c1), 4); 

    let c2 = '中';
    assert_eq!(size_of_val(&c2), 4); 

    println!("Success!")
} 

// 使成功打印
fn main() {
    let _f: bool = false;

    let t = true;
    if t {
        println!("Success!")
    }
} 

fn main() {
    let f = true;
    let t = true || false;
    assert_eq!(t, f);

    println!("Success!")
}

// 让代码工作，但不要修改 `implicitly_ret_unit` !
fn main() {
    let _v: () = ();

    let v = ();
    assert_eq!(v, implicitly_ret_unit());

    println!("Success!")
}

fn implicitly_ret_unit() {
    println!("I will return a ()")
}

// 不要使用下面的函数，它只用于演示！
fn explicitly_ret_unit() -> () {
    println!("I will return a ()")
}

// 让代码工作：修改 `assert!` 中的 `4` 
use std::mem::size_of_val;
fn main() {
    let unit: () = ();
    assert!(size_of_val(&unit) == 0);

    println!("Success!")
}
```
###  2.3. <a name='-1'></a>语句和表达式
####  2.3.1. <a name='-1'></a>零碎知识点
Rust的函数让最后一个表达式来返回值。表达式不同于一般的语句，不过也没必要太多区分，可以以函数式返回的方式去理解它们。

`let`在目前的Rust版本中是一个语句，不可以用作赋值。而表达式是可以用于赋值的，比如`let a = 5`中的`5`就是表达式。

表达式不能包含分号，否则会变成一条语句。如果表达式不返回任何值，则会隐式返回一个`()`。
####  2.3.2. <a name='-1'></a>题解
```rust
// 使用两种方法让代码工作起来
fn main() {
   let v = {
       let mut x = 1;
       x + 2
       // x += 2;
       //x
   };

   assert_eq!(v, 3);
}

fn main() {
   let v = {
       let x = 3;
       x
   };

   assert!(v == 3);
}

fn main() {
    let s = sum(1 , 2);
    assert_eq!(s, 3);
}

fn sum(x: i32, y: i32) -> i32 {
    x + y
}
```
###  2.4. <a name='-1'></a>函数
####  2.4.1. <a name='-1'></a>零碎知识点
对于函数的命名，最好按照蛇形的规范，比如命名为`snake_fn`。

对于函数的返回值，可以利用`return`实现提前返回，此外，可以不写`->`，表示返回的东西是`()`。如果函数以`;`结尾，那么同样返回值为`()`。你也可以显式地把返回值`-> ()`写出来。

当用`-> !`作为返回类型时，表示该函数永远不返回，这个语法一般用于会导致程序崩溃的函数。
```rust
fn dead_end() -> ! {
  panic!("你已经到了穷途末路，崩溃吧！");
}
```

####  2.4.2. <a name='-1'></a>题解
```rust
fn main() {
    // 不要修改下面两行代码!
    let (x, y) = (1, 2);
    let s = sum(x, y);

    assert_eq!(s, 3);
}

fn sum(x: i32, y: i32) -> i32 {
    x + y
}

fn main() {
   print();
}

// 使用另一个类型来替代 i32
fn print() -> () {
   println!("hello,world");
}

// 用两种方法求解
fn main() {
    never_return();
}

fn never_return() -> ! {
    // 实现这个函数，不要修改函数签名!
    // panic!("go fuck yourself.");
    loop {
    //...
        thread::sleep(time::Duration::from_secs(1)) // 别把电脑榨干了。
    };
}

fn main() {
    println!("Success!");
}

fn main() {
    println!("Success!");
    get_option(23);
}

fn get_option(tp: u8) -> Option<i32> {
    match tp {
        1 => {
            // TODO
            ()
        }
        _ => {
            // TODO
            ()
        }
    };
    
    // 这里与其返回一个 None，不如使用发散函数替代
    never_return_fn()
}

// 使用三种方法实现以下发散函数
fn never_return_fn() -> ! {
    // panic!("go fuck yourself");
    // unimplemented!()
    todo!();
}

fn main() {
    // 填空
    let b = false;

    let _v = match b {
        true => 1,
        // 发散函数也可以用于 `match` 表达式，用于替代任何类型的值
        false => {
            println!("Success!");
            panic!("we have no value for `false`, but we can panic")
        }
    };

    println!("Exercise Failed if printing out this line!");
}
```
##  3. <a name='-1'></a>所有权和借用
###  3.1. <a name='-1'></a>所有权
####  3.1.1. <a name='-1'></a>零碎知识点
如何将不需要的内存空间清空？三大流派：
1. GC: Java, Go.
2. 手动管理: C, C++.
3. 所有权管理内存：Rust.

所有权规则：
1. Rust的每一个值都被一个变量所拥有，该变量被称为值的所有者。
2. 一个值同时只能被一个变量所拥有，或者说一个值只能拥有一个所有者。
3. 当所有者（变量）离开作用域时，值将会被丢弃。

以`let s = "hello"`声明的字符串字面值是一种硬编码模式，因为其被硬编码到程序代码之中，其字面值是不可变的。这样，在使用时就缺乏了灵活性。为此，Rust提供的动态字符串类型`String`是会被分配到堆上。
```Rust
let s = String::from("hello");
```

对于在栈上的基础类型，Rust能够通过直接拷贝的方式将值传递。但是，以`String`为例的东东它存在在堆上，为此不能自动地执行拷贝操作。`String`内包含存在栈中的堆指针、字符串长度、字符串容量共同组成。

我们考虑变量的两种拷贝方式：
1. 深拷贝：将堆上的全部变量信息进行拷贝，这个过程享受不到Cache带来的性能优化，是不够经济的。
2. 仅拷贝`String`内存放的信息，让新的`String`指向同一个位置，但是这样会造成一个区域有两个外部指针与之相连。在离开作用域后，可能会造成二次释放的错误。

Rust以所有权的递交解决问题。比如以下就会报错。
```rust
let s1 = String::from("hello");
let s2 = s1;

println!("{}, world!", s1);
```
仔细考虑这个过程，结论似乎慢慢明晰起来，或许这个并不叫做拷贝，而是叫做移动才会更加的贴切。

而下面的这个例子就用的是栈咯，这个就显得比较自然一些：用栈去理解，就很清楚了。
```rust
fn main() {
    let x: &str = "hello, world";
    let y = x;
    println!("{},{}",x,y);
}
```

深拷贝：一般情况下不会用，需要特定地把它写清楚。
```rust
let s1 = String::from("hello");
let s2 = s1.clone();

println!("s1 = {}, s2 = {}", s1, s2);
```
这个`clone`操作会在堆上也执行拷贝行为，虽然避免了一些麻烦，但是让性能变得比较低。

浅拷贝：仅发生在栈上的行为。

Rust的Copy特征：对于栈内的东东均有效，栈内东东组成的闭包也有效`(，)`，不可变的引用（说白了就是指针嘛）有效，但是`&mut T`这种是可变的指针信息，不能用的。

我们用这一段代码来总结。
```rust
fn main() {
    let s = String::from("hello");  // s 进入作用域

    takes_ownership(s);             // s 的值移动到函数里 ...
                                    // ... 所以到这里不再有效

    let x = 5;                      // x 进入作用域

    makes_copy(x);                  // x 应该移动函数里，
                                    // 但 i32 是 Copy 的，所以在后面可继续使用 x

} // 这里, x 先移出了作用域，然后是 s。但因为 s 的值已被移走，
  // 所以不会有特殊操作

fn takes_ownership(some_string: String) { // some_string 进入作用域
    println!("{}", some_string);
} // 这里，some_string 移出作用域并调用 `drop` 方法。占用的内存被释放

fn makes_copy(some_integer: i32) { // some_integer 进入作用域
    println!("{}", some_integer);
} // 这里，some_integer 移出作用域。不会有特殊操作
```

###  3.2. <a name='-1'></a>题解：
```rust
fn main() {
    // 使用尽可能多的方法来通过编译
    let x = String::from("hello, world");
    let y = x.clone();
    println!("{},{}",x,y);
}

// 不要修改 main 中的代码
fn main() {
    let s1 = String::from("hello, world");
    let s2 = take_ownership(s1);

    println!("{}", s2);
}

// 只能修改下面的代码!
fn take_ownership(s: String) -> String {
    println!("{}", s);
    s
}


fn main() {
    let s = give_ownership();
    println!("{}", s);
}

// 只能修改下面的代码!
fn give_ownership() -> String {
    let s = String::from("hello, world");
    // convert String to Vec
    // 将 String 转换成 Vec 类型
    // let _s = s.into_bytes();  mark 一下，等到看到字符串再回来玩！
    s
}

// 修复错误，不要删除任何代码行
fn main() {
    let s = String::from("hello, world");

    let s = print_str(s);

    println!("{}", s);
}

fn print_str(s: String) -> String {
    println!("{}",s);
    s
}

// 不要使用 clone，使用 copy 的方式替代
fn main() {
    let x = (1, 2, (), "hello");
    let y = x;
    println!("{:?}, {:?}", x, y);
}

fn main() {
    let s = String::from("hello, ");
    
    // 只修改下面这行代码 !
    let mut s1 = s;

    s1.push_str("world")
}

fn main() {
    let x = Box::new(5);
    
    let mut y = Box::new(3);     // 完成该行代码，不要修改其它行！
    
    *y = 4;
    
    assert_eq!(*x, 5);
}

fn main() {
    #[derive(Debug)]
    struct Person {
        name: String,
        age: Box<u8>,
    }

    let person = Person {
        name: String::from("Alice"),
        age: Box::new(20),
    };

    // 通过这种解构式模式匹配，person.name 的所有权被转移给新的变量 `name`
    // 但是，这里 `age` 变量却是对 person.age 的引用, 这里 ref 的使用相当于: let age = &person.age 
    // ref并不会转移所有权，只是拿到了指针地址。
    let Person { name, ref age } = person;

    println!("The person's age is {}", age);

    println!("The person's name is {}", name);

    // Error! 原因是 person 的一部分已经被转移了所有权，因此我们无法再使用它
    // println!("The person struct is {:?}", person);

    // 虽然 `person` 作为一个整体无法再被使用，但是 `person.age` 依然可以使用
    println!("The person's age from person struct is {}", person.age);
}

fn main() {
   let t = (String::from("hello"), String::from("world"));

   let _s = t.0;

   // 仅修改下面这行代码，且不要使用 `_s`
   println!("{:?}", t.1);
}

fn main() {
   let t = (String::from("hello"), String::from("world"));

   // 填空，不要修改其它代码
   let (ref s1, ref s2) = t;

   println!("{:?}, {:?}, {:?}", s1, s2, t); // -> "hello", "world", ("hello", "world")
}
```
###  3.3. <a name='-1'></a>引用与借用
####  3.3.1. <a name='-1'></a>零碎知识点
常规引用和指针一毛一样。
```rust
fn main() {
    let x = 5;
    let y = &x;

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

不可变引用：`&`允许使用值，但不获取所有权。
```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```
当然，通过引用指向的值默认是不可变的。但同样有一种可变引用的方式，直接利用关键字`mut`让引用也变得可变就好了。但是这种引用在同一个作用域中只能有一个。并且，可变引用和不可变引用不能同时存在。
```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

新版的Rust会让引用作用域的结束位置从花括号变成最后一次试用的位置，让我们的工作变得灵活了很多。该优化被称作`Non-Lexical Lifetimes（NLL）`。

悬垂引用：dangling references

不同于返回s, 在函数终结之前移交的是&s，而s本身并没有送出去，因此堆内的`s`会被释放（因为没有人承接它）。
```rust
fn dangle() -> &String { // dangle 返回一个字符串的引用

    let s = String::from("hello"); // s 是一个新字符串

    &s // 返回字符串 s 的引用
} // 这里 s 离开作用域并被丢弃。其内存被释放。
  // 危险！
```
####  3.3.2. <a name='-1'></a>题解
```rust
fn main() {
   let x = 5;
   // 填写空白处
   let p = &x;

   println!("x 的内存地址是 {:p}", p); // output: 0x16fa3ac84
}

fn main() {
    let x = 5;
    let y = &x;

    // 只能修改以下行
    assert_eq!(5, *y);
}

// 修复错误
fn main() {
    let mut s = String::from("hello, ");

    borrow_object(&s);
}

fn borrow_object(s: &String) {} // 不可变引用

// 修复错误
fn main() {
    let mut s = String::from("hello, ");

    push_str(&mut s);
}

fn push_str(s: &mut String) {
    s.push_str("world")
}

fn main() {
    let mut s = String::from("hello, ");

    // 填写空白处，让代码工作
    let p = &mut s;
    
    p.push_str("world");
    println!("{}", s);
}

fn main() {
    let c = '中';

    let r1 = &c;
    // 填写空白处，但是不要修改其它行的代码
    let ref r2 = c; // ref的引用能得到一个值的引用。

    assert_eq!(*r1, *r2);
    
    // 判断两个内存地址的字符串是否相等
    assert_eq!(get_addr(r1),get_addr(r2));
}

// 获取传入引用的内存地址的字符串形式
fn get_addr(r: &char) -> String {
    format!("{:p}", r)
}

// 移除代码某个部分，让它工作
// 你不能移除整行的代码！
fn main() {
    let mut s = String::from("hello");

    let r1 = &s;
    let r2 = &s;

    println!("{}, {}", r1, r2);
}

fn main() {
    // 通过修改下面一行代码来修复错误
    let  s = String::from("hello, ");

    borrow_object(&s)
}

fn borrow_object(s: &String) {}


// 下面的代码没有任何错误，从可变的对象借用不可变的东西是可行的。
fn main() {
    let mut s = String::from("hello, ");

    borrow_object(&s);
    
    s.push_str("world");
}

fn borrow_object(s: &String) {}


// 注释掉一行代码让它工作，这个工作和生命周期有关系。
fn main() {
    let mut s = String::from("hello, ");

    let r1 = &mut s;
    r1.push_str("world");
    let r2 = &mut s;
    r2.push_str("!");
    
    //println!("{}",r1);
}


fn main() {
    let mut s = String::from("hello, ");

    let r1 = &mut s;
    let r2 = &mut s;
    // println!("r1 = {}, r2 = {}", r1, r2);// 当然是错的捏。
    // 在下面增加一行代码人为制造编译错误：cannot borrow `s` as mutable more than once at a time
    // 你不能同时使用 r1 和 r2
}
```
##  4. <a name='-1'></a>复合类型
`unimplemented!()`标记往往意味着暂时不实现功能，和`todo()!`功能一致，会返回`!`信息。对于复合类型的需求是因为基本数据类型确实没法帮助程序员完成更加复杂的工作，程序员需要从更高的抽象层去简化代码。

`#![allow(unused_variables)]`表示文件全局内均享受这个编译器噪声，而`#[allow(unused_variables)]`则表示仅下面这个块会享受这个编译器噪声。
###  4.1. <a name='-1'></a>字符串与切片
####  4.1.1. <a name='-1'></a>零碎知识点
Rust有Go中流行的切片，可以通过切片实现字符串的截取。
```rust
let s = String::from("hello world");

let hello = &s[0..5];
let world = &s[6..11];
// s[0..len], s[..]表示全部选取
```
特别的，这边的切片是左闭右开区间。需要小心的是，对于UTF-8格式的字符做完整的截取，不能截取一半。比如中文字符在UTF-8中占三个字节，不可以尝试肢解前两个字节。

下述例子展现了Rust切片不可变与可变的编译冲突。
```rust
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // error!

    println!("the first word is: {}", word);
}
fn first_word(s: &String) -> &str {
    &s[..1]
}
```

在Rust中的字符串字面量的类型是&str。&str是一个不可变的引用。也可以被视作是字符串切片的类型。
```rust
let s: &str = "hello world!";
```

与字符不同（编码Unicode），字符串是UTF-8编码。与语言级别上仅有的`str`类型不同，Rust标准库内同样存在`String`这一个动态UTF-8编码字符串。这两种类型可以自由转换：
```rust
fn main() {
    let s = String::from("hello,world!");
    say_hello(&s);
    say_hello(&s[..]);
    say_hello(s.as_str());
}

fn say_hello(s: &str) {
    println!("{}",s);
}
```

因为上述的编码方式，String是不允许索引的（毕竟UTF-8的话可能会有幺蛾子出来）。即便利用`&str`硬编码切片索引，也要合乎规范才可以使用，否则会panic。
```rust
let hello = "中国人";

let s = &hello[0..2];
```
比如中文占3个字节，这个行为就会panic。

####  4.1.2. <a name='-1'></a>题解：
1. 字符串
```rust
// 修复错误，不要新增代码行
fn main() {
    let s: &str = "hello, world";
}

// 使用至少两种方法来修复错误
fn main() {
    let s: Box<str> = "hello, world".into();
    //greetings(&s)
    greetings(s);
}

fn greetings(s: Box<str>/*s: &str*/) {
    println!("{}",s)
}

// 填空
fn main() {
    let mut s = String::from("");
    s.push_str("hello, world");
    s.push('!');

    assert_eq!(s, "hello, world!");
}

// 修复所有错误，并且不要新增代码行
fn main() {
    let mut s = String::from("hello");
    s.push(',');
    s.push_str(" world");
    s += "!"; // 利用add进行相加的话，不需要把&str利用to_string转化。

    println!("{}", s)
}

// 填空
fn main() {
    let s = String::from("I like dogs");
    // 以下方法会重新分配一块内存空间，然后将修改后的字符串存在这里
    let s1 = s.replace("dogs", "cats");

    assert_eq!(s1, "I like cats")
}

// 修复所有错误，不要删除任何一行代码
fn main() {
    let s1 = String::from("hello,");
    let s2 = String::from("world!");
    let s3 = s1 + &s2; 
    assert_eq!(s3,"hello,world!");
    // println!("{}",s1);
}

// 使用至少两种方法来修复错误
fn main() {
    let s = "hello, world";
    greetings(&s)
    // greetings(s.to_string());
}

fn greetings(s: &str/*s: String*/) {
    println!("{}",s)
}

// 使用两种方法来解决错误，不要新增代码行
fn main() {
    let s = "hello, world"/*.to_string()*/;
    let s1: &str = s/*.as_str()*/;
}

fn main() {
    // 你可以使用转义的方式来输出想要的字符，这里我们使用十六进制的值，例如 \x73 会被转义成小写字母 's'
    // 填空以输出 "I'm writing Rust"
    let byte_escape = "I'm writing Ru\x73\x74!";
    println!("What are you doing\x3F (\\x3F means ?) {}", byte_escape);

    // 也可以使用 Unicode 形式的转义字符
    let unicode_codepoint = "\u{211D}";
    let character_name = "\"DOUBLE-STRUCK CAPITAL R\"";

    println!("Unicode character {} (U+211D) is called {}",
                unicode_codepoint, character_name );

    // 还能使用 \ 来连接多行字符串
    let long_string = "String literals
                        can span multiple lines.
                        The linebreak and indentation here \
                         can be escaped too!";
    println!("{}", long_string);
}

/* 填空并修复所有错误 */
fn main() {
    let raw_str = "Escapes don't work here: \x3F \u{211D}";
    // 修改上面的行让代码工作
    assert_eq!(raw_str, "Escapes don't work here: ? ℝ");

    // 如果你希望在字符串中使用双引号，可以使用以下形式
    let quotes = r#"And then I said: "There is no escape!""#;
    println!("{}", quotes);

    // 如果希望在字符串中使用 # 号，可以如下使用：
    let  delimiter = r###"A string with "# in it. And even "##!"###;
    println!("{}", delimiter);

    // 填空
    let long_delimiter = "Hello, \"##\"";
    println!("{}", long_delimiter);
    assert_eq!(long_delimiter, "Hello, \"##\"")
}
//'Tokens' 章节.可以参阅，以了解一些字节字符串的信息。

fn main() {
    let s1 = String::from("hi,中国");
    let h = &s1[0..1]; // 修改当前行来修复错误，提示: `h` 字符在 UTF-8 格式中只需要 1 个字节来表示
    assert_eq!(h, "h");

    let h1 = &s1[3..6];// 修改当前行来修复错误，提示: `中` 字符在 UTF-8 格式中需要 3 个字节来表示
    assert_eq!(h1, "中");
}

fn main() {
    // 填空，打印出 "你好，世界" 中的每一个字符
    for c in "你好，世界".chars() {
        println!("{}", c)
    }
}

// 修复代码中的错误，不要新增代码行!
fn main() {
    let arr = [1, 2, 3];
    let s1: &[i32] = &arr[0..2];

    let s2: &str = "hello, world" as &str;
}

// 一个切片占用了两个字大小的内存空间，一个是指向数据的指针，一个是切片的长度。
fn main() {
    let arr: [char; 3] = ['中', '国', '人']; // unicode格式。

    let slice = &arr[..2];
    
    // 修改数字 `8` 让代码工作
    // 小提示: 切片和数组不一样，它是引用。如果是数组的话，那下面的 `assert!` 将会通过： '中'和'国'是char类型，char类型是Unicode编码，大小固定为4字节，两个字符为8字节。
    assert!(std::mem::size_of_val(&slice) == 16); // 这边问的是切片引用的大小。而不是切片内含有的'中''国'两个字符占了多大的空间。
}

fn main() {
   let arr: [i32; 5] = [1, 2, 3, 4, 5];
  // 填空让代码工作起来
  let slice: &[i32] = &arr[1..4];
  assert_eq!(slice, &[2, 3, 4]);
}

fn main() {
    let s = String::from("hello");

    let slice1 = &s[0..2];
    // 填空，不要再使用 0..2
    let slice2 = &s[0..=1];

    assert_eq!(slice1, slice2);
}

fn main() {
    let s = "你好，世界";
    // 修改以下代码行，让代码工作起来
    let slice = &s[0..3];

    assert!(slice == "你");
}

// 修复所有错误
fn main() {
    let mut s = String::from("hello world");

    // 这里, &s 是 `&String` 类型，但是 `first_character` 函数需要的是 `&str` 类型。
    // 尽管两个类型不一样，但是代码仍然可以工作，原因是 `&String` 会被隐式地转换成 `&str` 类型，如果大家想要知道更多，可以看看 Deref 章节: https://course.rs/advance/smart-pointer/deref.html
    let ch = first_character(&s);

    // s.clear(); // error!

    println!("the first character is: {}", ch);
}
fn first_character(s: &str) -> &str {
    &s[..1]
}


// 填空并修复错误
// 1. 不要使用 `to_string()`
// 2. 不要添加/删除任何代码行
fn main() {
    let mut s: String = String::from("hello, ");
    s.push_str("world");
    s.push('!');

    s = move_ownership(s);

    assert_eq!(s, "hello, world!");

    println!("Success!")
}

fn move_ownership(s: String) -> String {
    println!("ownership of \"{}\" is moved here!", s);
    s
}

// 虽然 String 的底层是 Vec<u8> 也就是字节数组的形式存储的，但是它是基于 UTF-8 编码的字符序列。String 分配在堆上、可增长且不是以 null 结尾。
// 而 &str 是切片引用类型( &[u8] )，指向一个合法的 UTF-8 字符序列，总之，&str 和 String 的关系类似于 &[T] 和 Vec<T> 。

// 填空
fn main() {  
   let mut s = String::from("hello, world");

   let slice1: &str = /*s.as_str()*/ &s[..]; // 使用两种方法
   assert_eq!(slice1, "hello, world");
   
   let slice2 = &s[..5];
   assert_eq!(slice2, "hello");

   let mut slice3: String = slice1.to_string(); 
   slice3.push('!');
   assert_eq!(slice3, "hello, world!");

   println!("Success!")
}

// 问题:  我们的代码中发生了多少次堆内存分配？
// 你的回答: 1 + 1 = 2
fn main() {  
    // 基于 `&str` 类型创建一个 String,
    // 字符串字面量的类型是 `&str`
   let s: String = String::from("hello, world!");

   // 创建一个切片引用指向 String `s`
   let slice: &str = &s;

   // 基于刚创建的切片来创建一个 String
   let s: String = slice.to_string();

   assert_eq!(s, "hello, world!");

   println!("Success!")
}

// 填空并修复错误
fn main() {
    let s = String::from("hello, 世界");
    let slice1 = &s[0..1]; //提示: `h` 在 UTF-8 编码中只占用 1 个字节
    assert_eq!(slice1, "h");

    let slice2 = &s[7..10];// 提示: `中` 在 UTF-8 编码中占用 3 个字节
    assert_eq!(slice2, "世");
    
    // 迭代 s 中的所有字符
    for (i, c) in s.chars().enumerate() { // 请注意enumerate的特殊用法。https://www.oreilly.com/library/view/learning-rust/9781785884306/70af2a22-d0e1-46ab-ae11-72630a834c33.xhtml
        if i == 7 {
            assert_eq!(c, '世')
        }
    }

    println!("Success!")
}

// 填空
use std::str;
fn main() {
    let mut s = String::new();
    s.push_str("hello");

    let v = vec![104, 101, 108, 108, 111];

    // 将字节数组转换成 String
    let s1 = match str::from_utf8(&v) {
        Ok(v) => v,
        Err(e) => panic!("Invalid UTF-8 sequence: {}", e),
    };
    
    
    assert_eq!(s, s1);

    println!("Success!")
}


// 填空
use std::str;
fn main() {
    let mut s = String::new();
    s.push_str("hello");

    let v = vec![104, 101, 108, 108, 111];

    // 将字节数组转换成 String
    let s1 = String::from_utf8_lossy(&v);
    
    assert_eq!(s, s1);

    println!("Success!")
}

// String是一个智能指针结构体，其在栈上存放了一个结构体，指向存储在堆上的字符串底层数据。
// 一个指针只指向堆上的字节数组，已使用的长度以及已分配的容量 capacity (已使用的长度小于等于已分配的容量，当容量不够时，会重新分配内存空间)。
// 对于capacity的分配，似乎遵循的是*2的原则，从8个字节长度开始计量，如果不够我就不停地乘2.

// 修改下面的代码以打印如下内容: 
// 25
// 25
// 25
// 循环中不会发生任何内存分配
fn main() {
    let mut s = String::with_capacity(25);
    println!("{}", s.capacity());

    for _ in 0..2 {
        s.push_str("hello");
        println!("{}", s.capacity());
    }

    println!("Success!")
}

// 填空
use std::mem;

fn main() {
    let story = String::from("Rust By Practice");

    // 阻止 String 的数据被自动 drop，这个地方要注意。
    let mut story = mem::ManuallyDrop::new(story);

    let ptr = story.as_mut_ptr();
    let len = story.len();
    let capacity = story.capacity();

    assert_eq!(16, len);

    // 我们可以基于 ptr 指针、长度和容量来重新构建 String. 
    // 这种操作必须标记为 unsafe，因为我们需要自己来确保这里的操作是安全的
    let s = unsafe { String::from_raw_parts(ptr, len, capacity) };

    assert_eq!(*story, s);

    println!("Success!")
}
```
###  4.2. <a name='-1'></a>元祖
####  4.2.1. <a name='-1'></a>零碎知识点
使用方法极端类似于python内的赋值。
```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```
####  4.2.2. <a name='-1'></a>题解：
```rust
fn main() {
    let _t0: (u8,i16) = (0, -1);
    // 元组的成员还可以是一个元组
    let _t1: (u8, (i16, u32)) = (0, (-1, 1));
    // 填空让代码工作
    let t: (u8, u16, i64, &str, String) = (1u8, 2u16, 3i64, "hello", String::from(", world"));
}

// 修改合适的地方，让代码工作
fn main() {
    let t = ("i", "am", "sunface");
    assert_eq!(t.2, "sunface");
}

// 修复代码错误，长度比13小就可以打印了。
fn main() {
    let too_long_tuple = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12);
    println!("too long tuple: {:?}", too_long_tuple);
}

fn main() {
    let tup = (1, 6.4, "hello");

    // 填空
    let (x, z, y) = tup;

    assert_eq!(x, 1);
    assert_eq!(y, "hello");
    assert_eq!(z, 6.4);
}

fn main() {
    let (x, y, z);

    // 填空
    (y, z, x) = (1, 2, 3);
    
    assert_eq!(x, 3);
    assert_eq!(y, 1);
    assert_eq!(z, 2);
}

fn main() {
    // 填空，需要稍微计算下
    let (x, y) = sum_multiply((3, 2));

    assert_eq!(x, 5);
    assert_eq!(y, 6);
}

fn sum_multiply(nums: (i32, i32)) -> (i32, i32) {
    (nums.0 + nums.1, nums.0 * nums.1)
}
```
###  4.3. <a name='-1'></a>结构体
####  4.3.1. <a name='-1'></a>零碎知识点
结构体默认自己的元素不可变。

结构体更新语法。
```rust
  let user2 = User {
        active: user1.active,
        username: user1.username,
        email: String::from("another@example.com"),
        sign_in_count: user1.sign_in_count,
    };

  let user2 = User {
        email: String::from("another@example.com"),
        ..user1
    };
```
利用这样的语法能让除了在`..`以外具体写出来的东西保持不变。当然，`..user1`需要放在尾部。对于动态分配在堆上的东东（即不满足COPY规则的东东），其所有权会发生转移（意料之中）。

Rust内结构体的内存排列与C语言是很接近的，不过至少需要知道哪些东西是存在栈内，哪些东西是存在堆上的。
![](https://pic3.zhimg.com/80/v2-8cc4ed8cd06d60f974d06ca2199b8df5_1440w.png)

元祖结构体：需要对其整体有一个命名方式，但是对其其中的载荷的名称不是很关心。比如描述三维空间的一个点到原点的距离，这时候就没必要一定把xyz的信息填进去。或者，比如已经把RGB规则写在脸上的东东。
```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);
let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);
```

如果在结构体中使用一个引用，需要加上生命周期（会比较麻烦）

一些关键字：`#[derive(Debug)]`，`dbg!`。
#### 题解
```rust
// fix the error
struct Person {
    name: String,
    age: u8,
    hobby: String
}
fn main() {
    let age = 30;
    let p = Person {
        name: String::from("sunface"),
        age,
        hobby: String::from("sing dance rap basketball")
    };
} 

struct Unit;
trait SomeTrait {
    // ...定义一些行为
}

// 我们并不关心结构体中有什么数据( 字段 )，但我们关心它的行为。Unit可以什么都不写。
// 因此这里我们使用没有任何字段的单元结构体，然后为它实现一些行为
impl SomeTrait for Unit { }
fn main() {
    let u = Unit;
    do_something_with_unit(u);
} 

// 填空，让代码工作
fn do_something_with_unit(u: Unit) {   }


// 填空并修复错误
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);
fn main() {
    let v = Color(0, 127, 255);
    check_color(v);
}   

fn check_color(p: Color) {
    let Color(x, y, z) = p;
    assert_eq!(x, 0);
    assert_eq!(p.1, 127);
    assert_eq!(p.2, 255);
 }

// 填空并修复错误，不要增加或移除代码行
struct Person {
    name: String,
    age: u8,
}
fn main() {
    let age = 18;
    let mut p = Person {
        name: String::from("sunface"),
        age,
    };

    // how can you believe sunface is only 18? 
    p.age = 30;

    // 填空
    p.name = String::from("sunfei");
}

// 填空
struct Person {
    name: String,
    age: u8,
}
fn main() {} 

fn build_person(name: String, age: u8) -> Person {
    Person {
        name,
        age,
    }
}


// 填空，让代码工作
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
fn main() {
    let u1 = User {
        email: String::from("someone@example.com"),
        username: String::from("sunface"),
        active: true,
        sign_in_count: 1,
    };

    let u2 = set_email(u1);
} 

fn set_email(u: User) -> User {
    User {
        email: String::from("contact@im.dev"),
        ..u
    }
}

// 填空，让代码工作
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let scale = 2;
    let rect1 = Rectangle {
        width: dbg!(30 * scale), // 打印 debug 信息到标准错误输出 stderr,并将 `30 * scale` 的值赋给 `width`
        height: 50,
    };

    dbg!(&rect1); // 打印 debug 信息到标准错误输出 stderr

    println!("{:?}", rect1); // 打印 debug 信息到标准输出 stdout
}

// 修复错误
#[derive(Debug)]
struct File {
    name: String,
    data: String,
}
fn main() {
    let f = File {
        name: String::from("readme.md"),
        data: "Rust By Practice".to_string()
    };

    let _name = f.name;

    // 只能修改这一行
    println!("{}, {}", _name, f.data);
} 

```
### 枚举
#### 零碎知识点
枚举类型可以利用其他值与之关联。任何类型的数据都可以放入到枚举成员中。
```rust
enum PokerCard {
    Clubs(u8),
    Spades(u8),
    Diamonds(char),
    Hearts(char),
}

fn main() {
   let c1 = PokerCard::Spades(5);
   let c2 = PokerCard::Diamonds('A');
}
```
下面的这个例子更能体现出枚举类型的优势所在，如果用struct进行封装，会显得很麻烦。
```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

fn main() {
    let m1 = Message::Quit;
    let m2 = Message::Move{x:1,y:1};
    let m3 = Message::ChangeColor(255,255,0);
}
```

Rust抛弃了NULL这个概念，使用Option枚举变量来表述这个结果。
```rust
enum Option<T> {
    Some(T), // 表示该枚举诚愿的数据类型为T，T是泛型参数。
    None, // 没有值
}
```
利用Option的封装会得到一个好处：这实际上就是加了一层壳的意思，对于T类型的数据，无法实现Option<T>与之相加等相关操作的直接进行，而是需要把这个核桃打开，看看里面是不是空的。那么这个过程就会涉及None值的检查。

如何进行使用，我们可以利用match来实现。
```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
```
#### 题解
```rust
// 修复错误
enum Number {
    Zero,
    One = 1,
    Two,
}

enum Number1 {
    Zero = 0,
    One = 1,
    Two,
}

// C语言风格的枚举定义
enum Number2 {
    Zero = 0,
    One = 1,
    Two = 2,
}


fn main() {
    // 通过 `as` 可以将枚举值强转为整数类型
    assert_eq!(Number::One as i32, Number1::One as i32);
    assert_eq!(Number1::One as i32, Number2::One as i32);
} 

// 填空
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

fn main() {
    let msg1 = Message::Move{x:1, y:2}; // 使用x = 1, y = 2 来初始化
    let msg2 = Message::Write("hello, world!".to_string()); // 使用 "hello, world!" 来初始化
} 


// 仅填空并修复错误
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

fn main() {
    let msg = Message::Move{x: 1, y: 1};

    if let Message::Move{x:a, y:b} = msg { // 感觉是很奇妙的用法。
        assert_eq!(a, b);
    } else {
        panic!("不要让这行代码运行！");
    }
} 

// 填空，并修复错误
#[derive(Debug)]
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

fn main() {
    let msgs: [Message; 3] = [
        Message::Quit,
        Message::Move{x:1, y:3},
        Message::ChangeColor(255,255,0)
    ];

    for msg in msgs {
        show_message(msg)
    }
} 

fn show_message(msg: Message) {
    println!("{:?}", msg);
}


// 填空让 `println` 输出，同时添加一些代码不要让最后一行的 `panic` 执行到
// 但是这边的if let模式匹配到底是什么东西，为什么可以这样用。搞不灵清，之后学习的时候回来看吧。
fn main() {
    let five = Some(5);
    let six = plus_one(five);
    let none = plus_one(None);

    if let Some(n) = six {
        println!("{}", n);
        return;
    } 
        
    panic!("不要让这行代码运行！");
} 

fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

// 后面的那道链表题我现在可能太早了，先润。
```