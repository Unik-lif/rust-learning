## 2.1 变量与常量
### 零碎知识点
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
### 题解：
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

## 2.2 基本类型
### 2.2.1 数值类型
#### 零碎知识点
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
#### 题解：
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
### 2.2.2 字符、布尔、单元类型
#### 零碎知识点
Rust中的布尔类型占用内存的大小为1个字节。

单元类型就是`()`，其可以用作`main`函数的返回值，也可以用作`map`的值，表示我们不关注具体的值，仅关注`key`。这种类型的数据是不占字节的。

Rust内的字符大小均为4个字节，是Unicode类型。
#### 题解：
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
### 2.2.3 语句和表达式
#### 零碎知识点
Rust的函数让最后一个表达式来返回值。表达式不同于一般的语句，不过也没必要太多区分，可以以函数式返回的方式去理解它们。

`let`在目前的Rust版本中是一个语句，不可以用作赋值。而表达式是可以用于赋值的，比如`let a = 5`中的`5`就是表达式。

表达式不能包含分号，否则会变成一条语句。如果表达式不返回任何值，则会隐式返回一个`()`。
#### 题解
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
### 2.2.4 函数
#### 零碎知识点
对于函数的命名，最好按照蛇形的规范，比如命名为`snake_fn`。

对于函数的返回值，可以利用`return`实现提前返回，此外，可以不写`->`，表示返回的东西是`()`。如果函数以`;`结尾，那么同样返回值为`()`。你也可以显式地把返回值`-> ()`写出来。

当用`-> !`作为返回类型时，表示该函数永远不返回，这个语法一般用于会导致程序崩溃的函数。
```rust
fn dead_end() -> ! {
  panic!("你已经到了穷途末路，崩溃吧！");
}
```

#### 题解
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
## 2.3 所有权和借用
#### 零碎知识点
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

### 题解：
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
```