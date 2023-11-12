# Typewrite of Rust

## 1. 变量绑定与解构

### 1.1 变量绑定

```rust
let a = 5;
let b:i32 = 5;
let mut c = 6;
```

### 1.2 下划线开头忽略使用的变量

```rust
let _x = 5;   // _x 不要净高未使用的变量，也是临时变量
```

### 1.3 变量解构

```rust
let (mut e, f): (bool, bool) = (true, false);
```

### 1.4 解构式赋值

**`+=`** 不支持

```rust
struct Struct {
  e:i32
}
fn main() {
  let (a, b, c, d, e);
  (a, b) = (1, 2);
  [_, c, .., d, _] = [1,2,3,4,5];
  Struct {e,..} = Struct {e: 5};

  assert_eq!([1,2,2,4,5], [a,b,c,d,e]);
}
```

### 1.5 变量和常量的区别

没有mut，没有let

```rust
const MAX_POINTS:u32 = 10_0000;
```

## 2. 基本类型

- 数值类型:
  - **有符号整数** (i8, i16, i32, i64, isize)
  - **无符号整数** (u8, u16, u32, u64, usize)
  - **浮点数** (f32, f64)
  - 有理数
  - 复数
- **字符串**：
  - 字符串字面量
  - 字符串切片 &str
- 布尔类型： true和false
- 字符类型: 表示单个 Unicode 字符，存储为 4 个字节
- 单元类型: 即 () ，其唯一的值也是 ()

### 2.1 函数

```rust
fn add(i:i32,j:i32) -> i32{
  i + j
}
```

## 3. 复合类型

### 3.1 字符串

#### 3.1.1 字符串和切片(字符串字面量)

```rust
// 中文在 UTF-8 中占用三个字节
// 切片的本质是指针
let s = String::from("hello world");
let sentence = String::from("你好,世界");
let a = [1,2,3,4,5];

let hello = &s[0..5];
let world = &s[6..11];

assert_eq!(&s[0..5], &s[..5]);
assert_eq!(&s, &s[..]);
assert!(&s[0..6]);  // 需要是3的倍数，否则报错
assert_eq!(&a[1..3], [2, 3]);
```

#### 3.1.2 操作字符串

```rust
s.push_str("追加字符串");
s.insert(5,'插入字符');
s.replace("rust", "RUST");
s.replacen("rust", "RUST", 1);  // index
s.replace_range(0..5, "RUST");

// 删除字符串
s.pop();    // 从后往前pop
s.remove(0);    // index

s.truncate(5);  // 从指定位置到结尾，截断
s.clear();      // 清空
let s = "hello".to_string()+" "+"world";    
// String = String1 + &str + &str... String1所有权转移！
```

#### 3.1.3 操作UTF-8字符串

```rust
for c in "中国人".chars(){}
```

### 3.2 元祖

```rust
let tup:(i32, f64, u8) = (500, 6.4, 1);
```

#### 3.2.1 模式匹配解构元祖

```rust
let (x, y, z) = tup;
```

#### 3.2.2 用 . 来访问元组

```rust
let five_hundred = tup.0;
let six_point_four = tup.1;
let one = tup.2;
```

### 3.3 结构体

#### 3.3.1 结构体语法

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

#### 3.3.2 元祖结构体

```rust
struct Color(i32, i32, i32);
let black = Color(0, 0, 0);
```

#### 3.3.3 单元结构体

```rust
struct AlwaysEqual;

let subject = AlwaysEqual;

// 我们不关心 AlwaysEqual 的字段数据，只关心它的行为，因此将它声明为单元结构体，然后再为它实现某个特征
impl SomeTrait for AlwaysEqual {}
```

#### 3.3.4 结构体数据的所有权

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

#### 3.3.5 使用`#[derive(Debug)]` 来打印结构体的信息

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

### 3.4 枚举

#### 3.4.1 枚举语法

```rust
enum PokerSuit {
  Clubs,
  Spades,
  Diamonds,
  Hearts,
}

let heart = PokerSuit::Hearts;
let diamond = PokerSuit::Diamonds;
```

