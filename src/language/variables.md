# 变量

下面是 C# 中变量赋值的例子：

```csharp
int x = 5;
```

Rust 中同样的：

```rust
let x: i32 = 5;
```

目前看来，两个语言唯一的不同在于类型声明的位置。而且，C# 和 Rust 都是类型安全的：编译器会确保变量的值始终是它期望的类型。借助编译器自动推断变量类型的能力，上面的例子可以简写。C# 中：

```csharp
var x = 5;
```

Rust 中：

```rust
let x = 5;
```

扩展一下第一个例子，要更新变量的值（重新赋值），C# 和 Rust 的行为就不同了：

```csharp
var x = 5;
x = 6;
Console.WriteLine(x); // 6
```

In Rust, the identical statement will not compile:

```rust
let x = 5;
x = 6; // Error: cannot assign twice to immutable variable 'x'.
println!("{}", x);
```

Rust 中，变量默认是 _不可变_ 的。一旦值绑定到了一个名称，变量值就不能再改变了。可以通过在变量名前添加 [`mut`][mut.rs] 来使其成为 _可变的_：

```rust
let mut x = 5;
x = 6;
println!("{}", x); // 6
```

Rust 提供了一种替代方案来解决上面例子的问题，而不需要可变性，就是使用变量 _隐藏_（shadowing）：

```rust
let x = 5;
let x = 6;
println!("{}", x); // 6
```

C# 也支持隐藏，例如，本地变量可以隐藏字段，类型成员可以隐藏基类成员。在 Rust 中，上面的例子演示了隐藏还允许改变变量的类型而不改变名称，这对于要转换数据为不同的类型而又不想每一次都给它们起不同的名字的情况十分有用。

另见：

- [Data races and race conditions]：更多关于可变性意义的内容
- [Scope and shadowing]
- [内存管理][memory-management-section] 解释了 _移动_ 和 _所有权_

[mut.rs]: https://doc.rust-lang.org/std/keyword.mut.html
[memory-management-section]: ../memory-management/index.md
[data races and race conditions]: https://doc.rust-lang.org/nomicon/races.html
[scope and shadowing]: https://doc.rust-lang.org/stable/rust-by-example/variable_bindings/scope.html#scope-and-shadowing
