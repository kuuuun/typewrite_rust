# 9. 认识生命周期

<font color="red" size=5 >"引用才有生命周期"</font>

## 9.1 生命周期消除

***三条消除规则***

<font size=1>编译器使用三条消除规则来确定哪些场景不需要显式地去标注生命周期。其中第一条规则应用在输入生命周期上，第二、三条应用在输出生命周期上。若编译器发现三条规则都不适用时，就会报错，提示你需要手动标注生命周期。</font>

- **每一个引用参数都会获得独自的生命周期**

> 例如一个引用参数的函数就有一个生命周期标注: fn foo\<'a>(x: &'a i32)，两个引用参数的
> 有两个生命周期标注:fn foo\<'a, 'b>(x: &'a i32, y: &'b i32), 依此类推。

- **若只有一个输入生命周期(函数参数中只有一个引用类型)，那么该生命周期会被赋给所有的输出生命周期，也就是所有返回值的生命周期都等于该输入生命周期**

> 例如函数 fn foo(x: &i32) -> &i32，x 参数的生命周期会被自动赋给返回值 &i32，
> 因此该函数等同于 fn foo\<'a>(x: &'a i32) -> &'a i32

- **若存在多个输入生命周期，且其中一个是 &self 或 &mut self，则 &self 的生命周期被赋给所有的输出生命周期**

> 拥有 &self 形式的参数，说明该函数是一个 方法，该规则让方法的使用便利度大幅提升。

## 9.2 函数中的生命周期

```rust
fn main() {
    let string1 = String::from("abcd");
    let string2 = "xyz";

    let result = longest(string1.as_str(), string2);
    println!("The longest string is {}", result);
}

// 第一条规则，x和y各有生命周期
// 第二条规则，多个生命周期参数，无法确定返回值的生命周期
// 第三条规则，不是方法，无法确定返回值生命周期
// 不加注：fn longest<'a,'b>(x:&'a str,y:&'b str)->&str{}
//
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    // if 需要返回值，无法判断返回的是x或y，因此要明确生命周期
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

## 9.3 结构体中的生命周期

```rust
// 结构体 ImportantExcerpt 所引用的字符串 str 必须比该结构体活得更久。
struct ImportantExcerpt<'a> {
    part: &'a str,  // 成员为引用类型，须标注
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.').next().expect("Could not find a '.'");
    let i = ImportantExcerpt {
        part: first_sentence,
    };
}
```

```rust
#[derive(Debug)]
struct ImportantExcerpt<'a> {
    part: &'a str,  // 成员为引用类型，须标注
}

fn main() {
    let i;
    {
        let novel = String::from("Call me Ishmael. Some years ago...");
        let first_sentence = novel.split('.').next().expect("Could not find a '.'");
        // 捕获i, 但是first_sentence的生命周期不够
        i = ImportantExcerpt {
            part: first_sentence,
        };
        // first_sentence销毁
    }
    // 可以看出结构体比它引用的字符串活得更久
    println!("{:?}",i);
}
```

## 9.4 方法中的生命周期

```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}

// 需要与结构体中的生命周期保持一致
// 三条规则，方法签名中一般不用标注生命周期
impl<'a> ImportantExcerpt<'a> {
    fn level(&self) -> i32 {
        3
    }
}
```

## 9.5 静态生命周期

```rust
// 'static，拥有该生命周期的引用可以和整个程序活得一样久。
// 'static 引用类型！ const 非引用类型！
let s: &'static str = "我没啥优点，就是活得久，嘿嘿";
```

## 9.6 一个复杂的例子: 泛型，特征约束

```rust
use std::fmt::Display;

fn longest_with_an_announcement<'a, T>( x: &'a str, y: &'a str, ann: T,) -> &'a str
where
    T: Display,
{
    println!("Announcement! {}", ann);
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
fn main(){
    let (str1, str2) = ("long string", "xyz");
    assert_eq!(longest_with_an_announcement(str1, str2, "我是一个提示信息"), str1);
}
```
