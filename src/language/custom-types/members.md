# 成员（Members）

## 构造函数

Rust 没有任何构造函数的表示法。相反，你可以之禅秀编写返回该类型实例的工厂函数。工厂函数可以是单独的也可以是该类型的 _关联函数_。用 C# 的说法，关联函数就像一个类型的静态方法。按照惯例，如果一个 `struct` 只有一个工厂函数，它应被命名为 `new`。

```rust
struct Rectangle {
    x1: i32, y1: i32,
    x2: i32, y2: i32,
}

impl Rectangle {
    pub fn new(x1: i32, y1: i32, x2: i32, y2: i32) -> Self {
        Self { x1, y1, x2, y2 }
    }
}
```

由于 Rust 函数（不管是否关联）不支持重载，这些工厂函数必须被唯一地命名。例如，下面是 `String` 类型可用的所谓构造函数或工厂函数：

- `String::new`：创建空字符串
- `String::with_capacity`：创建带有指定初始缓冲区容量的字符串
- `String::from_utf8`：从一串 UTF-8 字节编码字符串。
- `String::from_utf16`：从一串 UTF-16 字节编码字符串。

对于 Rust `enum` 类型的情况，它的变体成员就是构造函数。参见 [enum 一节][enums]。

另见：

- [Constructors are static, inherent methods (C-CTOR)][rs-api-C-CTOR]

  [enums]: enums.md
  [rs-api-C-CTOR]: https://rust-lang.github.io/api-guidelines/predictability.html?highlight=new#constructors-are-static-inherent-methods-c-ctor

## 方法（静态的和实例的）

就像 C#，Rust 类型（`enum` 和 `struct` 都是）可以有静态和实例方法。在 Rust 语境中，_方法_ 始终是实例上的，并且通过它的第一个参数是`self`来标识，`self` 参数没有类型标注，因为它始终是该方法所属的类型。静态方法则被称作 _关联函数_，下面的例子中，`new` 是一个关联函数，而剩下的（`length`、`width` 和 `area`）则是该类型的方法：

```rust
struct Rectangle {
    x1: i32, y1: i32,
    x2: i32, y2: i32,
}

impl Rectangle {
    pub fn new(x1: i32, y1: i32, x2: i32, y2: i32) -> Self {
        Self { x1, y1, x2, y2 }
    }

    pub fn length(&self) -> i32 {
        self.y2 - self.y1
    }

    pub fn width(&self)  -> i32 {
        self.x2 - self.x1
    }

    pub fn area(&self)  -> i32 {
        self.length() * self.width()
    }
}
```

## 常量

像 C# 一样，Rust 类型可以包含常量。不过，更有意思的一点是 Rust 允许类型实例被定义为常量：

```rust
struct Point {
    x: i32,
    y: i32,
}

impl Point {
    const ZERO: Point = Point { x: 0, y: 0 };
}
```

C# 中，这需要静态只读字段来实现：

```c#
readonly record struct Point(int X, int Y)
{
    public static readonly Point Zero = new(0, 0);
}
```

## 事件

Rust 没有内置的支持来让类型成员广播和激发事件，不像 C#拥有 `event` 关键字。

## 属性[^translator_note_attribute_vs_property]

C# 中，类型字段通常是私有的，它们被属性成员保护/封装着，通过访问器方法（`get` 和 `set`）来读写字段。访问器方法可以包含额外逻辑，例如，设置值的时候进行验证或读取值的时候进行计算。Rust 只有方法，[getter 用字段名命名（Rust 中方法和字段的标识符可以一样），setter 则前缀 `set_`][get-set-name.rs]

  [get-set-name.rs]: https://github.com/rust-lang/rfcs/blob/master/text/0344-conventions-galore.md#gettersetter-apis

下面的例子演示了 Rust 中的类似属性的访问器方法：

```rust
struct Rectangle {
    x1: i32, y1: i32,
    x2: i32, y2: i32,
}

impl Rectangle {
    pub fn new(x1: i32, y1: i32, x2: i32, y2: i32) -> Self {
        Self { x1, y1, x2, y2 }
    }

    // 相当于属性 getter (都和对应的字段名一样)

    pub fn x1(&self) -> i32 { self.x1 }
    pub fn y1(&self) -> i32 { self.y1 }
    pub fn x2(&self) -> i32 { self.x2 }
    pub fn y2(&self) -> i32 { self.y2 }

    // 相当于属性 setter

    pub fn set_x1(&mut self, val: i32) { self.x1 = val }
    pub fn set_y1(&mut self, val: i32) { self.y1 = val }
    pub fn set_x2(&mut self, val: i32) { self.x2 = val }
    pub fn set_y2(&mut self, val: i32) { self.y2 = val }

    // 相当于计算属性

    pub fn length(&self) -> i32 {
        self.y2 - self.y1
    }

    pub fn width(&self)  -> i32 {
        self.x2 - self.x1
    }

    pub fn area(&self)  -> i32 {
        self.length() * self.width()
    }
}
```

## 扩展方法

C# 中的扩展方法允许开发者为现有类型附加新的静态绑定的方法，而不需要修改原有的类型定义。下面的 C# 示例中，通过 _扩展_，为 `StringBuilder` 类添加了一个新的 `Wrap` 方法：