```rust
// 用struct的带值的枚举
enum PokerSuit {
    Clubs,
    Spades,
    Diamonds,
    Hearts,
}

struct PokerCard {
    suit: PokerSuit,
    value: u8
}

fn main() {
   let c1 = PokerCard {
       suit: PokerSuit::Clubs,
       value: 1,
   };
   let c2 = PokerCard {
       suit: PokerSuit::Diamonds,
       value: 12,
   };
}
```

```rust
// 带值的枚举，优于上面代码
enum PokerCard {
    Clubs(u8),
    Spades(u8),
    Diamonds(u8),
    Hearts(u8),
}

fn main() {
   let c1 = PokerCard::Spades(5);
   let c2 = PokerCard::Diamonds(13);
}
```

#### 3.4.2 Option枚举用于处理空值

```rust
enum Option<T> {
    Some(T),
    None,
}
```

```rust
// 因为 Option，Some，None 都包含在 prelude 中，因此你可以直接通过名称来使用它们，
// 而无需以 Option::Some 这种形式去使用，总之，千万不要因为调用路径变短了，
// 就忘记 Some 和 None也是 Option 底下的枚举成员！
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

### 3.5 数组

- **速度很快但是长度固定**的 array，称为**数组**。
- **可动态增长的但是有性能损耗**的 Vector，称为**动态数组**。

#### 3.5.1 创建数组

```rust
let a = [1, 2, 3, 4, 5];
let a:[i32; 5] = [1, 2, 3, 4, 5];
let a = [3; 5];
```

#### 3.5.2 访问数组

```rust
let a = [1, 2, 3, 4, 5];
let first = a[0];
let second = a[1];
```

#### 3.5.3 数组元素为非基础类型

```rust
// String 未实现 Copy, 不能实现[String; 8]的赋值方式
// 须使用 std::array::from_fn
let array: [String; 8] = std::array::from_fn(|_i| String::from("rust is good!"));
println!("{:#?}", array);
```

```rust
fn main() {
  // 编译器自动推导出one的类型
  let one             = [1, 2, 3];
  // 显式类型标注
  let two: [u8; 3]    = [1, 2, 3];
  let blank1          = [0; 3];
  let blank2: [u8; 3] = [0; 3];

  // arrays是一个二维数组，其中每一个元素都是一个数组，元素类型是[u8; 3]
  let arrays: [[u8; 3]; 4]  = [one, two, blank1, blank2];

  // 借用arrays的元素用作循环中
  for a in &arrays {
    print!("{:?}: ", a);
    // 将a变成一个迭代器，用于循环
    // 你也可以直接用for n in a {}来进行循环
    for n in a.iter() {
      print!("\t{} + 10 = {}", n, n+10);
    }

    let mut sum = 0;
    // 0..a.len,是一个 Rust 的语法糖，其实就等于一个数组，元素是从0,1,2一直增加到到a.len-1
    // 可以用for_each来进行循环 a.iter().for_each(|n| sum += n);
    for i in 0..a.len() {
      sum += a[i];
    }
    println!("\t({:?} = {})", a, sum);
  }
}
```

## 4. 流程控制

### 4.1 使用if来做分支控制

```rust
fn main() {
    let condition = true;
    let number = if condition {
        5
    } else {
        6
    };

    println!("The value of number is: {}", number);
}
```

### 4.2 使用`else if`来处理多重条件

```rust
fn main() {
    let n = 6;

    if n % 4 == 0 {
        println!("number is divisible by 4");
    } else if n % 3 == 0 {
        println!("number is divisible by 3");
    } else if n % 2 == 0 {
        println!("number is divisible by 2");
    } else {
        println!("number is not divisible by 4, 3, or 2");
    }
}
```

### 4.3 for循环

```rust
// 如果还想继续使用container，可以使用&和&mut
for item in &container {
  // ...
}

for item in &mut collection {
  // ...
}
```

```rust
// 获得数组中每个元素的索引
fn main() {
    let a = [4, 3, 2, 1];
    // `.iter()` 方法把 `a` 数组变成一个迭代器
    for (i, v) in a.iter().enumerate() {
        println!("第{}个元素是{}", i + 1, v);
    }
}
```

```rust
// 运行10次
for _ in 0..10 {
  // ...
}
```

### continue和break

```rust
// 使用 continue 可以跳过当前当次的循环，开始下次的循环：
 for i in 1..4 {
     if i == 2 {
         continue;
     }
     println!("{}", i); // output: 1 3
 }
