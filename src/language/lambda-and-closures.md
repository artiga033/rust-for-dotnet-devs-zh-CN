# Lambda表达式与闭包

C# 和 Rust 允许函数用作一级值（first-class value），这使得可以编写 _高阶函数_。高阶函数可以接受其它函数作为参数，从而允许调用者参与被调用函数的代码。在 C# 中，_类型安全的函数指针_ 由委托表示，最常见的就是 `Func` 和 `Action`。C# 允许通过 _lambda 表达式_ 来临时声明这些委托。

Rust 也有函数指针，最简单的就是 `fn` 类型：

```rust
fn do_twice(f: fn(i32) -> i32, arg: i32) -> i32 {
    f(arg) + f(arg)
}

fn main() {
    let answer = do_twice(|x| x + 1, 5);
    println!("The answer is: {}", answer); // Prints: The answer is: 12
}
```

但是，Rust 对 _函数指针_（`fn` 定义的类型） 和 _闭包_ 做了区分：闭包可以引用环绕它的作用域的变量，但函数指针不可以。尽管 C# 也有 [函数指针][*delegate] (`*delegate`)，但托管的、类型安全的等价替代是静态 lambda 表达式。

  [*delegate]: https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/proposals/csharp-9.0/function-pointers

接受闭包的函数和方法都使用泛型类型编写，最终绑定到其中一种表示函数的 trait：`Fn`、`FnMut` 和 `FnOnce`。当为函数指针和闭包提供值时， Rust 开发者使用 _闭包表达式_ （例如上例中的 `|x| x + 1`），就像 C# 中的 lambda 表达式。闭包表达式创建函数指针还是闭包取决于闭包表达式是否引用了它的上下文。

当一个闭包从环境中捕获变量时，所有权规则也会参与其中，因为所有权最终会随着闭包而结束。更多信息参见《Rust 程序设计语言》的 “[将被捕获的值移出闭包和 Fn trait][closure-move]”一节。

  [closure-move]: https://kaisery.github.io/trpl-zh-cn/ch13-01-closures.html#%E5%B0%86%E8%A2%AB%E6%8D%95%E8%8E%B7%E7%9A%84%E5%80%BC%E7%A7%BB%E5%87%BA%E9%97%AD%E5%8C%85%E5%92%8C-fn-trait
