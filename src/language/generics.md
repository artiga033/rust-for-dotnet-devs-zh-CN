# 泛型

C# 中的泛型提供了一种定义使用其它类型作为参数的类型和方法的方式。这提高了代码的重用性、类型安全和性能（例如，避免了运行时类型强制转换）。考虑下面这个示例，这是一个泛型类型，它为任何值添加一个时间戳：

```csharp
using System;

sealed record Timestamped<T>(DateTime Timestamp, T Value)
{
    public Timestamped(T value) : this(DateTime.UtcNow, value) { }
}
```

Rust 也有泛型，这是上面的等价实现：

```rust
use std::time::*;

struct Timestamped<T> { value: T, timestamp: SystemTime }

impl<T> Timestamped<T> {
    fn new(value: T) -> Self {
        Self { value, timestamp: SystemTime::now() }
    }
}
```

另见：

- [泛型数据类型]

[泛型数据类型]: https://kaisery.github.io/trpl-zh-cn/ch10-01-syntax.html

## 泛型类型约束

C# 中，通过使用 `where` 子句，[泛型类型可以被约束][type-constraints.cs]。 下例展示了 C# 中的这种约束：

```csharp
using System;

// 备注：记录自动实现了 `IEquatable`。
// 为了和 Rust 比较，下面的实现显式表明了这一行为。
sealed record Timestamped<T>(DateTime Timestamp, T Value) :
    IEquatable<Timestamped<T>>
    where T : IEquatable<T>
{
    public Timestamped(T value) : this(DateTime.UtcNow, value) { }

    public bool Equals(Timestamped<T>? other) =>
        other is { } someOther
        && Timestamp == someOther.Timestamp
        && Value.Equals(someOther.Value);

    public override int GetHashCode() => HashCode.Combine(Timestamp, Value);
}
```

Rust 中同样可以做到：

```rust
use std::time::*;

struct Timestamped<T> { value: T, timestamp: SystemTime }

impl<T> Timestamped<T> {
    fn new(value: T) -> Self {
        Self { value, timestamp: SystemTime::now() }
    }
}

impl<T> PartialEq for Timestamped<T>
    where T: PartialEq {
    fn eq(&self, other: &Self) -> bool {
        self.value == other.value && self.timestamp == other.timestamp
    }
}
```

在 Rust 中，泛型类型约束叫做 [`bounds`][bounds.rs]。

C# 版本中， `Timestamped<T>` 实例 _仅_ 可以为自身实现了 `IEquatable<T>` 的 `T` 类型创建。但是注意，Rust 版本更加灵活，因为它的 `Timestamped<T>` _有条件地实现了_ `PartialEq`。这意味着 `Timestamped<T>` 实例仍然可以为不可比较的 `T` 类型创建，但是此时 `Timestamped<T>` 不会为这个 `T` 通过 `PartialEq` 实现相等性。

另见：

- [trait 作为参数]
- [返回实现了 trait 的类型]

[type-constraints.cs]: https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/constraints-on-type-parameters
[bounds.rs]: https://doc.rust-lang.org/rust-by-example/generics/bounds.html
[trait 作为参数]: https://kaisery.github.io/trpl-zh-cn/ch10-02-traits.html#trait-%E4%BD%9C%E4%B8%BA%E5%8F%82%E6%95%B0
[返回实现了 trait 的类型]: https://kaisery.github.io/trpl-zh-cn/ch10-02-traits.html#%E8%BF%94%E5%9B%9E%E5%AE%9E%E7%8E%B0%E4%BA%86-trait-%E7%9A%84%E7%B1%BB%E5%9E%8B
