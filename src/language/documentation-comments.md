# 文档注释

C# 提供一种使用包含 xml 文本的注释语法来为类型 API 编写文档的方案。C# 编译器会产生一个 xml 文件，它包含了表示注释和 API 签名的结构化数据。其他工具可以将该输出处理为另一种人类可读的文档形式。一个 C# 中的简单示例：

```csharp
/// <summary>
/// This is a document comment for <c>MyClass</c>.
/// </summary>
public class MyClass {}
```

Rust [文档注释][doc comments]提供了和 C# 中同等的功能。Rust 文档注释使用 Markdown 语法。[`rustdoc`][rustdoc] 是 Rust 代码的文档编译器，通常通过 [`cargo doc`][cargo doc] 调用，这会将注释编译为文档。例如：

```rust
/// This is a doc comment for `MyStruct`.
struct MyStruct;
```

.NET SDK 中并没有 `dotnet doc` 之类的和 `cargo doc` 对应的命令。

另见：

- [How to write documentation]
- [Documentation tests]

[doc comments]: https://doc.rust-lang.org/rust-by-example/meta/doc.html
[rustdoc]: https://doc.rust-lang.org/rustdoc/index.html
[cargo doc]: https://doc.rust-lang.org/cargo/commands/cargo-doc.html
[How to write documentation]: https://doc.rust-lang.org/rustdoc/how-to-write-documentation.html
[documentation tests]: https://doc.rust-lang.org/rustdoc/write-documentation/documentation-tests.html
