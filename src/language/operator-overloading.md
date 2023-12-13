# 运算符重载

在 C# 中，自定义类型可以重载 _可重载运算符_。考虑下面这个 C# 示例：

```csharp
Console.WriteLine(new Fraction(5, 4) + new Fraction(1, 2));  // 14/8

public readonly record struct Fraction(int Numerator, int Denominator)
{
    public static Fraction operator +(Fraction a, Fraction b) =>
        new(a.Numerator * b.Denominator + b.Numerator * a.Denominator, a.Denominator * b.Denominator);

    public override string ToString() => $"{Numerator}/{Denominator}";
}
```

在 Rust 中，大多数运算符[可以通过 trait 重载][ops.rs]。之所以这样可行是因为运算符是方法调用的语法糖。例如， `a + b` 中的 `+` 运算符调用了 `add` 方法。（参见 [operator overloading]）。

```rust
use std::{fmt::{Display, Formatter, Result}, ops::Add};

struct Fraction {
    numerator: i32,
    denominator: i32,
}

impl Display for Fraction {
    fn fmt(&self, f: &mut Formatter<'_>) -> Result {
        f.write_fmt(format_args!("{}/{}", self.numerator, self.denominator))
    }
}

impl Add<Fraction> for Fraction {
    type Output = Fraction;

    fn add(self, rhs: Fraction) -> Fraction {
        Fraction {
            numerator: self.numerator * rhs.denominator + rhs.numerator * self.denominator,
            denominator: self.denominator * rhs.denominator,
        }
    }
}

fn main() {
    println!(
        "{}",
        Fraction { numerator: 5, denominator: 4 } + Fraction { numerator: 1, denominator: 2 }
    ); // 14/8
}

```

[ops.rs]: https://doc.rust-lang.org/core/ops/
[operator overloading]: https://doc.rust-lang.org/rust-by-example/trait/ops.html
