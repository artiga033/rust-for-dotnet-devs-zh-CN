# 异常处理

在 .NET 中，异常是一种派生自 [`System.Exception`][net-system-exception] 的类。如果一段代码发生了问题，那么异常就会被抛出。抛出的异常会沿着栈传递，直到应用程序处理了它，或者程序终止。

Rust 没有异常，但是区分了 _可恢复_ 与 _不可恢复_ 的错误。一个可恢复的错误代表着一个应当被报告的问题，但是程序为此继续运行。对于可能会因可恢复的错误而失败的操作，它的的结果是类型 [`Result<T,E>`][rust-result]，其中 `E` 是错误变体的类型。[`panic!`][panic] 宏会在程序遇到不可恢复的错误时终止程序。不可恢复的错误总是意味着漏洞的存在。

## 自定义错误类型

在 .NET 中， 自定义异常从 `Exception` 类派生。关于 [如何创建用户定义的异常][net-user-defined-exceptions] 的文档提到了这个例子：

```csharp
public class EmployeeListNotFoundException : Exception
{
    public EmployeeListNotFoundException() { }

    public EmployeeListNotFoundException(string message)
        : base(message) { }

    public EmployeeListNotFoundException(string message, Exception inner)
        : base(message, inner) { }
}
```

在 Rust 中，可以通过实现 [`Error`][rust-std-error] trait 来实现错误值的基本要求。Rust 中最简短的用户定义错误实现是：

```rust
#[derive(Debug)]
pub struct EmployeeListNotFound;

impl std::fmt::Display for EmployeeListNotFound {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        f.write_str("Could not find employee list.")
    }
}

impl std::error::Error for EmployeeListNotFound {}
```

.NET 中 `Exception.InnerException` 属性的等价是 Rust 中的 `Error::source()` 方法。然而，对 `Error::source()` 的实现并非必要的，通用(默认的)实现返回一个 `None`。

## 引发异常

要在 C# 中引发异常，抛出一个异常的实例：

```csharp
void ThrowIfNegative(int value)
{
    if (value < 0)
    {
        throw new ArgumentOutOfRangeException(nameof(value));
    }
}
```

对于 Rust 中的可恢复错误，方法应当返回一个 `Ok` 或 `Err` 变体：

```rust
fn error_if_negative(value: i32) -> Result<(), &'static str> {
    if value < 0 {
        Err("Specified argument was out of the range of valid values. (Parameter 'value')")
    } else {
        Ok(())
    }
}
```

[`panic!`][panic] 宏创建不可恢复的错误：

```rust
fn panic_if_negative(value: i32) {
    if value < 0 {
        panic!("Specified argument was out of the range of valid values. (Parameter 'value')")
    }
}
```

## 错误传播

在 .NET 中，异常会沿着调用栈传递直到它们被处理或者程序终止。在 Rust 中，不可恢复的错误表现类似，不过通常不会处理它们。

然而，可恢复的错误需要被显式地传播和处理。它们的存在总是由 Rust 函数或方法的签名表示。在 C# 中，捕获异常允许你对错误的存在与否进行处理：

```csharp
void Write()
{
    try
    {
        File.WriteAllText("file.txt", "content");
    }
    catch (IOException)
    {
        Console.WriteLine("Writing to file failed.");
    }
}
```

在 Rust 中，这大致相当于：

```rust
fn write() {
    match std::fs::File::create("temp.txt")
        .and_then(|mut file| std::io::Write::write_all(&mut file, b"content"))
    {
        Ok(_) => {}
        Err(_) => println!("Writing to file failed."),
    };
}
```

大多数情况下，可恢复错误更需要被传播而不是处理。这种情况下，方法签名需要与要传播的错误类型兼容。[`?` 运算符][question-mark-operator] 以一种符合人体工学的方式传播错误。

```rust
fn write() -> Result<(), std::io::Error> {
    let mut file = std::fs::File::create("file.txt")?;
    std::io::Write::write_all(&mut file, b"content")?;
    Ok(())
}
```

**备注**：若要使用问号运算符来传播错误，错误实现必须 _兼容_，就像[传播错误的简写：? 运算符][propagating-errors-rust-book]中提到的。最一般的错误类型是错误的 [trait 对象][trait object] `Box<dyn Error>`。

## 堆栈跟踪

在 .NET 中抛出异常会导致运行时打印堆栈跟踪信息，这样可以借助额外的上下文来调试问题。

对于 Rust 中的可恢复错误，[`panic` backtrace][panic-backtrace] 提供了类似行为。

stable Rust 中的可恢复错误还不支持 backtrace，不过它当前已有实验性支持，通过使用[provide 方法][provide method]。

[net-system-exception]: https://learn.microsoft.com/zh-cn/dotnet/api/system.exception?view=net-6.0
[rust-result]: https://doc.rust-lang.org/std/result/enum.Result.html
[panic-backtrace]: https://kaisery.github.io/trpl-zh-cn/ch09-01-unrecoverable-errors-with-panic.html#%E4%BD%BF%E7%94%A8-panic-%E7%9A%84-backtrace
[net-user-defined-exceptions]: https://learn.microsoft.com/zh-cn/dotnet/standard/exceptions/how-to-create-user-defined-exceptions
[rust-std-error]: https://doc.rust-lang.org/std/error/trait.Error.html
[provide method]: https://doc.rust-lang.org/std/error/trait.Error.html#method.provide
[question-mark-operator]: https://doc.rust-lang.org/std/result/index.html#the-question-mark-operator-
[panic]: https://doc.rust-lang.org/std/macro.panic.html
[propagating-errors-rust-book]: https://kaisery.github.io/trpl-zh-cn/ch09-02-recoverable-errors-with-result.html#%E4%BC%A0%E6%92%AD%E9%94%99%E8%AF%AF%E7%9A%84%E7%AE%80%E5%86%99-%E8%BF%90%E7%AE%97%E7%AC%A6
[trait object]: https://doc.rust-lang.org/reference/types/trait-object.html