```

```rust
// 使用 break 可以直接跳出当前整个循环：
 for i in 1..4 {
     if i == 2 {
         break;
     }
     println!("{}", i); // output: 1
 }
```

### while循环

```rust
// 如果你需要一个条件来循环，当该条件为 true 时，继续循环，条件为 false，跳出循环，那么 while 就非常适用：
fn main() {
    let mut n = 0;

    while n <= 5  {
        println!("{}!", n);

        n = n + 1;
    }

    println!("我出来了！");
}
```

### loop循环

```rust
fn main() {
    let mut counter = 0;

    // loop 是一个表达式，因此可以返回一个值
    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;  // break 可以单独使用，也可以带一个返回值，有些类似 return
        }
    };

    println!("The result is {}", result);
}
```

## 模式匹配

### match和if let

#### 匹配模式: match和if let

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

#### 使用 match 表达式赋值

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

#### 模式绑定

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

#### 穷尽匹配

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

#### if let匹配

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

#### matches!宏

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

#### 变量遮蔽

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

### 解构Option

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

### 全模式列表

#### 匹配字面量

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

#### 匹配命名变量

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

#### 单分支多模式

```rust
// 单分支多模式
let x = 1;

match x {
    1 | 2 => println!("one or two"),
    3 => println!("three"),
    _ => println!("anything"),
}
```

#### 通过序列 ..= 匹配值的范围

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

#### 结构并分解值

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

#### 忽略模式中的值

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

#### 匹配守卫提供的额外条件

```rust
let num = Some(4);

