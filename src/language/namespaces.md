# 命名空间

命名空间在 .NET 中用于整理类型，也用来控制项目中类型和方法的作用域。

在 Rust 中，命名空间是一个不同的概念。Rust 中命名空间的等价物是[模块][rust-module]。不管是 C# 还是 Rust，程序项的可见性都能够用可访问性修饰符，又叫可见性修饰符，来限制。Rust 中，默认的可见性是 _私有的（private）_（只有少数例外）。Rust 中和 C# 的 `public` 等价的是 `pub`，`internal` 则相当于 `pub(crate)`。如果需要更多细粒度的访问控制，参考 [visibility modifiers]

[rust-module]: https://doc.rust-lang.org/reference/items/modules.html
[visibility modifiers]: https://doc.rust-lang.org/reference/visibility-and-privacy.html
