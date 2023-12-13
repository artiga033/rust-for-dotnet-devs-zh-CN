# 继承

在[结构][structures]一节中解释过，不像 C#，Rust 不提供（基于类的）继承。一种为结构提供共享行为的方法是使用 trait。然而， 就像 C# 中的 _接口继承_，Rust 允许定义 trait 之间的关系，通过使用[_父 trait_][supertrait.rs]。

[structures]: ./custom-types/structs.md
[supertrait.rs]: https://kaisery.github.io/trpl-zh-cn/ch19-03-advanced-traits.html#%E7%88%B6-trait-%E7%94%A8%E4%BA%8E%E5%9C%A8%E5%8F%A6%E4%B8%80%E4%B8%AA-trait-%E4%B8%AD%E4%BD%BF%E7%94%A8%E6%9F%90-trait-%E7%9A%84%E5%8A%9F%E8%83%BD
