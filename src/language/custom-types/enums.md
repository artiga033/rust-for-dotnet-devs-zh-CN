# 枚举（Enum）

C# 中，枚举是一种值类型，它将符号映射到整数值。

```c#
enum DayOfWeek
{
    Sunday = 0,
    Monday = 1,
    Tuesday = 2,
    Wednesday = 3,
    Thursday = 4,
    Friday = 5,
    Saturday = 6,
}
```

Rust 有着 _完全一致_ 语法做同样的事：

```rust
enum DayOfWeek
{
    Sunday = 0,
    Monday = 1,
    Tuesday = 2,
    Wednesday = 3,
    Thursday = 4,
    Friday = 5,
    Saturday = 6,
}
```

与 .NET 不同，Rust `enum` 实例没有任何预定义行为，甚至无法进行像 `dow == DayOfWeek::Friday` 这种简单的等价检验。为了赋予它一些和 C# `enum` 差不多的功能，需要使用 [`#derive`属性][derive] 来自动实现一些常用功能：

```rust,does_not_compile
#[derive(Debug,     // 为 "{:?}" 启用格式化
         Clone,     // Copy 需要
         Copy,      // 启用按值复制语义
         Hash,      // 为了在各种 map 中使用，启用哈希能力
         PartialEq  // 启用值等价性 (==)
)]
enum DayOfWeek
{
    Sunday = 0,
    Monday = 1,
    Tuesday = 2,
    Wednesday = 3,
    Thursday = 4,
    Friday = 5,
    Saturday = 6,
}

fn main() {
    let dow = DayOfWeek::Wednesday;
    println!("Day of week = {dow:?}");

    if dow == DayOfWeek::Friday {
        println!("Yay! It's the weekend!");
    }

    // 强制转换为整形
    let dow = dow as i32;
    println!("Day of week = {dow:?}");

    let dow = dow as DayOfWeek;
    println!("Day of week = {dow:?}");
}
```

上面的例子表明，一个 `enum` 类型可以被强制转换到为它分配的整数类型，不过不像 C#，反过来的转换时不可行的（尽管这在 C#/.NET 中也有负面影响，即 `enum` 实例可能持有的是不存在的值）。反之，开发者需要来提供这一帮助函数：

```rust
impl DayOfWeek {
    fn from_i32(n: i32) -> Result<DayOfWeek, i32> {
        use DayOfWeek::*;
        match n {
            0 => Ok(Sunday),
            1 => Ok(Monday),
            2 => Ok(Tuesday),
            3 => Ok(Wednesday),
            4 => Ok(Thursday),
            5 => Ok(Friday),
            6 => Ok(Saturday),
            _ => Err(n)
        }
    }
}
```

如果`n`是有效值，`from_i32` 函数返回一个包裹在表示成功（`Ok`）的 `Result` 中的 `DayOfWeek`，否则它将`n`包裹在表示失败（`Err`）的 `Result` 中原样返回。

```rust
let dow = DayOfWeek::from_i32(5);
println!("{dow:?}"); // prints: Ok(Friday)

let dow = DayOfWeek::from_i32(50);
println!("{dow:?}"); // prints: Err(50)
```

Rust 有一些 crate 可以帮助实现这种从整数类型的映射从而不必手动编码。

Rust 中的 `enum` 类型也是一种设计（可区分）联合类型的方式，它允许不同的 _成员_（_variants_）持有特定于自己的数据。例：

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1"));
```

这种 `enum` 声明形式不存在于 C# 中，不过可以用（类）记录来模拟：

```c#
var home = new IpAddr.V4(127, 0, 0, 1);
var loopback = new IpAddr.V6("::1");

abstract record IpAddr
{
    public sealed record V4(byte A, byte B, byte C, byte D): IpAddr;
    public sealed record V6(string Address): IpAddr;
}
```

二者不同之处在于 Rust 定义产生的是成员的 _封闭类型（closed type）_，也就是说编译器知道 `IpAddr` 没有除了 `IpAddr::V4` 和 `IpAddr::V6`之外的成员，并且可以借此执行更严格的检查，例如在 `match` 表达式（相当于 C# `switch` 表达式）中，Rust 编译器不会通过代码除非所有变体都已被覆盖。相比之下，C# 的模拟方案实际上创建了类架构（尽管表达很简单），并且由于 `IpAddr` 是一个 _抽象基类_，它能表示的所有类型集合对于编译器是未知的。

  [derive]: https://doc.rust-lang.org/stable/reference/attributes/derive.html
