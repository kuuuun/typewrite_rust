# 5.1 match和if let

## 5.1.1 匹配模式: match和if let

match:

```rust
enum Direction {
    East,
    West,
    North,
    South,
}

// 模式匹配最常用的就是 match 和 if let
fn main() {
    let dire = Direction::South;
    match dire {
        Direction::East => println!("East"),
        Direction::North | Direction::South => {
            println!("South or North");
        },
        _ => println!("West"),
    };
}
```

if let:

```rust
enum Direction {
    East,
    West,
    North,
    South,
}

// 只判断一个选择的时候用if let，多个选择用match
fn main() {
    let dire = Direction::South;
    if let Direction::South = dire {
        println!("South or North");
    }
}
```

## 5.1.2 使用 match 表达式赋值

```rust
enum IpAddr {
   Ipv4,
   Ipv6
}

fn main() {
    let ip1 = IpAddr::Ipv6;
    // match 本身也是一个表达式，因此可以用它来赋值
    let ip_str = match ip1 {
        IpAddr::Ipv4 => "127.0.0.1",
        _ => "::1",
    };

    println!("{}", ip_str);
}
```

## 5.1.3 模式绑定

模式绑定：

```rust
enum Action {
    Say(String),
    MoveTo(i32, i32),
    ChangeColorRGB(u16, u16, u16),
}

fn main() {
    let actions = [
        Action::Say("Hello Rust".to_string()),
        Action::MoveTo(1,2),
        Action::ChangeColorRGB(255,255,0),
    ];
    for action in actions {
        match action {
            Action::Say(s) => {
                println!("{}", s);
            },
            Action::MoveTo(x, y) => {
                println!("point from (0, 0) move to ({}, {})", x, y);
            },
            Action::ChangeColorRGB(r, g, _) => {
                println!("change color into '(r:{}, g:{}, b:0)', 'b' has been ignored",
                    r, g,
                );
            }
        }
    }
}
```

匹配取enum里的值：

```rust
#[derive(Debug)]
enum UsState {
    Alabama,
    Alaska,
    // --snip--
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState), // 25美分硬币
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        // 内部存储的值绑定到了 state 变量上，因此 state 变量就是对应的 UsState 枚举类型。
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        },
    }
}

fn main() {
    let coin = Coin::Quarter(UsState::Alaska);
    assert_eq!(value_in_cents(coin), 25);
}
```

解包取enum里的元组：

```rust
#[allow(dead_code)]
enum Action {
    Say(String),
    MoveTo(i32, i32),
    ChangeColorRGB(u16, u16, u16),
}

fn main() {
    let actions = Action::MoveTo(12, 34);
    let res = match actions {
        // 获取远祖，并拆包
        Action::MoveTo(x, y) => (Some(x), Some(y)),
        _ => (None, None),
    };
    assert_eq!(res.0.unwrap(),12);
    assert_eq!(res.1.unwrap(),34);
}
```

## 5.1.4 穷尽匹配

```rust
fn main(){
    let number = 42;
    match number{
        0 => println!("Origin"),
        1...3 => println!("All"),
        |5|7|13| => println!("Bad Luck"),
        n @ 42 => println!("Answer is {}", n),
        _ => println!("Common"),
    }
}
```

## 5.1.5 if let匹配

```rust
// 只有一种情况的匹配,适合使用 if let
let v = Some(4);
if let Some(4) = v {
    println!("four");
}

// match 需要穷尽匹配
let v = Some(3u8);
match v {
    Some(3) => println!("three"),
    _ => (),
}
```

## 5.1.6 matches!宏

```rust
macro_rules! matches {
    ($expression:expr, $pattern:pat $(if $guard:expr)? $(,)?) => { ... };
}
// 返回给定表达式是否与任何给定模式匹配
// 像在match表达式中一样，可以在模式后跟if和可以访问由模式绑定的名称的保护表达式
```

```rust
let foo = 'f';
// `..=` 正则
assert!(matches!(foo, 'A'..='Z' | 'a'..='z'));

let bar = Some(4);
// 匹配守卫模式
assert!(matches!(bar, Some(x) if x > 2));

```

```rust
enum MyEnum {
    Foo,
    Bar
}

fn main() {
    let mut count = 0;

    let v = vec![MyEnum::Foo,MyEnum::Bar,MyEnum::Foo];
    for e in v {
        if matches!(e, MyEnum::Foo) {
            count += 1;
        }
    }
    assert_eq!(count, 2);
}
```

```rust
#[derive(Debug)]
enum MyEnum {
    Foo,
    Bar
}

fn main() {
    let v = vec![MyEnum::Foo,MyEnum::Bar,MyEnum::Foo];

    for a in v.iter().filter(|x| matches!(x, MyEnum::Foo)){
        println!("{:?}", a);
    }
}
```

## 5.1.7 变量遮蔽

```rust
// 在if let 中，= 右边 Some(i32) 类型的 age 被左边 i32 类型的新 age 遮蔽了，
// 该遮蔽一直持续到 if let 语句块的结束。
// 因此第三个 println! 输出的 age 依然是 Some(i32) 类型。
// BUG: 说的有点不对！因为同名
fn main() {
   let age = Some(30);
   println!("在匹配前，age是{:?}",age);
    // 尽量不用用同名,避免难以理解, 比如用x
   if let Some(age) = age {
       println!("匹配出来的age是{}",age);
   }

   println!("在匹配后，age是{:?}",age);
}
```

```rust
fn main() {
   let age = Some(30);
   println!("在匹配前，age是{:?}",age);
   match age {
        // 尽量不用用同名,避免难以理解, 比如用x
       Some(age) =>  println!("匹配出来的age是{}",age),
       _ => ()
   }
   println!("在匹配后，age是{:?}",age);
}
```