match num {
    Some(x) if x < 5 => println!("less than five: {}", x),
    Some(x) => println!("{}", x),
    None => (),
}
```

### @绑定

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

#### @前绑定后解构(Rust 1.56 新增)

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

#### @新特性(Rust 1.53 新增)

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

## 方法Method

### 定义方法

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

### 带有多个参数的方法

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

### 关联函数

```rust
impl Rectangle {
    // 构造函数，可简化结构体初始化
    fn new(w: u32, h: u32) -> Rectangle {
        Rectangle { width: w, height: h }
    }
}
```

### 多个impl定义

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

### 为枚举实现方法

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

## 泛型和特征

### 泛型Generics

```rust
// 泛型会增加编译时间，增加最后编译文件的大小。
```

#### 泛型详解

```rust
// 函数 largest 有泛型类型 T，它有个参数 list，其类型是元素为 T 的数组切片，最后，该函数返回值的类型也是 T。
fn largest<T>(list: &[T]) -> T {
```

```rust
// where 的用法
fn largest<T>(list: &[T]) -> T
where T: PartialOrd + Copy {
    let mut largest = list[0];  // 增加了Copy trait, 允许复制

    for &item in list {     // list是一个数组切片，&item是一个元素
        if item > largest {     // 增加了PartialOrd trait，允许比较
            largest = item;     // item的类型是T，可以复制
        }
    }
    largest
}

fn main() {
    let number_list = vec![34, 50, 25, 100, 65];

    let result = largest(&number_list);
    println!("The largest number is {}", result);

    let char_list = vec!['y', 'm', 'a', 'q'];

    let result = largest(&char_list);
    println!("The largest char is {}", result);
}
```

#### 结构体的泛型

```rust
struct Point<T,U> {
    x: T,
    y: U,
}
fn main() {
    let p = Point{x: 1, y :1.1};
}
```

#### 枚举的泛型

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

#### 方法的泛型

```rust
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

fn main() {
    let p = Point { x: 5, y: 10 };

    println!("p.x = {}", p.x());
}
```

```rust
struct Point<T, U> {
    x: T,
    y: U,
}

impl<T, U> Point<T, U> {
    fn mixup<V, W>(self, other: Point<V, W>) -> Point<T, W> {
        Point {
            x: self.x,
            y: other.y,
        }
    }
}

fn main() {
    let p1 = Point { x: 5, y: 10.4 };
    let p2 = Point { x: "Hello", y: 'c'};

    let p3 = p1.mixup(p2);

    println!("p3.x = {}, p3.y = {}", p3.x, p3.y);
}
```

##### 为具体的泛型类型实现方法

```rust
impl Point<f32> {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}
```

#### const 泛型（Rust 1.51 版本引入的重要特性）

```rust
// 用数组切片的方式，可以实现 const 泛型
fn display_array<T: std::fmt::Debug>(arr: &[T]) {
    println!("{:?}", arr);
}
fn main() {
    let arr: [i32; 3] = [1, 2, 3];
    display_array(&arr);

    let arr: [i32;2] = [1,2];
    display_array(&arr);
}
```

```rust
// const 泛型，针对usize
fn display_array<T: std::fmt::Debug, const N: usize>(arr: [T; N]) {
    println!("{:?}", arr);
}
fn main() {
    let arr: [i32; 3] = [1, 2, 3];
    display_array(arr);

    let arr: [i32; 2] = [1, 2];
    display_array(arr);
}
```

### 特征trait

```rust
fn add<T: std::ops::Add<Output = T>>(a:T, b:T) -> T {
    a + b
}
```

#### 定义特征

```rust
pub trait Summary {
    fn summarize(&self) -> String;
}
```

#### 为类型实现特征

```rust
pub trait Summary {
    fn summarize(&self) -> String;
}
pub struct Post {
    pub title: String, // 标题
    pub author: String, // 作者
    pub content: String, // 内容
}

impl Summary for Post {
    fn summarize(&self) -> String {
        format!("文章{}, 作者是{}", self.title, self.author)
    }
}

pub struct Weibo {
    pub username: String,
    pub content: String
}

impl Summary for Weibo {
    fn summarize(&self) -> String {
        format!("{}发表了微博{}", self.username, self.content)
    }
}

fn main() {
    let post = Post{title: "Rust语言简介".to_string(),author: "Sunface".to_string(), content: "Rust棒极了!".to_string()};
    let weibo = Weibo{username: "sunface".to_string(),content: "好像微博没Tweet好用".to_string()};

    println!("{}",post.summarize());
    println!("{}",weibo.summarize());
}
```

#### 使用特征作为函数参数

```rust
pub fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}
```

#### 特征约束(trait bound)

```rust
// 标准trait bound, 上面的代码impl Summary 是语法糖
pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}
```

```rust
pub fn notify(item1: &impl Summary, item2: &impl Summary) {}
// 两个参数实现不同的特征，用这种方式实现，比较好。
```

```rust
pub fn notify<T: Summary>(item1: &T, item2: &T) {}
// 如果是多参数实现相同的特征，用这种。
```

##### 多重约束

```rust
pub fn notify(item: &(impl Summary + Display)) {}
// 除了上面的语法糖，还可以使用特征约束
pub fn notify<T: Summary + Display>(item: &T) {}
```

##### where约束

```rust
fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {}
```

```rust
fn some_function<T, U>(t: &T, u: &U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
{}
```

##### 使用特征约束有条件地实现方法或特征

```rust
use std::fmt::Display;

struct Pair<T> {
    x: T,
    y: T,
}

impl<T> Pair<T> {
    fn new(x: T, y: T) -> Self {
        Self {
            x,
            y,
        }
    }
}

impl<T: Display + PartialOrd> Pair<T> {
    fn cmp_display(&self) {
        if self.x >= self.y {
            println!("The largest member is x = {}", self.x);
        } else {
            println!("The largest member is y = {}", self.y);
        }
    }
}
```

#### 函数返回中的 impl Trait

```rust
// 但是这种返回值方式有一个很大的限制：只能有一个具体的类型
fn returns_summarizable() -> impl Summary {
    Weibo {
        username: String::from("sunface"),
        content: String::from(
            "m1 max太厉害了，电脑再也不会卡",
        )
    }
}
```

#### 几个综合例子

##### 为自定义类型实现 + 操作

```rust
use std::ops::Add;

// 为Point结构体派生Debug特征，用于格式化输出
#[derive(Debug)]
struct Point<T: Add<T, Output = T>> { //限制类型T必须实现了Add特征，否则无法进行+操作。
    x: T,
    y: T,
}

impl<T: Add<T, Output = T>> Add for Point<T> {
    type Output = Point<T>;

    fn add(self, p: Point<T>) -> Point<T> {
        Point{
            x: self.x + p.x,
            y: self.y + p.y,
        }
    }
}

fn add<T: Add<T, Output=T>>(a:T, b:T) -> T {
    a + b
}

fn main() {
    let p1 = Point{x: 1.1f32, y: 1.1f32};
    let p2 = Point{x: 2.1f32, y: 2.1f32};
    println!("{:?}", add(p1, p2));

    let p3 = Point{x: 1i32, y: 1i32};
    let p4 = Point{x: 2i32, y: 2i32};
    println!("{:?}", add(p3, p4));
}
```

##### 自定义类型的打印输出

```rust
#![allow(dead_code)]

use std::fmt;
use std::fmt::{Display};

#[derive(Debug,PartialEq)]
enum FileState {
  Open,
  Closed,
}

#[derive(Debug)]
struct File {
  name: String,
  data: Vec<u8>,
  state: FileState,
}

impl Display for FileState {
   fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
     match *self {
         FileState::Open => write!(f, "OPEN"),
         FileState::Closed => write!(f, "CLOSED"),
     }
   }
}

