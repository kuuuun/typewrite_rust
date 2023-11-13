# 3.3 结构体

## 3.3.1 结构体语法

```rust
// 定义结构体
// 结构体数据存在堆上, 实际是指针
struct User {
  active: bool,
  username: String,
  email: String,
  sign_in_count: u64,
}

// 实例化
let mut user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};

// 访问结构体的成员
user1.email = String::from("anotheremail@example.com");

// 结构体更新语法
let user2 = User {
      email: String::from("another@example.com"),
      ..user1
};
```

## 3.3.2 元祖结构体

```rust
struct Color(i32, i32, i32);
let black = Color(0, 0, 0);
```

## 3.3.3 单元结构体

```rust
struct AlwaysEqual;

let subject = AlwaysEqual;

// 我们不关心 AlwaysEqual 的字段数据，只关心它的行为，因此将它声明为单元结构体，然后再为它实现某个特征
impl SomeTrait for AlwaysEqual {}
```

## 3.3.4 结构体数据的所有权

```rust
// 因为我们想要这个结构体拥有它所有的数据，而不是从其它地方借用数据。
// 你也可以让 User 结构体从其它对象借用数据，不过这么做，就需要引入生命周期(lifetimes)这个新概念
struct User<'a> {
    username: &'a str,
    email: &'a str,
    sign_in_count: u64,
    active: bool,
}

fn main() {
    let user1 = User {
        email: "someone@example.com",
        username: "someusername123",
        active: true,
        sign_in_count: 1,
    };
}
```

## 3.3.5 使用`#[derive(Debug)]` 来打印结构体的信息

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let scale = 2;
    let rect1 = Rectangle {
        width: dbg!(30 * scale),
        height: 50,
    };

    println!("rect1 is {:?}", rect1);
    // 还有一个简单的输出 debug 信息的方法，那就是使用 dbg! 宏，
    // 它会拿走表达式的所有权，然后打印出相应的文件名、行号等 debug 信息，
    // 当然还有我们需要的表达式的求值结果。除此之外，它最终还会把表达式值的所有权返回!
    dbg!(&rect1);
}
```

```text
$ cargo run
rect1 is Rectangle { width: 30, height: 50 }

$ cargo run
[src/main.rs:10] 30 * scale = 60
[src/main.rs:14] &rect1 = Rectangle {
    width: 60,
    height: 50,
}
```

> dbg! 输出到标准错误输出 stderr，而 println! 输出到标准输出 stdout。
