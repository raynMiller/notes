第10、11章 模块和crate

- 模块系统作用
    - 模块是项（item）的集合	
        - 函数
        - 结构体
        - `trait``
        - ``impl` 块
        - 其它模块
    - 将代码按层次分成多个逻辑单元（模块）
    - 管理模块之间的可见性（公有（public）或私有（private））

[可见性](https://rustwiki.org/zh-CN/rust-by-example/mod/visibility.html#可见性)

- 模块中的项默认私有的可见性
- 公有的（public）项可以从模块外的作用域访问
- 模块中的项默认私有
    - `mod my_mod { fn private_func() {...} }`
    - 私有项只能被本模块中的项使用
    - `mod my_mod { fn in_access() {private_func();} }`
- `pub` 修饰语可以改变默认可见性
    - `mod my_mod { pub fn pub_func() {... } } `
- 模块也可以嵌套
    - `pub mod 模块 { pub mod 嵌套模块 { ... }}`
- `pub(in path)` 语法指定函数可见域
    - `pub mod 我的模块 { pub mod 嵌套模块 {pub (in 我的模块) fn pub_func_in_我的模块() { ... }}}`
    - `pub (in path)`中的`path` 必须是父模块（parent module）或祖先模块（ancestor module）
    - `pub(in path)` 项只能在指定的模块中访问
-  `pub(self)` 语法定义的函数则只在**当前模块**中可见
    - `pub(self) fn pub_in_current_mod() { ... }`
- `pub(super)` 语法定义的函数只在**父模块**中可见
    - `pub(super) fn pub_in_super_mod() { ... }`
- `pub(crate)` 使得函数只在当前 `crate` 中可见
    - `pub(crate) fn pub_in_current_crate() { ... }`
    - `pub(crate)` 项可以在同一个 `crate` 中的任何地方访问
- 公有项，都可以在父模块外部访问（包括模块内嵌套的模块）
- 模块分布
    - `mod my_mod`
        - `fn private_function()`
        - `pub fn function()`
            - 可在父模块外部如`main()`中用`my_mod::function()`访问
        - `pub fn indirect_access() { private_function() }`
        - `pub mod nested`
            - `pub fn function()`
            - `fn private_function()`
            - `pub(in my_mod) fn public_function_in_my_mod()`
                - 只能在指定的模块`my_mod`中访问
                - 在`my_mod`中通过`nested::public_function_in_my_mod()`访问
                - 不能在`main()`中通过`my_mod::nested::public_function_in_my_mod()`访问
                - 因为函数 `public_function_in_my_mod` 是私有的
                - 模块的私有项不能直接访问，即便它是嵌套在公有模块内部的
            - `pub(self) fn public_function_in_nested()`
            - `pub(super) fn public_function_in_super_mod()`
        - `pub fn call_public_function_in_my_mod()`
        - `pub(crate) fn public_function_in_crate()`
        - `mod private_nested`
            - `pub fn function()`
- [结构体字段的可见性](https://rustwiki.org/zh-CN/rust-by-example/mod/struct_visibility.html#结构体的可见性)
    - 从结构体被定义的模块之外访问其字段时，这个可见性才会起作用
    - 字段默认拥有私有，意义是隐藏信息（即封装，encapsulation）
    - 字段分布
        - `mod my`
            - `pub struct OpenBox<T>`
                - `pub contents: T`
            - `pub struct ClassBox<T>`
                - `contents: T`
                - 公有的结构体，带有一个私有的字段
            - `impl<T> ClosedBox<T>`
                - `pub fn new(contents: T) -> ClosedBox<T>` 公有的构造器方法
        - `main()` 在 `main` 中可以访问
            - `my::OpenBox { .... }`带有公有字段的公有结构体
            - `my::ClosedBox { contents: "classified information" }` 错误：
                - 带有私有字段`contents`的公有结构体`ClosedBox`不能使用字段名来构造
            - `my::ClosedBox::new("classified information")`
                - 带有私有字段`contents`的结构体`ClosedBox`可以使用公有的构造器`new`来创建
                - 但私有字段`contents`不能访问：`closed_box.contents` 错误
- [`use` 声明](https://rustwiki.org/zh-CN/rust-by-example/mod/use.html#use-声明)
    - 将一个完整的路径绑定到一个新的名字，更容易访问
        - 将 `deeply::nested::function` 路径绑定到 `other_function`
            - `use deeply::nested::function as other_function;`
        - `use deeply::nested::function;` === `use deeply::nested::function as function`
        - `use` 绑定拥有局部作用域
- [`super` 、`self`和`crate`](https://rustwiki.org/zh-CN/rust-by-example/mod/super.html#super-和-self)
    - 在路径中使用 `super` （父级）和 `self`（自身）关键字，防止不必要的路径硬编码
    - 路径分布图
        - `fn function`
        - `mod cool`
            - `pub fn function`
        - `mod my`
            - `mod cool`
                - `pub fn function`
            - `pub fn indirect_call`
                - `self::function()`：`self` 关键字表示当前的模块作用域（在这个例子是 `my`）
                - `function` === `my::function` === `self::function`
                - `super::function()`：`super` 关键字表示父作用域（在 `my` 模块外面）
                    - `super::function` === `crate::function`
            - `use crate::cool::function as root_function;` 
                - 在 *crate* 作用域内绑定 `cool::function`
                - crate 作用域是最外面的作用域
- [文件分层](https://rustwiki.org/zh-CN/rust-by-example/mod/split.html#文件分层)
    - 模块可以分配到文件/目录的层次结构
    - 目录结构
        - `my/`
            - `inaccessible.rs`
                - `pub fn public_function`：外部通过`my::inaccessible::public_function`访问
            - `mod.rs`
                - `mod inaccessible`：找`inaccessible.rs` 文件，并在它们放到`inaccessible`模块中
                - `pub fn function`：外部通过`my::function`访问
            - `nested.rs`
                - `pub fn function`：外部通过`my::nested::function`访问
        - `split.rs`
            - `mod my`：
                - 查找名为 `my.rs` 或 `my/mod.rs` 的文件
                - 将该文件的内容放到此作用域中一个名为 `my` 的模块里面
            - `my::function`：访问的是`my/mod.rs`中的`function`





# crate 包装箱

- crate 是 Rust 的编译单元

- `rustc some_file.rs` 将`some_file.rs` 被当作 **crate 文件**。文件中含有 `mod` 声明的地方，rust 将模块文件的内容将在编译之前被插入 crate 文件的相应声明处

- 模块用于`include`导入，crate用于编译

- crate编译成：

    - 二进制可执行文件（binary）
    - 库文件（library）

- 库链接到另一个 `crate`

    - 创建库 `rustc --crate-type=lib rary.rs`

        - 将`rary.rs`编译成：`library.rlib`
        - 可以通过[`crate_name` ](https://rustwiki.org/zh-CN/rust-by-example/attribute/crate.html) 属性修改默认名称

    - `extern crate rary;` 

        - [`extern crate`](https://rustwiki.org/zh-CN/rust-by-example/crates/link.html#extern-crate) 声明

        - 链接到 `rary` 库
        - 导入其中的项
            - 用一个与库名相同的模块来存放库里面的所有项
        - 使用：`rary::public_function();`

    - 命令行编译

        - `rustc executable.rs --extern rary=library.rlib`
        - `library.rlib` 是已编译好的库的路径，这里假设它在同一目录下

