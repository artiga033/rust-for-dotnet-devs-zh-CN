# 字符串

Rust中有两种字符串类型：`String` 和 `str`，前者分配在堆上，后者则是到一个 `String` 或 `&str` 的切片。

这些类型到 .NET 的映射关系如下：

| Rust               | .NET                 | 备注        |
| ------------------ | -------------------- | ----------- |
| `&mut str`         | `Span<char>`         |             |
| `&str`             | `ReadOnlySpan<char>` |             |
| `Box<str>`         | `String`             | 参见备注 1. |
| `String`           | `String`             |             |
| `String` (mutable) | `StringBuilder`      | 参见备注 1. |

在 Rust 和 .NET 中使用字符串的方式有所不同，不过可以从上面列出的大致的等价关系开始。其中一点是 Rust 字符串是 UTF-8 编码的，而 .NET 的则是 UTF-16 编码。此外，.NET 字符串是不可变的，但 Rust 字符串可以被声明为可变，例如 `let s = &mut String::from("hello");`。

还有一些区别是由于所有权概念导致的。要了解 String 类型的所有权机制，参见 [Rust Book][ownership-string-type-example].

[ownership-string-type-example]: https://kaisery.github.io/trpl-zh-cn/ch04-01-what-is-ownership.html#string-%E7%B1%BB%E5%9E%8B

备注：

1. Rust 中的 `Box<str>` 类型和 .NET 的 `String` 类型是等价的。Rust中的 `Box<str>` 和 `String` 类型的区别是前者保存指针和长度，而后者保存指针、长度、还有容量，使得 `String` 长度可以增长。如果 Rust 的 `String` 声明为可变的，那么它与 .NET 中的 `StringBuilder` 类型很相似。

C#：

```csharp
ReadOnlySpan<char> span = "Hello, World!";
string str = "Hello, World!";
StringBuilder sb = new StringBuilder("Hello, World!");
```

Rust：

```rust
let span: &str = "Hello, World!";
let str = Box::new("Hello World!");
let mut sb = String::from("Hello World!");
```

## 字符串字面量

.NET 中的字符串字面量是分配在堆上的不可变的 `String` 类型。Rust 中则是 `&'static str`，它是不可变的，具有全局生命周期且并非分配在堆上，而是直接嵌入编译后的二进制文件。

C#

```csharp
string str = "Hello, World!";
```

Rust

```rust
let str: &'static str = "Hello, World!";
```

C# 的逐字字符串字面量和 Rust 的原始字符串字面量等价。

```csharp
string str = @"Hello, \World/!";
```

Rust

```rust
let str = r#"Hello, \World/!"#;
```

C# 的 UTF-8 字符串字面量和 Rust 的字节字符串字面量等价。

C#

```csharp
string str = "hello"u8;
```

Rust

```rust
let str = b"hello";
```

## 字符串内插值

C# 有内置的字符串内插功能，允许你在字符串字面量内嵌入表达式。下面是 C# 字符串内插的示例。

```csharp
string name = "John";
int age = 42;
string str = $"Person {{ Name: {name}, Age: {age} }}";
```

Rust 没有内置的字符串内插功能。作为替代，`format!` 宏被用来格式化字符串。下面是 Rust 字符串内插的示例。

```rust
let name = "John";
let age = 42;
let str = format!("Person {{ name: {name}, age: {age} }}");
```

在 C# 中，自定义类和结构体也可以被内插，因为所有类型都有继承自 `object` 的 `ToString()`方法。

```csharp
class Person
{
    public string Name { get; set; }
    public int Age { get; set; }

    public override string ToString() =>
        $"Person {{ Name: {Name}, Age: {Age} }}";
}

var person = new Person { Name = "John", Age = 42 };
Console.Writeline(person);
```

在 Rust 中，并非每一个类型都有默认的格式化实现/继承。相反，每一个需要转换到字符串的类型都必须实现 `std::fmt::Display` trait 。

```rust
use std::fmt::*;

struct Person {
    name: String,
    age: i32,
}

impl Display for Person {
    fn fmt(&self, f: &mut Formatter<'_>) -> Result {
        write!(f, "Person {{ name: {}, age: {} }}", self.name, self.age)
    }
}

let person = Person {
    name: "John".to_owned(),
    age: 42,
};

println!("{person}");
```

另一种选择是使用 `std::fmt::Debug` trait，`Debug` trait 为所有标准类型实现，并且可以用于打印类型的内部表示。下面的例子展示了如何使用 `derive` 属性来使用 `Debug` trait 打印一个自定义结构体的内部表示。这种声明方式为 `Person` 结构体自动实现了 `Debug` trait：

```rust
#[derive(Debug)]
struct Person {
    name: String,
    age: i32,
}

let person = Person {
    name: "John".to_owned(),
    age: 42,
};

println!("{person:?}");
```

> 备注： 使用 :? 格式化说明符会使用 `Debug` trait 来打印结构体，
> 省略该符号则会使用 `Display` trait。

另见：

- [Rust by Example - Debug](https://doc.rust-lang.org/stable/rust-by-example/hello/print/print_debug.html?highlight=derive#debug)
