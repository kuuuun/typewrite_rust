# 3.2 元祖

```rust
let tup:(i32, f64, u8) = (500, 6.4, 1);
```

## 3.2.1 模式匹配解构元祖

```rust
let (x, y, z) = tup;
```

## 3.2.2 用 . 来访问元组

```rust
let five_hundred = tup.0;
let six_point_four = tup.1;
let one = tup.2;
```
