## 5. 模式匹配

### 5.1 match和if let

#### 5.1.1 匹配模式: match和if let

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

#### 5.1.2 使用 match 表达式赋值

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

#### 5.1.3 模式绑定

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

#### 5.1.4 穷尽匹配

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

#### 5.1.5 if let匹配

```rust
// 只有一种情况的匹配,适合使用 if let
if let Some(3) = v {
    println!("three");
}

// match 需要穷尽匹配
let v = Some(3u8);
match v {
    Some(3) => println!("three"),
    _ => (),
}
```

#### 5.1.6 matches!宏

```rust
let foo = 'f';
assert!(matches!(foo, 'A'..='Z' | 'a'..='z'));

let bar = Some(4);
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

#### 5.1.7 变量遮蔽

```rust
// 在if let 中，= 右边 Some(i32) 类型的 age 被左边 i32 类型的新 age 遮蔽了，该遮蔽一直持续到 if let 语句块的结束。
// 因此第三个 println! 输出的 age 依然是 Some(i32) 类型。
fn main() {
   let age = Some(30);
   println!("在匹配前，age是{:?}",age);
    // 尽量不用用同名,避免难以理解, 比如用x
   if let Some(age) = age {
       println!("匹配出来的age是{}",age);
   }x

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

### 5.2 解构Option

```rust
// Option 值的存在; Result 错误的存在
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

// Option 实现了Copy, 会move
let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);

// unwrap解包Some()里包含的值
let six = six.unwrap();
```

### 5.3 全模式列表

#### 5.3.1 匹配字面量

```rust
// 匹配字面量
let x = 1;

match x {
    1 => println!("one"),
    2 => println!("two"),
    3 => println!("three"),
    _ => println!("anything"),
}
```

#### 5.3.2 匹配命名变量

```rust
// 匹配命名变量
fn main() {
    let x = Some(5);
    let y = 10;

    match x {
        Some(50) => println!("Got 50"),
        Some(y) => println!("Matched, y = {:?}", y),
        _ => println!("Default case, x = {:?}", x),
    }

    println!("at the end: x = {:?}, y = {:?}", x, y);
}
```

#### 5.3.3 单分支多模式

```rust
// 单分支多模式
let x = 1;

match x {
    1 | 2 => println!("one or two"),
    3 => println!("three"),
    _ => println!("anything"),
}
```

#### 5.3.4 通过序列 ..= 匹配值的范围

```rust
// 通过序列 ..= 匹配值的范围
let x = 5;

match x {
    1..=5 => println!("one through five"),
    _ => println!("something else"),
}

// 另外一个例子
let x = 'c';

match x {
    'a'..='j' => println!("early ASCII letter"),
    'k'..='z' => println!("late ASCII letter"),
    _ => println!("something else"),
}
```

#### 5.3.5 结构并分解值

##### 解构结构体

```rust
// 解构并分解值
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let p = Point { x: 0, y: 7 };

    let Point { x: a, y: b } = p;
    let Point { x, y } = p;
    assert_eq!(0, a);
    assert_eq!(7, b);
    assert_eq!(0, x);
    assert_eq!(7, y);
}
```

```rust
// 结构匹配
fn main() {
    let p = Point { x: 0, y: 7 };

    match p {
        Point { x, y: 0 } => println!("On the x axis at {}", x),
        Point { x: 0, y } => println!("On the y axis at {}", y),
        Point { x, y } => println!("On neither axis: ({}, {})", x, y),
    }
}
```

##### 解构枚举

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

fn main() {
    let msg = Message::ChangeColor(0, 160, 255);

    match msg {
        Message::Quit => {
            println!("The Quit variant has no data to destructure.")
        }
        Message::Move { x, y } => {
            println!(
                "Move in the x direction {} and in the y direction {}",
                x,
                y
            );
        }
        Message::Write(text) => println!("Text message: {}", text),
        Message::ChangeColor(r, g, b) => {
            println!(
                "Change the color to red {}, green {}, and blue {}",
                r,
                g,
                b
            )
        }
    }
}
```

##### 解构嵌套的结构体和枚举

```rust
enum Color {
   Rgb(i32, i32, i32),
   Hsv(i32, i32, i32),
}

enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(Color),
}

fn main() {
    let msg = Message::ChangeColor(Color::Hsv(0, 160, 255));

    match msg {
        Message::ChangeColor(Color::Rgb(r, g, b)) => {
            println!(
                "Change the color to red {}, green {}, and blue {}",
                r,
                g,
                b
            )
        }
        Message::ChangeColor(Color::Hsv(h, s, v)) => {
            println!(
                "Change the color to hue {}, saturation {}, and value {}",
                h,
                s,
                v
            )
        }
        _ => ()
    }
}
```

##### 解构结构体和元组

```rust
struct Point {
     x: i32,
     y: i32,
 }

let ((feet, inches), Point {x, y}) = ((3, 10), Point { x: 3, y: -10 });
```

##### 解构数组

```rust
// 定长数组
let arr: [u16; 2] = [114, 514];
let [x, y] = arr;

assert_eq!(x, 114);
assert_eq!(y, 514);
```

```rust
// 不定长数组
let arr: &[u16] = &[114, 514];

if let [x, ..] = arr {
    assert_eq!(x, &114);
}

if let &[.., y] = arr {
    assert_eq!(y, 514);
}

let arr: &[u16] = &[];

assert!(matches!(arr, [..]));   // [..] 就是个空数组
assert!(!matches!(arr, [x, ..]));   // x is not in scope
```

#### 5.3.6 忽略模式中的值

##### 使用 _ 忽略整个值

```rust
fn foo(_: i32, y: i32) {
    println!("This code only uses the y parameter: {}", y);
}

fn main() {
    foo(3, 4);
}
```

##### 用 .. 忽略剩余值

```rust
struct Point {
    x: i32,
    y: i32,
    z: i32,
}

let origin = Point { x: 0, y: 0, z: 0 };

match origin {
    Point { x, .. } => println!("x is {}", x),
}
```

#### 5.3.7 匹配守卫提供的额外条件

```rust
let num = Some(4);

match num {
    Some(x) if x < 5 => println!("less than five: {}", x),
    Some(x) => println!("{}", x),
    None => (),
}
```

### 5.4 @绑定

```rust
enum Message {
    Hello { id: i32 },
}

let msg = Message::Hello { id: 5 };

match msg {
    Message::Hello { id: id_variable @ 3..=7 } => {
        println!("Found an id in range: {}", id_variable)
    },
    Message::Hello { id: 10..=12 } => {
        println!("Found an id in another range")
    },
    Message::Hello { id } => {
        println!("Found some other id: {}", id)
    },
}
```

#### 5.4.1 @前绑定后解构(Rust 1.56 新增)

```rust
#[derive(Debug)]
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    // 绑定新变量 `p`，同时对 `Point` 进行解构
    let p @ Point {x: px, y: py } = Point {x: 10, y: 23};
    println!("x: {}, y: {}", px, py);
    println!("{:?}", p);


    let point = Point {x: 10, y: 5};
    if let p @ Point {x: 10, y} = point {
        println!("x is 10 and y is {} in {:?}", y, p);
    } else {
        println!("x was not 10 :(");
    }
}
```

#### 5.4.2 @新特性(Rust 1.53 新增)

```rust
fn main() {
    match 1 {
        num @ (1 | 2) => {
            println!("{}", num);
        }
        _ => {}
    }
}
```
