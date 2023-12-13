# 标量类型

下表展示了 Rust 中的原生类型（primitive types）和它们在 C# 和 .NET 中的等价物：

| Rust    | C#        | .NET                   | 备注             |
| ------- | --------- | ---------------------- | ---------------- |
| `bool`  | `bool`    | `Boolean`              |                  |
| `char`  | `char`    | `Char`                 | 参见备注 1.      |
| `i8`    | `sbyte`   | `SByte`                |                  |
| `i16`   | `short`   | `Int16`                |                  |
| `i32`   | `int`     | `Int32`                |                  |
| `i64`   | `long`    | `Int64`                |                  |
| `i128`  |           | `Int128`               |                  |
| `isize` | `nint`    | `IntPtr`               |                  |
| `u8`    | `byte`    | `Byte`                 |                  |
| `u16`   | `ushort`  | `UInt16`               |                  |
| `u32`   | `uint`    | `UInt32`               |                  |
| `u64`   | `ulong`   | `UInt64`               |                  |
| `u128`  |           | `UInt128`              |                  |
| `usize` | `nuint`   | `UIntPtr`              |                  |
| `f32`   | `float`   | `Single`               |                  |
| `f64`   | `double`  | `Double`               |                  |
|         | `decimal` | `Decimal`              |                  |
| `()`    | `void`    | `Void` 或 `ValueTuple` | 参见备注 2 & 3. |
|         | `object`  | `Object`               | 参见备注 3.      |

备注：

1. Rust中的 [`char`][char.rs] 和 .NET 中的 [`Char`][char.net] 的定义不同。在Rust中，`char` 是一个四字节宽的[Unicode 标量值]，但是在.NET中，`Char` 是两字节宽的，它保存的该字符的UTF-16编码。更多信息参见[Rust `char` 文档][char.rs]
2. 尽管 unit 类型`()`（一个空元组）在Rust中是 _可表达值(expressible value)_，C# 中最接近它的是表示“无”的 `void`。然而，仅当使用指针和 unsafe 代码时，`void` 才是 _可表达值_。.NET 中的 [`ValueTuple`][ValueTuple] 是一个空元组，但是C#并没有像是 `()` 这种表示它的字面量语法。C# 中可以使用 `ValueTuple`，但这是很罕见的做法。不过，不同于 C# ，F# 拥有像 Rust 一样的 [unit 类型][unit.fs]。
3. 尽管 `void` 和 `object` 不是标量类型（甚至在 .NET 类型系统中 `int` 这种标量也是 `object` 的子类），方便起见它们也被列入上表中。

另见：

- [Primitives (Rust By Example)][primitives.rs]

[char.net]: https://learn.microsoft.com/zh-cn/dotnet/api/system.char
[char.rs]: https://doc.rust-lang.org/std/primitive.char.html
[Unicode 标量值]: https://www.unicode.org/glossary/#unicode_scalar_value
[ValueTuple]: https://learn.microsoft.com/zh-cn/dotnet/api/system.valuetuple
[unit.fs]: https://learn.microsoft.com/zh-cn/dotnet/fsharp/language-reference/unit-type
[primitives.rs]: https://doc.rust-lang.org/rust-by-example/primitives.html
