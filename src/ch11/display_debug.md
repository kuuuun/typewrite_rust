# 11.2 {} 与 {:?}

- {} 适用于实现了 std::fmt::Display 特征的类型，用来以更优雅、更友好的方式格式化文本，例如展示给用户
- {:?} 适用于实现了 std::fmt::Debug 特征的类型，用于调试场景

## 11.2.1 Debug特征

```rust
// 对于数值、字符串、数组，可以直接使用 {:?} 进行输出，但是对于结构体，需要派生Debug特征后
#[derive(Debug)]
struct Person {
    name: String,
    age: u8
}

fn main() {
    let i = 3.1415926;
    let s = String::from("hello");
    let v = vec![1, 2, 3];
    let p = Person{name: "sunface".to_string(), age: 18};
    println!("{:?}, {:?}, {:?}, {:?}", i, s, v, p);
}
```

## 11.2.2 Display特征

```rust
// 运行后可以看到 v 和 p 都无法通过编译，因为没有实现 Display 特征，但是你又不能像派生 Debug 一般派生 Display，只能另寻他法：
// - 使用 {:?} 或 {:#?}
// - 为自定义类型实现 Display 特征
// - 使用 newtype 为外部类型实现 Display 特征
let i = 3.1415926;
let s = String::from("hello");
let v = vec![1, 2, 3];
let p = Person {
    name: "sunface".to_string(),
    age: 18,
};
// v和p都不能直接打印
println!("{}, {}, {:?}, {:#?}", i, s, v, p);
```

## 11.2.3 为自定义类型实现Display特征

```rust
struct Person {
    name: String,
    age: u8,
}

use std::fmt;
impl fmt::Display for Person {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(
            f,
            "大佬在上，请受我一拜，小弟姓名{}，年芳{}，家里无田又无车，生活苦哈哈",
            self.name, self.age
        )
    }
}
fn main() {
    let p = Person {
        name: "sunface".to_string(),
        age: 18,
    };
    println!("{}", p);
}
```

## 11.2.4 为外部类型实现Display特征

```rust
struct Array(Vec<i32>);

use std::fmt;
impl fmt::Display for Array {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "数组是：{:?}", self.0)
    }
}
fn main() {
    let arr = Array(vec![1, 2, 3]);
    println!("{}", arr);
}
```
