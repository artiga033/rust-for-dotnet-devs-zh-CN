# 复合类型

.NET 中常用的对象和集合类型以及它们到 Rust 的映射

| C#           | Rust      |
| ------------ | --------- |
| `Array`      | `Array`   |
| `List`       | `Vec`     |
| `Tuple`      | `Tuple`   |
| `Dictionary` | `HashMap` |

## 数组

在 Rust 和 .NET 中，定长数组以同样的方式支持。

C#:

```csharp
int[] someArray = new int[2] { 1, 2 };
```

Rust:

```rust
let someArray: [i32; 2] = [1,2];
```

## 列表

Rust 中 `List<T>` 的等价物是 `Vec<T>`。数组可以转换成 Vec，反之亦然。

C#:

```csharp
var something = new List<string>
{
    "a",
    "b"
};

something.Add("c");
```

Rust:

```rust
let mut something = vec![
    "a".to_owned(),
    "b".to_owned()
];

something.push("c".to_owned());
```

## 元组

C#:

```csharp
var something = (1, 2)
Console.WriteLine($"a = {something.Item1} b = {something.Item2}");
```

Rust:

```rust
let something = (1, 2);
println!("a = {} b = {}", something.0, something.1);

// deconstruction supported
let (a, b) = something;
println!("a = {} b = {}", a, b);
```

> **备注**：Rust 元组的元素不像 C# 那般是可命名的。
> 唯一访问元组元素的办法就是使用该元素的索引，要么就解构元组。

## 字典

Rust 中 `Dictionary<TKey,TValue>` 的等价物是 `Hashmap<K,V>`。

C#:

```csharp
var something = new Dictionary<string, string>
{
    { "Foo", "Bar" },
    { "Baz", "Qux" }
};

something.Add("hi", "there");
```

Rust:

```rust
let mut something = HashMap::from([
    ("Foo".to_owned(), "Bar".to_owned()),
    ("Baz".to_owned(), "Qux".to_owned())
]);

something.insert("hi".to_owned(), "there".to_owned());
```

另见：

- [Rust 标准库 - 集合](https://doc.rust-lang.org/std/collections/index.html)
