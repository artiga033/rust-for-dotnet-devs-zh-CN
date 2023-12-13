# 类型转换

C# 和 Rust 在编译时都是静态类型的。因此，一旦声明了一个变量，后续再为它赋值一个不同类型的值是不被允许的
（除非该值可以隐式转换到目标类型）。C# 中有好几种类型转换的方式，Rust中也有和它们相对的存在。

## 隐式转换

C# 中有隐式转换，Rust 也是（这被称作[强制类型转换（Type coercion）][type coercions]）。参考这个例子：

```csharp
int intNumber = 1;
long longNumber = intNumber;
```

Rust 对于对于强制类型转换更严格：

```rust
let int_number: i32 = 1;
let long_number: i64 = int_number; // error: expected `i64`, found `i32`
```

一个有效的隐式转换的例子，通过使用 [子类型（subtyping）][subtyping.rs]：

```rust
fn bar<'a>() {
    let s: &'static str = "hi";
    let t: &'a str = s;
}
```

另见：

- [Deref coercion]
- [Subtyping and variance]

[type coercions]: https://doc.rust-lang.org/reference/type-coercions.html
[subtyping.rs]: https://github.com/rust-lang/rfcs/blob/master/text/0401-coercions.md#subtyping
[deref coercion]: https://doc.rust-lang.org/std/ops/trait.Deref.html#more-on-deref-coercion
[Subtyping and variance]: https://doc.rust-lang.org/reference/subtyping.html#subtyping-and-variance

## 显式转换

如果转换可能导致丢失信息，C# 需要使用强制转换表达式来显式转换：

```csharp
double a = 1.2;
int b = (int)a;
```

当向下转换时，显式转换可能会在运行时失败，并抛出 `OverflowException` 或 `InvalidCastException` 之类的异常。

Rust 不提供原始类型之间的强制转换，不过可以使用 as[as.rs] 关键字来进行[显式转换][casting.rs]。Rust 中显式转换不会引发 panic。

```rust
let int_number: i32 = 1;
let long_number: i64 = int_number as _;
```

[casting.rs]: https://doc.rust-lang.org/rust-by-example/types/cast.html
[as.rs]: https://doc.rust-lang.org/reference/expressions/operator-expr.html#type-cast-expressions

## 自定义转换

通常，.NET 类型提供用户定义的转换运算符来将一个类型转换成另一个。另外，`System.IConvertible` 也是为了类型互相转换而存在的。

再 Rust 中，标准库包含将一个值转换成另一种类型的抽象，通过 [`From`][from.rs] trait 和与它对应的 [`Into`][into.rs]。当为一个类型实现 `From` 时，会自动提供一个默认的 `Into` 实现（在 Rust 中叫做 _通用实现_）。下例演示了这两种类型转换：

```rust
fn main() {
    let my_id = MyId("id".into()); // 由于为 `String` 实现了 `from<&str>` trait，`into()` 也自动实现了。
    println!("{}", String::from(my_id)); // 这使用 `String` 的 `From<MyId>` 实现。
}

struct MyId(String);

impl From<MyId> for String {
    fn from(MyId(value): MyId) -> Self {
        value
    }
}
```

另见：

- [`TryFrom`][try-from.rs] 和 [`TryInto`][try-into.rs]，`From`
  和 `Into` 的可失败的版本。

[from.rs]: https://doc.rust-lang.org/std/convert/trait.From.html
[into.rs]: https://doc.rust-lang.org/std/convert/trait.Into.html
[try-from.rs]: https://doc.rust-lang.org/std/convert/trait.TryFrom.html
[try-into.rs]: https://doc.rust-lang.org/std/convert/trait.TryInto.html
