# 补充问题

## `&`与`ref`的区别

ref在模式中用于绑定对左值的引用(左值是一个值,您可以使用或多或少的地址).
重要的是要理解模式从正常表达式"向后",因为它们用于解构价值.

这是一个简单的例子.假设我们有这个:

```rust
let value = 42;
```

我们可以通过value两种方式绑定引用:

```rust
let value = 42;

let reference1 = &value;
let ref reference2 = value;
```

在第一种情况下,我们使用&运算符来获取地址value.在第二种情况下,我们使用ref模式来"解构"左值.在这两种情况下,变量的类型都是&i32.

&也可以在模式中使用,但它恰恰相反:它通过解除引用来解构引用.假设我们有:

```rust
let value = 42;
let reference = &value;
```

我们可以reference通过两种方式取消引用:

```rust
let deref1 = *reference;
let &deref2 = reference;
```

在这里,两者的类型deref1和deref2为i32.

但是,并不总是可以通过两种方式编写相同的表达式,如此处所示.
例如,您不能用来&引用存储在枚举变体中的值:您需要匹配它.
例如,如果要引用a中的值Some,则需要编写:

```rust
match option {
    Some(ref value) => { /* stuff */ }
    None => { /* stuff */ }
}
```

因为在Rust中无法使用&运算符来访问该值.
