# 1. 变量绑定与解构

## 1.1 变量绑定

```rust
let a = 5;
let b:i32 = 5;
let mut c = 6;
let d = 6.23f32;
let d = 6.23_f32;
let e = 100_0000u32;
```

## 1.2 下划线开头忽略使用的变量

```rust
let _x = 5;   // _x 不要净高未使用的变量，也是临时变量
```

## 1.3 变量解构

```rust
let (mut e, f): (bool, bool) = (true, false);
```

## 1.4 解构式赋值

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

## 1.5 变量和常量的区别

没有mut，没有let

```rust
const MAX_POINTS:u32 = 10_0000;
```