impl Display for File {
   fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
      write!(f, "<{} ({})>",
             self.name, self.state)
   }
}

impl File {
  fn new(name: &str) -> File {
    File {
        name: String::from(name),
        data: Vec::new(),
        state: FileState::Closed,
    }
  }
}

fn main() {
  let f6 = File::new("f6.txt");
  //...
  println!("{:?}", f6);
  println!("{}", f6);
}
```

## 集合类型

### 动态数组Vector

#### 创建动态数组

##### Vec::new

```rust
let v: Vec<i32> = Vec::new();

let mut v = Vec::new();
v.push(1);
```

##### `vec![]`

```rust
let v = vec![1, 2, 3];
```

#### 从Vector中读取元素

```rust
let v = vec![1, 2, 3, 4, 5];

// 通过下标
let third: &i32 = &v[2];
println!("第三个元素是 {}", third);

// 通过get方法
match v.get(2) {
    Some(third) => println!("第三个元素是 {third}"),
    None => println!("去你的第三个元素，根本没有！"),
}
```

#### 下标索引与.get的区别

```rust
let v = vec![1, 2, 3, 4, 5];

let does_not_exist = &v[100];   // 报错
let does_not_exist = v.get(100);    // 有值返回Some, 无值返回None
```

#### 同时借用多个数组元素

```rust
// 原因在于：数组的大小是可变的，当旧数组的大小不够用时，Rust 会重新分配一块更大的内存空间，然后把旧数组拷贝过来。
// 这种情况下，之前的引用显然会指向一块无效的内存，这非常 rusty —— 对用户进行严格的教育。
//
// 不可变借用可以n个，可变借用只能有一个
let mut v = vec![1, 2, 3, 4, 5];

// 不可变借用
let first = &v[0];

// 可变借用
v.push(6);

// 又一次使用first，报错！
println!("The first element is: {first}");
```

#### 迭代遍历Vector中的元素

```rust
let v = vec![1, 2, 3];
// 遍历不可变借用v
for i in &v {
    println!("{i}");
}
```

```rust
// 遍历修改Vector
let mut v = vec![1, 2, 3];
// 遍历可变借用v，因此i是可变借用，类型为&mut i32, 不支持+=
// 需要deref
for i in &mut v {
    *i += 10
}
```

#### 存储不同类型的元素

```rust
// 数组的元素必须类型相同。
// 解决方案：通过枚举类型和特征类型实现不同元素的存储。
// 枚举类型的实现：
#[derive(Debug)]
enum IpAddr {
    V4(String),
    V6(String)
}
fn main() {
    let v = vec![
        IpAddr::V4("127.0.0.1".to_string()),
        IpAddr::V6("::1".to_string())
    ];

    for ip in v {
        show_addr(ip)
    }
}

fn show_addr(ip: IpAddr) {
    println!("{:?}",ip);
}
```

```rust
trait IpAddr {
    fn display(&self);
}

struct V4(String);
impl IpAddr for V4 {
    fn display(&self) {
        println!("ipv4: {:?}",self.0)
    }
}
struct V6(String);
impl IpAddr for V6 {
    fn display(&self) {
        println!("ipv6: {:?}",self.0)
    }
}

