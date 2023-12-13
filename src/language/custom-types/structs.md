# 结构（Struct）

Rust 和 C# 中的结构体有一些相同点：

- 它们都通过 `struct` 关键字定义，但是 Rust 中，`struct` 只能定义数据/字段。函数和方法所表示的行为都单独定义在一个 _impl块_ 中。

- Rust 中结构可以实现多个 trait，就像 C# 中的可以实现多个接口。

- 它们无法派生类。

- 它们默认分配在栈上，除非：
  - .NET 中，装箱或转换为接口类型。
  - Rust 中，包裹在 `Box`，`Rc`/`Arc` 等智能指针内。

在 C# 中，`struct` 是建立 .NET _值类型_，的办法，它们通常是一些领域特定的原始值，或是需要值等价性语义的复合类型。在 Rust 中，`struct` 是主要的建立数据类型的方式。（另一个是 `enum`）。

C# 中的 `struct` （或 `record struct`）默认拥有按值复制和值等价性语义。但在 Rust 中，这需要额外的一步，使用 [`#derive` 属性][derive] 并列出要实现的 trait。

  [derive]: https://doc.rust-lang.org/stable/reference/attributes/derive.html

```rust
#[derive(Clone, Copy, PartialEq, Eq, Hash)]
struct Point {
    x: i32,
    y: i32,
}
```

C#/.NET 中的值类型通常被开发者设计为不可变的。这被认为是符合语义的最佳实践，不过语言本身并不阻止设计一个具有破坏性和原地修改行为的 `struct`。Rust 中也一样。类型必须被开发者刻意地设计为可变的。

由于 Rust 没有类，因此也没有通过派生类实现的类型架构，共享行为通过 trait，泛型，以及通过使用 [trait objects] 动态分发实现的多态。

  [trait objects]: https://kaisery.github.io/trpl-zh-cn/ch17-02-trait-objects.html#%E9%A1%BE%E5%8F%8A%E4%B8%8D%E5%90%8C%E7%B1%BB%E5%9E%8B%E5%80%BC%E7%9A%84-trait-%E5%AF%B9%E8%B1%A1

假设下面这个 C# `struct` 表示一个矩形：

```c#
struct Rectangle
{
    public Rectangle(int x1, int y1, int x2, int y2) =>
        (X1, Y1, X2, Y2) = (x1, y1, x2, y2);

    public int X1 { get; }
    public int Y1 { get; }
    public int X2 { get; }
    public int Y2 { get; }

    public int Length => Y2 - Y1;
    public int Width => X2 - X1;

    public (int, int) TopLeft => (X1, Y1);
    public (int, int) BottomRight => (X2, Y2);

    public int Area => Length * Width;
    public bool IsSquare => Width == Length;

    public override string ToString() => $"({X1}, {Y1}), ({X2}, {Y2})";
}
```

Rust 的等价实现：

```rust
#![allow(dead_code)]

struct Rectangle {
    x1: i32, y1: i32,
    x2: i32, y2: i32,
}

impl Rectangle {
    pub fn new(x1: i32, y1: i32, x2: i32, y2: i32) -> Self {
        Self { x1, y1, x2, y2 }
    }

    pub fn x1(&self) -> i32 { self.x1 }
    pub fn y1(&self) -> i32 { self.y1 }
    pub fn x2(&self) -> i32 { self.x2 }
    pub fn y2(&self) -> i32 { self.y2 }

    pub fn length(&self) -> i32 {
        self.y2 - self.y1
    }

    pub fn width(&self)  -> i32 {
        self.x2 - self.x1
    }

    pub fn top_left(&self) -> (i32, i32) {
        (self.x1, self.y1)
    }

    pub fn bottom_right(&self) -> (i32, i32) {
        (self.x2, self.y2)
    }

    pub fn area(&self)  -> i32 {
        self.length() * self.width()
    }

    pub fn is_square(&self)  -> bool {
        self.width() == self.length()
    }
}

use std::fmt::*;

impl Display for Rectangle {
    fn fmt(&self, f: &mut Formatter<'_>) -> Result {
        write!(f, "({}, {}), ({}, {})", self.x1, self.y2, self.x2, self.y2)
    }
}
```

需要注意 C# 的 `struct` 继承了来自 `Object` 的 `ToString` 方法，因此，它可以通过 _重写_ 基类实现来提供自定义的字符串表示。由于 Rust 中没有继承，类型对一些 _格式化_ 表示的支持是通过实现 `Display` trait。这样该结构的实例就可以参与格式化，例如被 `println!` 打印。如下：

```rust
fn main() {
    let rect = Rectangle::new(12, 34, 56, 78);
    println!("Rectangle = {rect}");
}
```
