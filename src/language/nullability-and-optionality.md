# Nullable 和 Option

在 C# 中，`null` 通常用来表示不存在、缺失或逻辑上未初始化的值。例如：

```csharp
int? some = 1;
int? none = null;
```

Rust 没有 `null` 从而也就没有启用可为 null 的上下文的说法。作为替代，可选的或不存在的值用 [`Option<T>`][option] 表示。上面的 C# 代码的 Rust 等价实现是：

```rust
let some: Option<i32> = Some(1);
let none: Option<i32> = None;
```

Rust 的 `Option<T>` 实际上和 F# 的 [`'T option`][opt.fs] 一致。

[opt.fs]: https://fsharp.github.io/fsharp-core-docs/reference/fsharp-core-option-1.html

## 带有可选项的控制流

在 C# 中，你可能会使用 `if`/`else` 语句来控制带有可为 null 值时的程序流。

```csharp
uint? max = 10;
if (max is { } someMax)
{
    Console.WriteLine($"The maximum is {someMax}."); // The maximum is 10.
}
```

在 Rust 中你可以用模式匹配取得同样的行为：

使用 `if let` 甚至会更简单：

```rust
let max = Some(10u32);
if let Some(max) = max {
    println!("The maximum is {}.", max); // The maximum is 10.
}
```

## Null 条件运算符

C# 的 Null 条件运算符（`?.`和`?[]`）使得处理 `null` 更人性化。在 Rust 中，它们最好的替代就是使用 [`map`][optmap] 方法。下面的代码段展示了对照：

```csharp
string? some = "Hello, World!";
string? none = null;
Console.WriteLine(some?.Length); // 13
Console.WriteLine(none?.Length); // (blank)
```

```rust
let some: Option<String> = Some(String::from("Hello, World!"));
let none: Option<String> = None;
println!("{:?}", some.map(|s| s.len())); // Some(13)
println!("{:?}", none.map(|s| s.len())); // None
```

## Null 合并运算符

Null 合并运算符（`??`）常用于当一个可为 null 的值为 `null` 时默认为另一个值：

```csharp
int? some = 1;
int? none = null;
Console.WriteLine(some ?? 0); // 1
Console.WriteLine(none ?? 0); // 0
```

在 Rust 中，你可以通过 [`unwrap_or`][unwrap-or] 来取得同样的行为：

```rust
let some: Option<i32> = Some(1);
let none: Option<i32> = None;
println!("{:?}", some.unwrap_or(0)); // 1
println!("{:?}", none.unwrap_or(0)); // 0
```

**备注**：如果默认值的计算很昂贵，你可以使用 `unwrap_or_else` 替代。它接收闭包作为参数，因此可以懒加载默认值。

## Null 包容运算符

Null 包容运算符（`!`）在 Rust 中没有对应的构造，因为在 C# 中，它也仅仅是影响编译器的静态分析。在 Rust 中，不需要它这样的存在。

[option]: https://doc.rust-lang.org/std/option/enum.Option.html
[optmap]: https://doc.rust-lang.org/std/option/enum.Option.html#method.map
[unwrap-or]: https://doc.rust-lang.org/std/option/enum.Option.html#method.unwrap_or
