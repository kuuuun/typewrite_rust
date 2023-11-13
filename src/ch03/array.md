# 3.5 数组

- **速度很快但是长度固定**的 array，称为**数组**。
- **可动态增长的但是有性能损耗**的 Vector，称为**动态数组**。

## 3.5.1 创建数组

```rust
let a = [1, 2, 3, 4, 5];
let a:[i32; 5] = [1, 2, 3, 4, 5];
let a = [3; 5];
```

## 3.5.2 访问数组

```rust
let a = [1, 2, 3, 4, 5];
let first = a[0];
let second = a[1];
```

## 3.5.3 数组元素为非基础类型

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
