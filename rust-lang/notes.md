## Chapter 7: crates, packages & modules.
编译过程：
1. 编译器从lib.rs和main.rs，分别从库文件和二进制文件作为`crate root`文件开始工作。
2. 如果在上述文件中能够看到`mod garden`，编译器会在当前包内文件、garden.rs，garden/mod.rs这三个文件位置处去寻找这个包。
3. 在`mod garden`内如果又看到了新的`mod`，那么会以上述2中递归的方式去查询子模块。
4. mod的引用方式很像C++。

```
结构简示：
backyard
├── Cargo.lock
├── Cargo.toml
└── src
    ├── garden
    │   └── vegetables.rs
    ├── garden.rs
    └── main.rs

use crate::garden::vegetables::Asparagus;

pub mod garden;

fn main() {
    let plant = Asparagus {};
    println!("I'm growing {:?}!", plant);
}

// src/garden.rs
pub mod vegetables;
// src/garden/begetables.rs
#[derive(Debug)]
pub struct Asparagus {}
```

同样的，对于lib，可以采用`nested modules`的形式来。

`cargo new restaurant --lib`以建立依赖包的构型。
```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn serve_order() {}

        fn take_payment() {}
    }
}

crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```
在Module Tree中refer不同的模块时，`absolute path`表示从`crate root`开始的工作，而`relative path`表示从当前的模块开始的工作，使用`self`和`super`等关键字来实现索引。
```rust
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
```