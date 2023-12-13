# 接口(Interfaces)

Rust 没有 C#/.NET 中的接口。作为替代，它使用 _trait_。和接口类似，一个 trait 表示一种抽象，同时形成一种约束：当类型实现 trait 时，必须实现它的所有成员。

就像 C#/.NET 接口可以有默认方法（默认实现的方法体作为方法定义的一部分），Rust 中的 trait 也一样。实现接口/trait 的类型也可以随后提供一个更合适的或优化过的实现。

C#/.NET 的接口可以拥有所有类型的成员，属性、索引器、事件、方法等，既可以是静态的也可以是实例的。类似地，Rust trait 可以有（实例）方法、关联函数（当作是 C#/.NET 中的静态方法）、常量。

除了类架构，接口是通过动态分发实现多态的核心概念，这创造了横向的抽象。通过接口，可以编写针对接口的通用代码，而无需关心实现它的具体类型。Rust 的 trait 也可以有限地取得同样的效果。trait 对象实际上是一张 _v-table_ （虚表），它通过 `dyn` 关键字后跟上 trait 名来标识，例如 `dyn Shape`（其中 `Shape` 是trait名）。trait 对象总是隐藏在指针之后，通过引用（例如 `&dyn Shape`）或堆上的 `Box`（例如 `Box<dyn Shape>`。这与 .NET 有些类似，接口是引用类型，因此值类型转换成接口会被自动装箱到托管堆上。前文提到过 trait 对有些限制，那就是原始的实现类型无法被恢复。也就是说，虽然转换或者测试一个接口是否是某个实例是非常常见的场景，但在 Rust 中是不可能的（如果没有一些额外的工作和支持）。