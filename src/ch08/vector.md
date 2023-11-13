# 8.1 动态数组Vector

## 8.1.1 创建动态数组

### Vec::new

```rust
let v: Vec<i32> = Vec::new();

let mut v = Vec::new();
v.push(1);
```

### `vec![]`

```rust
let v = vec![1, 2, 3];
```

### arr 转 vec

```rust
let arr = [1,3,4];
let vec = arr.to_vec();
```

## 8.1.2 从Vector中读取元素

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

## 8.1.3 下标索引与.get的区别

```rust
let v = vec![1, 2, 3, 4, 5];

let does_not_exist = &v[100];   // 报错
let does_not_exist = v.get(100);    // 有值返回Some, 无值返回None
```

## 8.1.4 同时借用多个数组元素

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

## 8.1.5 迭代遍历Vector中的元素

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

## 8.1.6 存储不同类型的元素

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

## 8.1.7 Vector的排序

```rust
// 稳定的排序 sort 和 sort_by，以及非稳定排序 sort_unstable 和 sort_unstable_by。
// 在 [ 稳定 ] 排序算法里，对相等的元素，不会对其进行重新排序。而在 [ 不稳定 ] 的算法里则不保证这点。
// 总体而言，[ 非稳定 ] 排序的算法的速度会优于 [ 稳定 ] 排序算法，同时，[ 稳定 ] 排序还会额外分配原数组一半的空间。
```

### 整数数组的排序

```rust
fn main() {
    let mut vec = vec![1, 5, 10, 2, 15];    
    vec.sort_unstable();    
    assert_eq!(vec, vec![1, 2, 5, 10, 15]);
}
```

### 浮点数组的排序

```rust
fn main() {
    let mut vec = vec![1.0, 5.6, 10.3, 2.0, 15f32];    
    // vec.sort_unstable();
    vec.sort_unstable_by(|a, b| a.partial_cmp(b).unwrap());    
    assert_eq!(vec, vec![1.0, 2.0, 5.6, 10.3, 15f32]);
}
```

#### partial_cmp

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

### 对结构体数组进行排序

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
