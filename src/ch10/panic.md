# 10. 错误处理

- 如果是 main 线程，则程序会终止，如果是其它子线程，该线程会终止，但是不会影响 main 线程。\
  因此，尽量不要在 main 线程中做太多任务，将这些任务交由子线程去做，就算子线程 panic 也不会导致整个程序的结束。

## 10.1 `panic`

### 10.1.1 主动调用

```rust
fn main(){
    panic!("crash and burn");
}
```

### 10.1.2 何时使用`panic!`

有害状态大概分为几类：

- 非预期的错误
- 后续代码的运行会受到显著影响
- 内存安全的问题

```rust
// 确切知道不可能panic! 可以使用unwrap()
use std::net::IpAddr;
let home: IpAddr = "127.0.0.1".parse().unwrap();
```

## 10.2 可恢复错误 Result

```rust
// 报错
let f: u32 = File::open("hello.txt");
```

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => {
            panic!("Problem opening the file: {:?}", error)
        },
    };
}
```

### 10.2.1 对返回的错误进行处理

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => match error.kind() {
            ErrorKind::NotFound => match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("Problem creating the file: {:?}", e),
            },
            other_error => panic!("Problem opening the file: {:?}", other_error),
        },
    };
}
```

### 10.2.2 失败就panic: unwrap 和 expect

```rust
// expect会自带提示信息，相当于重载了错误打印函数。
#![allow(unused_variables)]
use std::fs::File;

fn main() {
    let f = File::open("hello.txt").expect("Failed to open hello.txt");
}
```

### 10.2.3 错误传播

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    // 打开文件，f是`Result<文件句柄,io::Error>`
    let f = File::open("hello.txt");

    let mut f = match f {
        // 打开文件成功，将file句柄赋值给f
        Ok(file) => file,
        // 打开文件失败，将错误返回(向上传播)
        Err(e) => return Err(e),
    };
    // 创建动态字符串s
    let mut s = String::new();
    // 从f文件句柄读取数据并写入s中
    match f.read_to_string(&mut s) {
        // 读取成功，返回Ok封装的字符串
        Ok(_) => Ok(s),
        // 将错误向上传播
        Err(e) => Err(e),
    }
}
fn main() {
    println!("{:?}", read_username_from_file());
}
```

#### 传播界的大明星`?`

```rust
use std::fs::File;
use std::io;
use std::io::Read;

fn read_username_from_file() -> Result<String, io::Error> {
    // open()返回Result
    let mut f = File::open("hello.txt")?;
    let mut s = String::new();
    // read_to_string()返回Result
    f.read_to_string(&mut s)?;
    Ok(s)
}
fn main() {
    println!("{:?}", read_username_from_file());
}
```

```rust
use std::fs::File;
use std::io;
use std::io::Read;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();

    File::open("hello.txt")?.read_to_string(&mut s)?;

    Ok(s)
}
fn main() {
    println!("{:?}", read_username_from_file());
}
```

```rust
use std::fs;
use std::io;

fn read_username_from_file() -> Result<String, io::Error> {
    // read_to_string是定义在std::io中的方法，因此需要在上面进行引用
    fs::read_to_string("hello.txt")
}
fn main() {
    println!("{:?}", read_username_from_file());
}
```

#### 用于Option的返回

```rust
// slice::get()方法
// 根据索引的类型返回对元素或子切片的引用。
// - 如果给定位置，则返回该位置上的元素的**引用**，如果越界则返回 None。
// - 如果给定范围，则返回对应于该范围的子切片; 如果越界，则返回 None。
#![allow(unused)]
fn main() {
    let v = [10, 40, 30];
    assert_eq!(Some(&40), v.get(1));
    assert_eq!(Some(&[10, 40][..]), v.get(0..2));
    assert_eq!(None, v.get(3));
    assert_eq!(None, v.get(0..4));
}
```

```rust
fn first(arr: &[i32]) -> Option<&i32> {
   let v = arr.get(0)?;
   Some(v)
}
```

```rust
fn first(arr: &[i32]) -> Option<&i32> {
   arr.get(0)
}
```

```rust
let text = "hello";
fn last_char_of_first_line(text: &str) -> Option<char> {
    // lines() &str整行的迭代器
    // next() 迭代器下一个，也就是第一个
    // chars() 返回字符串切片的char上的迭代器
    // last() 消耗迭代器，返回最后一个元素
    // nth() 消耗迭代器，返回第n个元素，正数从0开始
    text.lines().next()?.chars().last();
    text.lines().next()?.chars().nth(0)
}
println!("{:?}",last_char_of_first_line(text));
```

#### 新手用?常会犯的错误

```rust
// 切记 `?` 操作符需要一个变量来承载正确的值
// get函数只会返回Some(&i32)或者None，只有错误值能返回，正确的不行。
// 如果数组中存在0号元素，那么函数第二行使用 ? 后的返回类型为 &i32 而不是 Some(&i32)。
// - let v = xxx()?;
// - xxx()?.yyy()?;
fn first(arr: &[i32]) -> Option<&i32> {
    // 错误的形式，注意get返回值;
    arr.get(0)?
}
```

#### 带返回值的main函数

```rust
// ?要求Result<T,E>形式的返回值
// 原则上，？用在function内
#![allow(unused_variables)]
use std::error::Error;
use std::fs::File;

// Box<dyn Error> 特征对象，因为 std::error:Error 是 Rust 中抽象层次最高的错误，
// 其它标准库中的错误都实现了该特征，因此我们可以用该特征对象代表一切错误，就算 main 函数中调用任何标准库函数发生错误，
// 都可以通过 Box<dyn Error> 这个特征对象进行返回。
fn main() -> Result<(), Box<dyn Error>> {
    let f = File::open("hello.txt")?;

    Ok(())
}
```
