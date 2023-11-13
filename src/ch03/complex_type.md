# 3. 复合类型

## 3.1 字符串

### 3.1.1 字符串和切片(字符串字面量)

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

### 3.1.2 操作字符串

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

### 3.1.3 操作UTF-8字符串

```rust
for c in "中国人".chars(){}
```

## 3.2 元祖

```rust
let tup:(i32, f64, u8) = (500, 6.4, 1);
```

### 3.2.1 模式匹配解构元祖

```rust
let (x, y, z) = tup;
```

### 3.2.2 用 . 来访问元组

```rust
let five_hundred = tup.0;
let six_point_four = tup.1;
let one = tup.2;
```

## 3.3 结构体

### 3.3.1 结构体语法

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

### 3.3.2 元祖结构体

```rust
struct Color(i32, i32, i32);
let black = Color(0, 0, 0);
```

### 3.3.3 单元结构体

```rust
struct AlwaysEqual;

let subject = AlwaysEqual;

// 我们不关心 AlwaysEqual 的字段数据，只关心它的行为，因此将它声明为单元结构体，然后再为它实现某个特征
impl SomeTrait for AlwaysEqual {}
```

### 3.3.4 结构体数据的所有权

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

### 3.3.5 使用`#[derive(Debug)]` 来打印结构体的信息

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

## 3.4 枚举

### 3.4.1 枚举语法

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

### 3.4.2 Option枚举用于处理空值

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

## 3.5 数组

- **速度很快但是长度固定**的 array，称为**数组**。
- **可动态增长的但是有性能损耗**的 Vector，称为**动态数组**。

### 3.5.1 创建数组

```rust
let a = [1, 2, 3, 4, 5];
let a:[i32; 5] = [1, 2, 3, 4, 5];
let a = [3; 5];
```

### 3.5.2 访问数组

```rust
let a = [1, 2, 3, 4, 5];
let first = a[0];
let second = a[1];
```

### 3.5.3 数组元素为非基础类型

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
