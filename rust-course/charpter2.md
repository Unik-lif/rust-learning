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
```