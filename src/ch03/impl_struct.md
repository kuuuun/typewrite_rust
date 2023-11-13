# impl 结构体

```rust
use std::fmt;
#[derive(Copy, Clone)]
struct Point{
    x:i32,
    y:i32,
}

impl Point{
    fn new(x:i32,y:i32)->Self{
        Point{x,y}
    }

    fn x(&mut self, x:i32){
        self.x=x;
    }
}

impl fmt::Display for Point{
    fn fmt(&self,f:&mut fmt::Formatter)->fmt::Result{
        write!(f,"Point {{ x: {},y: {} }}",self.x,self.y)
    }
}

fn main(){
    let mut point = Point::new(10,20);
    point.x(20);
    println!("{}",point);
}
```
