# 3.4 枚举

## 3.4.1 枚举语法

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

## 3.4.2 Option枚举用于处理空值

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
