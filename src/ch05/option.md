# 5.2 解构Option

```rust
// Option - 判断值的存在; Result - 判断错误的存在
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

// NOTE: i32 实现了Copy, 不会move
let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
println!("{:?}", five);

// unwrap解包Some()里包含的值
assert_eq!(six.unwrap(), 6);
```

```rust
// 尝试一些转换
fn plus_one(x: i32) -> i32 {
    let var = Some(x);
    match var {
        None => panic!("None"), // 这就不能返回None了
        Some(i) => i + 1,
    }
}

let five = 5;
let six = plus_one(five);
assert_eq!(six,6);
```
