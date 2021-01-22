# 注释

- 普通注释
    - `// `单行注释
    - `/* ... */` 区块注释
- 文档注释（解析成 HTML 帮助[文档](https://rustwiki.org/zh-CN/rust-by-example/meta/doc.html)）
    - `///` 生成库文档，用于函数或结构体的说明
    - `//!` 生成库文档，用于说明整个模块的功能

# 格式化输出

  `format!`: 将格式化文本写到字符串（String），并返回
  `print!`：与 `format!` 类似，但将文本输出到控制台（io::stdout）
  `eprint!`：但将文本输出到标准错误（io::stderr）
  `fmt::Debug`用`{:?}`标记，可以通过`#[derive(Debug)]`自动出显示格式
  `fmt::Display`用`{}`标记，实现自定义格式化输出

    ```rust
    use std::fmt;
    impl fmt::Display for for Structure {
      fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result{
        write!(f, "{}",self.0)
      }
    }
    ```

  `?`操作符：宏`try!()`的简写，比如：`write!(f, "{}", value)?`当发生错误时，`?`会返回相应的错误；没有出错继续执行后面的语句


# 格式化

`format!("{}", foo)`变量（foo）根据使用的参数类型（`X`, `o`）格式化成不同的形式，每一种参数类型都对应一种 trait。

参数类型为未指定是，调用的是`Display trait`，有时需要自己实现

# 原生类型

## 标量类型

类型确定方式：

1. 显式地给出**类型说明**： `let a: bool = true;`
2. 通过**后缀** （suffix）或**默认方式**来声明类型：`let a = 5u32`
3. 编译器自动推断

# [字面量和运算符](https://rustwiki.org/zh-CN/rust-by-example/print.html#字面量和运算符)

1. 数值字面量中插入下划线，`1_0000`
2. 后面接字面量的类型：`5u32`
3. 位运算：`&, |, ^, <<, >>`
4. 布尔运算的非：`!`

# [元组](https://rustwiki.org/zh-CN/rust-by-example/print.html#元组)

元组是包含各种类型的值的组合，使用`()`

元组可以`{:?}`打印

很长的元组无法打印

单元素元组需要一个额外的逗号：`(5u32, )`

# [数组和切片](https://rustwiki.org/zh-CN/rust-by-example/print.html#数组和切片)

数组：内存中相同类型T的对象集合，大小编译时确定，类型标记：`[T; size]`

- 定长数组：`let xs = [0; 500]`

切片：切片引用连续的内存，切片不是直接创建的, 而是从现有变量创建的。切片是一个双字（usize）对象（指针和长度），类型标记：`&[T]`

- 数组可以自动被借用成为 slice：`&xs`





- `use`声明（类似`python import`）
    - `use SomeStruct::{Poor, Rich}` 在当前空间使用`Poor`和`Rich`
    - `use Work::*`：自动使用`Work`内部各名称
    
- `enum` 的 C 风格
    - `enum` 的隐式辨别值：`enum Number { zero, One, Two, }`
    - enum 的显式辨别值：`enum Color {Red = 0xff0000,Green = 0x00ff00}`
    
- 常量
    - `const`：不可改变的值
    - `static`：具有 [`'static`](https://rustwiki.org/zh-CN/rust-by-example/scope/lifetime/static_lifetime.html) 生命周期的，可以是可变的变量（`static mut`）
    - 作用域和C一样
    
- 变量
    - 只读与可变变量
        - `let` 绑定：将值（比如字面量）绑定（bind）到“只读”变量
        - `mut` 关键字完成“可变”变量绑定
    - 变量的作用域：被限定只在一个**代码块**中
    - 先声明，后初始化；禁止使用未经初始化的变量
    
- 类型系统
    
    - `as` 关键字显式类型转换
        - `T` 转成无符号数
            - 通过不断加上或减去 `(std::T::MAX + 1)`直到值位于新类型 T 的范围内
            - `1000 as u8: 1000 - 256 - 256 - 256 = 232`，对正数等于取模
            - `-1 as u8: -1 + 256 = 255`
        - `T` 转成有符号数
            - 先转换到对应的无符号类型，如果 MSB 是 1，则该值为负
            - `128 as i8: -128`
    - 字面量
        - 数值字面量类型作为后缀`5i32`
        - 默认编译器会对整数使用 `i32`，对浮点数使用 `f64`
    - 类型推断
        - 引擎会考察变量之后会怎样使用，借此推断类型
    - 类型别名
        - 遵循驼峰命名法
        - `#[allow(non_camel_case_types)]`
        - 类型别名*并不能*提供额外的类型安全，因为别名*并不是*新的类型
    - 类型之间的转换
        - 用[`From`](https://doc.rust-lang.org/std/convert/trait.From.html) 和 [`into`](https://doc.rust-lang.org/std/convert/trait.Into.html) 两个 trait 实现转换
            - [`From`](https://doc.rust-lang.org/std/convert/trait.From.html) trait 
                - 怎么根据另一种类型生成自己
                - `String::from(my_str)` ：根据 `str` 生成自己`String`类型
            - [`Into`](https://doc.rust-lang.org/std/convert/trait.Into.html) trait 
                - 实现了 `From` 就实现了 `Into`
        - [`TryFrom`](https://doc.rust-lang.org/std/convert/trait.TryFrom.html) 和 [`TryInto`](https://doc.rust-lang.org/std/convert/trait.TryInto.html) 类型转换的通用 trait
            - 用于易出错的转换，返回值是 [`Result`](https://doc.rust-lang.org/std/result/enum.Result.html) 型
        - [`ToString`](https://doc.rust-lang.org/std/string/trait.ToString.html) 和 `FromStr`
            - [`ToString`](https://doc.rust-lang.org/std/string/trait.ToString.html) trait 会把任何类型转换成 `String`
            - 推荐：实现[`fmt::Display`](https://doc.rust-lang.org/std/fmt/trait.Display.html) trait，它会自动提供 [`ToString`](https://doc.rust-lang.org/std/string/trait.ToString.html)，并且还可以用来打印类型
    - [解析字符串](https://rustwiki.org/zh-CN/rust-by-example/print.html#解析字符串)
        -  [`parse`](https://doc.rust-lang.org/std/primitive.str.html#method.parse) 函数把字符串转成数字
        - 只要对**目标类型**实现了 [`FromStr`](https://doc.rust-lang.org/std/str/trait.FromStr.html) trait，就可以用 `parse` 把字符串转换成目标类型
    - [表达式](https://rustwiki.org/zh-CN/rust-by-example/print.html#表达式)
        - 代码块也是表达式
        - 代码块最后一条表达式结尾处有分号，那 么返回值将变成 `()`
    
- [流程控制](https://rustwiki.org/zh-CN/rust-by-example/print.html#流程控制)

    - [`if/else`](https://rustwiki.org/zh-CN/rust-by-example/print.html#ifelse): 分支都必须返回相同的类型

    - [loop 循环](https://rustwiki.org/zh-CN/rust-by-example/print.html#loop-循环)

        - 使用 `break` 语句在任何时候退出一个循环
        -  `continue` 跳过循环体的剩 余部分并开始下一轮循环

    - [嵌套循环和标签](https://rustwiki.org/zh-CN/rust-by-example/print.html#嵌套循环和标签)

        - `break` 或 `continue` 到外层`'label`（标签）注明的地方

            - 中断外层循环

                ```rust
                'outer: loop {
                    println!("Entered the outer loop");
                
                    'inner: loop {
                        println!("Entered the inner loop");
                
                        // 这只是中断内部的循环
                        //break;
                
                        // 这会中断外层循环
                        break 'outer;
                    }
                ```

            - [从 loop 循环中返回](https://rustwiki.org/zh-CN/rust-by-example/print.html#从-loop-循环中返回)

                - `break` 之后的值，它就会被 `loop` 表达式返回
                - `break count * 2;`

    - [while 循环](https://rustwiki.org/zh-CN/rust-by-example/print.html#while-循环)

        - `while n < 100 { ... }`

    - [for 循环](https://rustwiki.org/zh-CN/rust-by-example/print.html#for-循环)

        - `for in` 结构可以遍历一个 `Iterator`（迭代器）
        - 区间标记迭代器： `a..b`，a..=b
        - `for` 循环会对给出的集合应用 `into_iter` 函数，把它转换成 一个迭代器
            - `iter` - 在每次迭代中借用集合中的一个元素
            - `into_iter` - 消耗集合，集合中的数据在循环中被 “转移”（move）
            - `iter_mut` - 可变地（mutably）借用集合中的每个元素

- [match 匹配](https://rustwiki.org/zh-CN/rust-by-example/print.html#match-匹配)

- [解构](https://rustwiki.org/zh-CN/rust-by-example/print.html#解构)

    - [元组](https://rustwiki.org/zh-CN/rust-by-example/print.html#元组-1)

        ```rust
        let pair = (0, -2);
        match pair {
            (0, y) => ...,
        }
        ```

    - [枚举](https://rustwiki.org/zh-CN/rust-by-example/print.html#枚举-1)

        ```rust
        enum Color {Red,Blue,RGB(u32, u32, u32),}
        match color {
            Color::Red   => ...,
            Color::RGB(r, g, b) => ...
        }
        ```

        