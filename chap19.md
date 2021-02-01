# chap 19 [标准库类型](https://rustwiki.org/zh-CN/rust-by-example/std.html#标准库类型)

## 标准库的自定义类型

这些自定义类型是在**原生类型**基础上扩展的类型：

- 可变长的字符串类型`String`，如 `"hello world"`
- 可变长的向量 `Vector`：`[1, 2, 3]`
- 有无项类型 `Option<T>`
- 错误处理类型：`Result<T, E>`
- 堆指针类型：`Box<i32>`

## [箱子、栈和堆](https://rustwiki.org/zh-CN/rust-by-example/std/box.html#箱子栈和堆)

- `Rust`中所有值默认是栈分配的
- `Box<T>`可以把值分配在堆上
- `box` 是`Box<T>`的实例
    - 它是智能指针
        - 智能：自动调用析构函数，释放堆上的资源
    - 指向堆上的值
        - 堆上的值可以通过 `*` 解应用
    - `mem::size_of_val(&object)`：查看 `object` 占用的字节

## [动态数组 vector](https://rustwiki.org/zh-CN/rust-by-example/std/vec.html#动态数组-vector)

- 可变长数组 `Vector` 大小在编译时是未知的

    - 一个指向数据的指针
    - `vector` 的长度
    - 它的容量
        - 超过容量，会给 `vector` 重新分配一段更大的容量

- 迭代器的收集

    - `iter<T>.collection() -> Vev<T>`

- `Vector` 的初始化

    - let mut xs = vec![1,2,3]`

- `Vector` 的增、删、改、查

    - 增

        - `xs.push(4)`

    - 删

        - `xs.pop()`

    - 查

        - `xs.len()`
        - `xs[1]`：超过边界，引发 `panic`

    - 改

        - `xs.iter()`

        - `xs.iter_mut()`

            ```rust
            for x in xs.iter_mut() {
                *x = 3;
            }
            ```

        - `xs.iter().enumerate()`

## [字符串](https://rustwiki.org/zh-CN/rust-by-example/std/str.html#字符串)

- 可变长字符串 `String`
    - 由字节组成的 `vector`（`Vec<u8>`）
        - 虽然是 `u8` 数组，但一定是有效的 `UTF8` 序列
        - 多个 `u8` 组成一个 `UTF8` 字符
    - 特点
        - 堆分配
        - 可增长
        - 不是零结尾
- 字符串切片 `str`
    - 字符串切片借用：`&str`
        - `pointer`：`x.as_ptr()`
        - `length`：`x.len()`
    - 有效 `UTF-8` 序列

### [字面量与转义字符](https://rustwiki.org/zh-CN/rust-by-example/std/str.html#字面量与转义字符)

- 字符串字面量都会产生类似的 `&str`

- 字节串（byte string）字面量都会产生 `&[u8; N]` 类型

- 可以用十六进制值来表示字节

    - `println!("I'm writing \x52\x75\x73\x74")`
        - `I'm writing Rust`

- 使用 `Unicode` 码位表示

    - `let unicode_codepoint = "\u{211D}"`

    - `let character_name = "\"DOUBLE-STRUCK CAPITAL R\"";`

    - `println!("Unicode character {} (U+211D) is called {}", unicode_codepoint, character_name );`

        ```bash
        Unicode character ℝ (U+211D) is called "DOUBLE-STRUCK CAPITAL R"
        ```

- 原始字符串（raw string）

    - `r"ab \x3f"` ：`ab \x3f`
    - 在原始字符串中写引号，请在两边加一对 `#`
        - `r#" "There is no escape!" "#`
    - 字符串中需要写 `#`，那就在定界符中使用更多的 `#`
        - `r###" A"#  in, and "##! "###`
        - `r###"  ##  "###`

- 使用 `UTF8` 可以通过 `str` 和 `String`

- 使用非 `UTF8` 只能通过字节数组

    - `let bytes_string: &[u8; 20] = b"this is "`
    - 单字节转义字符：`let 转义字符 = b"\x52\x53";`

## [选项 `Option`](https://rustwiki.org/zh-CN/rust-by-example/std/option.html#选项-option)

如果只要判断函数执行的成功与失败，可使用 `Option` 枚举类型

`Option<T>` 有两个变量

- `None`，表明函数执行失败
- `Some(value)`，函数执行成功，返回有效值

```rust
fn checked_div(被除数, 除数) -> Option<i32> {
    if 除数 == 0 { None }
    else { Some(被除数/除数)}
}
```

