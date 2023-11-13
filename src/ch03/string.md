# 3.1 字符串

## 3.1.1 字符串和切片(字符串字面量)

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

## 3.1.2 操作字符串

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

## 3.1.3 操作UTF-8字符串

```rust
for c in "中国人".chars(){}
```
