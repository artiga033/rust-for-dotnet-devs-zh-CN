# 弃元

在 C# 中，[弃元][net-discards] 表示编译器和其它工具链会忽略一个表达式的（部分）结果。

有多种上下文可以用到这个，例如最基本的，忽略一个表达式的结果。 在 C# 中，像这样：

```csharp
_ = city.GetCityInformation(cityName);
```

在 Rust 中，[忽略表达式的值][rust-ignoring-values] 看上去完全一致：

```rust
_ = city.get_city_information(city_name);
```

在 C# 中，弃元也用于解构元组：

```csharp
var (_, second) = ("first", "second");
```

Rust 中，完全一致：

```rust
let (_, second) = ("first", "second");
```

除了解构元组，Rust 还提供了对结构和枚举的[解构][rust-destructuring]，通过使用 `..`，它表示类型的剩余部分：

```rust
struct Point {
    x: i32,
    y: i32,
    z: i32,
}

let origin = Point { x: 0, y: 0, z: 0 };

match origin {
    Point { x, .. } => println!("x is {}", x), // x is 0
}
```

当进行模式匹配时，忽略一部分匹配表达式是很有用的，例如 C# 中：

```csharp
_ = ("first", "second") switch
{
    ("first", _) => "first element matched",
    (_, _) => "first element did not match"
};
```

同样的，Rust 的看起来也几乎一致：

```rust
_ = match ("first", "second")
{
    ("first", _) => "first element matched",
    (_, _) => "first element did not match"
};
```

[net-discards]: https://learn.microsoft.com/zh-cn/dotnet/csharp/fundamentals/functional/discards
[rust-ignoring-values]: https://kaisery.github.io/trpl-zh-cn/ch18-03-pattern-syntax.html#%E5%BF%BD%E7%95%A5%E6%A8%A1%E5%BC%8F%E4%B8%AD%E7%9A%84%E5%80%BC
[rust-destructuring]: https://doc.rust-lang.org/reference/patterns.html#destructuring
