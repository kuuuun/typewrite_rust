# 4. 流程控制

## 4.1 使用if来做分支控制

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

## 4.2 使用`else if`来处理多重条件

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

## 4.3 for循环

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

## 4.4 continue和break

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

## 4.5 while循环

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

## 4.6 loop循环

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
