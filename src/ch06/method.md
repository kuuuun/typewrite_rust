# 6. 方法Method

## 6.1 定义方法

```rust
struct Circle {
        x: f64,
        y: f64,
        radius: f64,
}

impl Circle {
    // new是Circle的关聀函数，因为它的第一个参数不是self，且new并不是关键字
    // 这种方法往往用于初始化当前结构体的实例
    fn new(x: f64, y: f64, radius: f64) -> Circle {
        Circle {
                x: x,
                y: y,
                radius: radius,
        }
    }
    // NOTE: 方法和字段名相同，往往适用于getter访问器
    fn radius(&self) -> f64 {
        self.radius
    }

    // NOTE:*self*: 表示所有权借用到该方法中，用的较少
    // NOTE:*&self*: 表示该方法对结构体的不可变借用
    // NOTE:*&mut self*: 表示该方法对结构体的可变借用
    // NOTE:*Self*: 表示结构体类型，也就是Circle
    //
    // Circle的方法，&self表示借用当前的Circle结构体
    fn area(&self) -> f64 {
        std::f64::consts::PI * (self.radius * self.)
    }
}

fn main(){
    let circle = Circle::new(0.0, 0.0, 5.0);

    // NOTE: Rust有一个自动引用和解引用的功能。方法调用是少数几个拥有这种行为的地方。
    // p1.distance(&p2);
    // (&p1).distance(&p2);
    println!("{}", circle.radius());
}
```

## 6.2 带有多个参数的方法

```rust
#![allow(unused)]
struct Rectangle{
    width:u32,
    height:u32,
}

impl Rectangle {
    // 构造函数，可简化结构体初始化
    fn new(w: u32, h: u32) -> Rectangle {
        Rectangle { width: w, height: h }
    }

    fn area(&self) -> u32 {
        self.width * self.height
    }

    // &Rectangle == &Self, 不拿所有权
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

fn main() {
    // let rect1 = Rectangle { width: 30, height: 50 };
    let rect1 = Rectangle::new(30, 50);
    let rect2 = Rectangle { width: 10, height: 40 };
    let rect3 = Rectangle { width: 60, height: 45 };

    println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
    println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
}
```

## 6.3 关联函数

```rust
impl Rectangle {
    // 构造函数，可简化结构体初始化
    fn new(w: u32, h: u32) -> Rectangle {
        Rectangle { width: w, height: h }
    }
}
```

## 6.4 多个impl定义

```rust
// 用于演示，实际不用
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

impl Rectangle {
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
```

## 6.5 为枚举实现方法

```rust
// NOTE: 枚举强大，在于它好用，可以同一化类型，还可以像结构一样实现方法。
#![allow(unused)]
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

impl Message {
    fn call(&self) {
        // 在这里定义方法体
        match &self {
            Message::Write(str) => {println!("Write string is {}", str);},
            _ => {println!("no message in Message::Write()");},
        }
    }
}

fn main() {
    let m = Message::Write(String::from("hello"));
    m.call();   // output: Write string is "hello"
    let n = Message::Quit;
    n.call();   // output: no message in Message::Write()
}
```