fn main() {
    // 将实现了特征 IpAddr 的实例，用Box::new()包装
    // 这里必须手动指定类型：Vec<Box<dyn IpAddr>>，表示数组 v 存储的是特征 IpAddr 的对象
    let v: Vec<Box<dyn IpAddr>> = vec![
        Box::new(V4("127.0.0.1".to_string())),
        Box::new(V6("::1".to_string())),
    ];

    for ip in v {
        ip.display();
    }
}
```

#### Vector的排序

```rust
// 稳定的排序 sort 和 sort_by，以及非稳定排序 sort_unstable 和 sort_unstable_by。
// 在 [ 稳定 ] 排序算法里，对相等的元素，不会对其进行重新排序。而在 [ 不稳定 ] 的算法里则不保证这点。
// 总体而言，[ 非稳定 ] 排序的算法的速度会优于 [ 稳定 ] 排序算法，同时，[ 稳定 ] 排序还会额外分配原数组一半的空间。
```

##### 整数数组的排序

```rust
fn main() {
    let mut vec = vec![1, 5, 10, 2, 15];    
    vec.sort_unstable();    
    assert_eq!(vec, vec![1, 2, 5, 10, 15]);
}
```

##### 浮点数组的排序

```rust
fn main() {
    let mut vec = vec![1.0, 5.6, 10.3, 2.0, 15f32];    
    // vec.sort_unstable();
    vec.sort_unstable_by(|a, b| a.partial_cmp(b).unwrap());    
    assert_eq!(vec, vec![1.0, 2.0, 5.6, 10.3, 15f32]);
}
```

###### partial_cmp

```rust
// 如果存在，则返回self和other之间的顺序
use std::cmp::Ordering;

let result = 1.0.partial_cmp(&2.0);
assert_eq!(result, Some(Ordering::Less));

let result = 1.0.partial_cmp(&1.0);
assert_eq!(result, Some(Ordering::Equal));

let result = 2.0.partial_cmp(&1.0);
assert_eq!(result, Some(Ordering::Greater));

// 如果不存在，则返回None
let result = f64::NAN.partial_cmp(&1.0);
assert_eq!(result, None);
```

##### 对结构体数组进行排序

```rust
#[derive(Debug)]
struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: String, age: u32) -> Person {
        Person { name, age }
    }
}

fn main() {
    let mut people = vec![
        Person::new("Zoe".to_string(), 25),
        Person::new("Al".to_string(), 60),
        Person::new("John".to_string(), 1),
    ];
    // 定义一个按照年龄倒序排序的对比函数
    people.sort_unstable_by(|a, b| b.age.cmp(&a.age));

    println!("{:?}", people);
}
```

```rust
// 实现 Ord 需要我们实现 Ord、Eq、PartialEq、PartialOrd 这些属性。好消息是，你可以 derive 这些属性：
#[derive(Debug, Ord, Eq, PartialEq, PartialOrd)]
struct Person {
    name: String,
    age: u32,
}

impl Person {
    fn new(name: String, age: u32) -> Person {
        Person { name, age }
    }
}

fn main() {
    let mut people = vec![
        Person::new("Zoe".to_string(), 25),
        Person::new("Al".to_string(), 60),
        Person::new("Al".to_string(), 30),
        Person::new("John".to_string(), 1),
        Person::new("John".to_string(), 25),
    ];

    people.sort_unstable();

    println!("{:?}", people);
}
// 需要 derive Ord 相关特性，需要确保你的结构体中所有的属性均实现了 Ord 相关特性，否则会发生编译错误。
// derive 的默认实现会依据属性的顺序依次进行比较，如上述例子中，当 Person 的 name 值相同，则会使用 age 进行比较。
```

### KV存储Hashmap

#### 创建Hashmap

##### 使用new方法

```rust
use std::collections::HashMap;

// 创建一个HashMap，用于存储宝石种类和对应的数量
let mut my_gems = HashMap::new();

