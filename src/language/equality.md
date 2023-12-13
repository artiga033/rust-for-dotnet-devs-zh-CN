# 相等性

C# 中，比较相等性有时是指检验 _相等_（又称 _值相等_），有时是指检验_引用相等_，这是检验两个变量是否指向内存中的同一个底层对象。每一个自定义类型都可以用上面提到的其中一种语义来比较相等性，因为它继承自 `System.Object`（或 `System.ValueType`，它又继承自 `System.Object`）。

例如，在 C# 中比较值相等性和引用相等性：

```csharp
var a = new Point(1, 2);
var b = new Point(1, 2);
var c = a;
Console.WriteLine(a == b); // (1) True
Console.WriteLine(a.Equals(b)); // (1) True
Console.WriteLine(a.Equals(new Point(2, 2))); // (1) True
Console.WriteLine(ReferenceEquals(a, b)); // (2) False
Console.WriteLine(ReferenceEquals(a, c)); // (2) True

record Point(int X, int Y);
```

1. `record Point` 上的相等操作符 `==` 和 `Equals` 方法会比较值相等性，因为记录默认支持值类型相等语义。

2. 比较引用相等性是检验变量是否指向内存中的同一个底层对象。

Rust 中的等价实现：

```rust
#[derive(Copy, Clone)]
struct Point(i32, i32);

fn main() {
    let a = Point(1, 2);
    let b = Point(1, 2);
    let c = a;
    println!("{}", a == b); // Error: "an implementation of `PartialEq<_>` might be missing for `Point`"
    println!("{}", a.eq(&b));
    println!("{}", a.eq(&Point(2, 2)));
}
```

上面的编译器报错展示了 Rust 的相等比较 _总是_ 和 trait 实现相关。要支持使用 `==` 的比较，类型必须实现 [`PartialEq`][partialeq.rs]。

要修复上面的实例就是要为 `Point` 实现 `PartialEq`。默认情况下，派生 `PartialEq` 会比较所有字段的相等性，这些字段本身也必须实现 `PartialEq`。这类似 C# 中记录的等价性。

```rust
#[derive(Copy, Clone, PartialEq)]
struct Point(i32, i32);

fn main() {
    let a = Point(1, 2);
    let b = Point(1, 2);
    let c = a;
    println!("{}", a == b); // true
    println!("{}", a.eq(&b)); // true
    println!("{}", a.eq(&Point(2, 2))); // false
    println!("{}", a.eq(&c)); // true
}
```

另见：

- [`Eq`][eq.rs]：更严格版本的 `PartialEq`。

[partialeq.rs]: https://doc.rust-lang.org/std/cmp/trait.PartialEq.html
[eq.rs]: https://doc.rust-lang.org/std/cmp/trait.Eq.html