## [结果 `Result`](https://rustwiki.org/zh-CN/rust-by-example/std/result.html#结果-result)

如果要要强调函数**为什么**会失败，可以使用 `Result` 枚举类型

`Result<T, E>` 类型的两个变体：

- `Ok(value)`：执行成功及其有效结果
- `Err(why)`：执行失败，及其包装的原因

## [`?` 运算符](https://rustwiki.org/zh-CN/rust-by-example/std/result/question_mark.html#-运算符)

`?` 用在返回值为 `Result` 的表达式后面

等同于 `match` 表达式：

- `Err(err)` 分支展开成提前返回的 `return Err(err)`
- `Ok(ok)` 分支展开成 `ok` 表达式

```rust
fn op(x, y) -> MathResult {
    let ratio = div(x, y)?
    let ln = ln(ratio)?
}
```

- 如果 `div` 失败，返回 `Err(MathError::DivisionByZero)`
- 如果 `ln()` 执行失败，返回 `Err(MathError::NegativeLog`)

## [`panic!`](https://rustwiki.org/zh-CN/rust-by-example/std/panic.html#panic)

`panic!` 产生恐慌，并开始回退`unwind`栈

回退栈的同时，运行时将会释放该线程所**拥有**的所有资源

`panic!` 不会泄露内存

## [散列表 HashMap](https://rustwiki.org/zh-CN/rust-by-example/std/hash.html#散列表-hashmap)

- `HashMap`（散列表）通过键（key）来索引值
- 键是任意实现了 `Eq` 和 `Hash` trait 的类型
- `HashMap` 在占据了多余空间时可以缩小自己
    - `HashMap::with_capacity(unit)` 创建具有一定初始容量的 `HashMap`
    - `HashMap::new()` 来获得一个带有默认初始容量的 `HashMap` （推荐）
- 常用方法
    - 初始化：`let mut contacts = HashMap::new()`
    - 增：`contacts.insert("Rayn", "789-1234")`
        - 如果插入的值已经存在于 `HashMap` 中新值将会替换旧的值
    - 删：`contacts.remove(&"Rayn")`
    - 查：`contacts.get(&"Rayn")`
        - `for (contact, &num) in contacts.iter()`
        - 迭代器以任意顺序举出`(&'a key, &'a value)`

## [更改或自定义关键字类型](https://rustwiki.org/zh-CN/rust-by-example/std/hash/alt_key_types.html#更改或自定义关键字类型)

任何实现了 `Eq` 和 `Hash` trait 的类型都可以充当 `HashMap` 的键

- `bool`
- `int`、`unit`　和其他整数类型
- `String` 和`&str`
- 所有实现了 `Eq` 和 `Hash`的集合
    - 甚至是自定义的结构体也可以作为查询键
    - 通过`\#[derive(PartialEq, Eq, Hash)]`实现自定义集合的特性

```rust
#[dervie(PartialEq, Eq, Hash)]
struct Account<'a> {
    username: &'a str,
    passwd: &'a str,
}

struct AccountInfo<'a> {
    name: &'a str, 
    email: &'a str,
}
type Accounts<'a> = HashMap<Account<'a>, AccountInfo<'a>>;

fn try_logon<'a>(accounts: &Acounts<'a>, username: &'a str, passwd: &'a str) {
    let logon = Account {username, passwd};
    match accounts.get(&logon) {
        Some(account_info) => {
            println!("登陆成功");
            println!("名称：{}", account_info.name);
            println!("email: {}", account_info.email);
        },
        _ => println!("登陆失败")
    }
}

fn main() {
    let mut accounts: Accounts = HashMap::new();
    
    let account = Account {
        username: "Ryan",
        passwd: "123456",
    }
    let account_info = AccountInfo {
        name: "John Everyman",
        email: "j.everyman@email.com",
    }
    accounts.insert(account, account_info);
    try_logon(&accounts, "Ryan", "654321");
    try_logon(&accounts, "Ryan", "123456");
}
```

## [散列集 HashSet](https://rustwiki.org/zh-CN/rust-by-example/std/hash/hashset.html#散列集-hashset)

`HashSet`是只关心键而不关心值的`HashMap`

`HashSet<T> = HashMap<T, ()>`

`HashSet` 相对于`Vec`保证了不会出现重复的元素

集合（set）拥有 4 种基本操作

- `union`：并集 `A + B`
- `difference`：差集 `A - B`
- `intersection` ：交集
- `symmetric_difference`：对称差f
    - 获取所有只属于其中一个集合，而不同时属于两个集合的所有元素