// 该Hashmap的类型HashMap<&str,i32>
// 将宝石类型和对应的数量写入表中
my_gems.insert("红宝石", 1);
my_gems.insert("蓝宝石", 2);
my_gems.insert("河边捡的误以为是宝石的破石头", 18);
```

##### 使用迭代器和collect方法创建

```rust
fn main() {
    use std::collections::HashMap;

    let teams_list = vec![
        ("中国队".to_string(), 100),
        ("美国队".to_string(), 10),
        ("日本队".to_string(), 50),
    ];

    let mut teams_map = HashMap::new();
    for team in &teams_list {
        // teams_list<vec>的第一项是String, HashMap要求是&str, 因此要用&team.0
        teams_map.insert(&team.0, team.1);
    }

    println!("{:?}",teams_map)
}


```

```rust
fn main() {
    use std::collections::HashMap;

    let teams_list = vec![
        ("中国队".to_string(), 100),
        ("美国队".to_string(), 10),
        ("日本队".to_string(), 50),
    ];

    // into_iter()
    // collect() 将迭代器转换为集合
    let teams_map: HashMap<_,_> = teams_list.into_iter().collect();
    
    println!("{:?}",teams_map)
}
```

```rust
let a = [1, 2, 3];

let doubled: Vec<i32> = a.iter()
                         .map(|&x| x * 2)
                         .collect();

assert_eq!(vec![2, 4, 6], doubled);
```

#### 所有权转移
```rust
fn main() {
    use std::collections::HashMap;

    let name = String::from("Sunface");
    let age = 18;

    let mut handsome_boys = HashMap::new();
    // String 没有实现copy特性,转移所有权
    // 需要复制name, 或者引用，如果引用，就要注意name的lifetime
    handsome_boys.insert(name, age);

    println!("因为过于无耻，{}已经被从帅气男孩名单中除名", name);   // name borrowed
    println!("还有，他的真实年龄远远不止{}岁", age);
}
```

```rust
fn main() {
    use std::collections::HashMap;

    let name = String::from("Sunface");
    let age = 18;

    let mut handsome_boys = HashMap::new();
    // &name 实现了copy特性
    handsome_boys.insert(&name, age);

    std::mem::drop(name);   // 引用的话，要保证所有权比hashmap活的久
    println!("因为过于无耻，{:?}已经被除名", handsome_boys);
    println!("还有，他的真实年龄远远不止{}岁", age);
}
```


#### 查询hashmap

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

let team_name = String::from("Blue");
// get方法获取值
let score: Option<&i32> = scores.get(&team_name);
```

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

// 遍历
for (key, value) in &scores {
    println!("{}: {}", key, value);
}
```

#### 更新hashmap

```rust
fn main() {
    use std::collections::HashMap;

    let mut scores = HashMap::new();

    scores.insert("Blue", 10);

    // insert() 无键返回None, 存在键值, 更新值，返回旧值。键值不更新。
    // 覆盖已有的值
    let old = scores.insert("Blue", 20);
    assert_eq!(old, Some(10));

    // 查询新插入的值
    let new = scores.get("Blue");
    assert_eq!(new, Some(&20));

    // 查询Yellow对应的值，若不存在则插入新值
    let v = scores.entry("Yellow").or_insert(5);
    assert_eq!(*v, 5); // 不存在，插入5

    // 查询Yellow对应的值，若不存在则插入新值
    let v = scores.entry("Yellow").or_insert(50);
    assert_eq!(*v, 5); // 已经存在，因此50没有插入
}
```

```rust
#![allow(unused)]
fn main() {
use std::collections::HashMap;

let text = "hello world wonderful world";

let mut map = HashMap::new();
// 根据空格来切分字符串(英文单词都是通过空格切分)
for word in text.split_whitespace() {
    let count = map.entry(word).or_insert(0); // or_insert() 值不存在则插入
    *count += 1;
}

println!("{:?}", map);
}
```
```rust
use std::collections::HashMap;

let mut map: HashMap<&str, u32> = HashMap::new();

// and_modify() 在任何潜在的插入 map 之前，提供对占用条目的就地可变访问。
map.entry("poneyland")
   .and_modify(|e| { *e += 1 })
   .or_insert(42);
assert_eq!(map["poneyland"], 42);

map.entry("poneyland")
   .and_modify(|e| { *e += 1 })
   .or_insert(42);
assert_eq!(map["poneyland"], 43);
```

### 认识生命周期