```csharp
using System;
using System.Text;
using Extensions; // (1)

var sb = new StringBuilder("Hello, World!");
sb.Wrap(">>> ", " <<<"); // (2)
Console.WriteLine(sb.ToString()); // Prints: >>> Hello, World! <<<

namespace Extensions
{
    static class StringBuilderExtensions
    {
        public static void Wrap(this StringBuilder sb,
                                string left, string right) =>
            sb.Insert(0, left).Append(right);
    }
}
```

注意为了使扩展方法可用（2），包含扩展方法的类型的命名空间必须先被引入（1）。Rust 通过 trait 提供了一种非常类似的方案，叫做 _扩展 trait_。下面的 Rust 例子和上面的 C# 例子等价，它为 `String` 扩展了方法 `wrap`：

```rust
#![allow(dead_code)]

mod exts {
    pub trait StrWrapExt {
        fn wrap(&mut self, left: &str, right: &str);
    }

    impl StrWrapExt for String {
        fn wrap(&mut self, left: &str, right: &str) {
            self.insert_str(0, left);
            self.push_str(right);
        }
    }
}

fn main() {
    use exts::StrWrapExt as _; // (1)

    let mut s = String::from("Hello, World!");
    s.wrap(">>> ", " <<<"); // (2)
    println!("{s}"); // Prints: >>> Hello, World! <<<
}
```

就像 C# 中那样，为了使扩展 trait 的方法可用（2），扩展 trait 必须先被导入（1）。还需要注意，扩展 trait 标识符 `StrWarpExt` 自身可以通过 `_` 被丢弃，这不会影响 `wrap` 对 `String` 的可用性。

## 可见性/访问修饰符

C# 有许多可访问性，亦称可见性，修饰符：

- `private`
- `protected`
- `internal`
- `protected internal` (family)
- `public`

Rust 中，编译过程就是构建一棵由模块组成的树，每个模块都包含并定义了若干[程序项（items）][items]，比如类型、trait、枚举、常量和函数。几乎一切都是默认私有的。例外之一是，比如公共 trait 中的关联函数是默认公开的。类似的是 C# 的接口即使没有任何公共修饰符也是默认公开的。Rust 只有 `pub` 修饰符来改变在模块树中的可见性。`pub` 有一些变体改变了公开可见的作用域：

- `pub(self)`
- `pub(super)`
- `pub(crate)`
- `pub(in PATH)`

更多细节可以查看 Rust Reference 的 [Visibility and Privacy][privis] 一节。

  [privis]: https://doc.rust-lang.org/reference/visibility-and-privacy.html
  [items]: https://doc.rust-lang.org/reference/items.html

下表是 C# 和 Rust 修饰符的大致对照：

| C#                            | Rust         | 备注        |
| ----------------------------- | ------------ | ----------- |
| `private`                     | (default)    |  见备注 1.  |
| `protected`                   | N/A          |  见备注 2.  |
| `internal`                    | `pub(crate)` |             |
| `protected internal` (family) | N/A          |  见备注 2.  |
| `public`                      | `pub`        |             |

1. 没有关键字来表明私有可见性，这是 Rust 的默认行为。

2. 由于 Rust 中没有基于类的类型架构，也就没有 `protected` 的等价物了。

## 可变性

在 C# 中设计类型时，开发者可以自行决定类型是否可变、是否支持破坏性修变更。C# 也为 _positional record 声明_（`record class`或 `readonly record struct`） 支持不可变设计。在 Rust 中，可变性通过方法的 `self` 参数来表达，如下面的例子所示：

```rust
struct Point { x: i32, y: i32 }

impl Point {
    pub fn new(x: i32, y: i32) -> Self {
        Self { x, y }
    }

    // self 不可变

    pub fn x(&self) -> i32 { self.x }
    pub fn y(&self) -> i32 { self.y }

    // self 可变

    pub fn set_x(&mut self, val: i32) { self.x = val }
    pub fn set_y(&mut self, val: i32) { self.y = val }
}
```

C# 中，你可以用 `with` 来进行非破坏性变更：

```c#
var pt = new Point(123, 456);
pt = pt with { X = 789 };
Console.WriteLine(pt.ToString()); // prints: Point { X = 789, Y = 456 }

readonly record struct Point(int X, int Y);
```

Rust 中没有 `with`，但是为了模拟类似的实现，它必须硬编码在类型设计中：

```rust
struct Point { x: i32, y: i32 }

impl Point {
    pub fn new(x: i32, y: i32) -> Self {
        Self { x, y }
    }

    pub fn x(&self) -> i32 { self.x }
    pub fn y(&self) -> i32 { self.y }

    // following methods consume self and return a new instance

    pub fn set_x(self, val: i32) -> Self { Self::new(val, self.y) }
    pub fn set_y(self, val: i32) -> Self { Self::new(self.x, val) }
}
```

---

[^translator_note_attribute_vs_property]: 译者注①：前文中也多次出现了“属性”一词，这里第一次出现 C# 语境下的 “属性”。由于 C# 和 Rust 社区的常用翻译不同，C# 中的属性指的是 Property，即封装字段的访问器。另一个词 Attribute 在 C# 中通常译作“特性”，即使用方括号 `[]` 包裹的用于为代码附加元信息的特殊类型，使用上类似于 Rust 中的 attribute macro，但这个词通常被 Rust 社区译为 “属性宏”。此译本的做法是，Property 始终译作“属性”，Attribute 在 C# 语境中译作“特性”，在 Rust 语境中译作“属性”。
