[泛型](https://rustwiki.org/zh-CN/rust-by-example/generics.html#泛型)

目的：泛化类型和函数功能，以扩大其适用范围，减少代码重复

泛型最简单和常用的用法是用于类型参数

语法

泛型的**类型参数**是使用**尖括号**和[**大驼峰命名**](https://en.wikipedia.org/wiki/CamelCase)的名称：`<Aaa, Bbb, ...>` 来指定

泛型函数：`fn foo<T>(arg: T) { ... }`

泛型类型参数 `<T>`，所以这里的 `(arg: T)` 中的 `T` 就是泛型类型

`struct SingleGen<T>(T);`

`<T>` 在第一次使用 `T` 前出现，所以 `SingleGen` 是一个**泛型类型**

因为 `T` 是泛型的，所以它可以是任何类型

    let _t    = SingleGen(A); // 使用在上面定义的 `A`
    let _i32  = SingleGen(6); // 使用 `i32` 类型
    let _char = SingleGen('a'); // 使用 `char`
14.1 [函数](https://rustwiki.org/zh-CN/rust-by-example/generics/gen_fn.html#函数)

使用类型 `T` 前声明 `<T>`，那么 `T` 就变成了泛型

函数使用显式指定的类型参数：`fun::<A, B, ...>()`

`struct SGen<T>(T);`： 泛型类型

`fn reg_fn(_s: S) {}`：普通函数

`fn gen_spec_t(_s: SGen<A>) {}`：普通函数

`fn generic<T>(_s: SGen<T>) {}`：泛型函数，第一个`<T>`是泛型参数声明，第二个`<T>`是使用泛型参数

`gen_spect_t(SGen(A))`：隐式地指定类型参数 `A`。

`generic::<char>(SGen('a'))`：显式地指定类型参数 `char`

`generic(SGen('a'))`：隐式地指定类型参数 `char`，由编译器推导出类型

[实现](https://rustwiki.org/zh-CN/rust-by-example/generics/impl.html#实现)

`impl` 块实现泛型

`struct GenericVal<T>(T,);`： 泛型类型 `GenericVal`

`impl GenericVal<f32> {}` 显示指定泛型参数为`f32`

`impl<T> GenericVal<T> {}`：声明泛型参数 `T`

`impl<T> GenVal<T> { fn value(&self) -> &T{ &self.gen_val } }`：实现泛型函数



[trait](https://rustwiki.org/zh-CN/rust-by-example/generics/gen_trait.html#trait)

声明一个泛型 `trait`

```rust
trait DoubleDrop<T> {
    fn double_drop(self, _: T);
}
```

对泛型的调用者类型`U`实现`DoubleDrop<T>`的`trait`

```rust
impl<T, U> DoubleDrop<T> for U {
	fn double_drop(self, _: T) {}
}
```

使用方法：`aaa.double_drop(bbb);`

14.4 [约束](https://rustwiki.org/zh-CN/rust-by-example/generics/bounds.html#约束)

类型参数使用 trait 作为**约束**（bound）来明确规定类型应实现哪些功能

`T` 必须实现 `Display` trait。

```rust
fn printer<T: Display>(t: T) {
    println!("{}", t);
}
```

泛型的实例可以访问作为约束的 `trait` 的方法：

`println!("Area: {}", area(&rectangle));`

`area()`函数的实现

```rust
fn area<T: HasArea>(t: &T) -> f64 { 
    t.area() 
}
```

​	`area()`的参数`&rectangle`必须实现`HasArea`功能

```rust
trait HasArea {
    fn area(&self) -> f64;
}
```

`let rectangle = Rectangle { length: 3.0, height: 4.0 };`

这里 `Rectangle` 结构体的定义是：

```rust
struct Rectangle { length: f64, height: f64 }
```

我们为它实现了`HasArea`方法：

```rust
impl HasArea for Rectangle {
    fn area(&self) -> f64 { self.length * self.height }
}
```

总结：调用关系

`area(&rectangle)`

`(&rectangle).area()` 

`Rectangle::HasArea(&&rectangle)`

[空约束](https://rustwiki.org/zh-CN/rust-by-example/generics/bounds/testcase_empty.html#测试实例空约束)

`trait Red {}`

`impl Red for apple {}`

`trait Blue {}`

`impl Blue for BlueJay {}`

`fn red<T: Red>(_: &T) -> &'staic str {"red"}`

`let blue_jay = BlueJay;`

`red(&blue_jay)`（错误）

14.5 [多重约束](https://rustwiki.org/zh-CN/rust-by-example/generics/multi_bounds.html#多重约束)

`fn compare<T: Debug + Display>(t: &T) { ... }`

14.6 [where 分句](https://rustwiki.org/zh-CN/rust-by-example/generics/where.html#where-分句)

同时指定泛型的类型和约束

```rust
impl <A: TraitB + TraitC, D: TraitE + TraitF> MyTrait<A, D> for YourType {}
```

用 `where` 从句

```rust
impl <A, D> MyTrait<A, D> for YourType where
    A: TraitB + TraitC,
    D: TraitE + TraitF {
        ...
}
```

指定和泛型`<T>`有关的项的约束，指定泛型<T>关联项`Option<T>`的约束

```rust
impl<T> PrintInOption for T where
    Option<T>: Debug {
        ...
}
```

14.7 [new type 惯用法](https://rustwiki.org/zh-CN/rust-by-example/generics/new_types.html#new-type-惯用法)

为不同种类的数据分别定义新的类型

`struct Years(i64); struct Days(i64);`

14.8 [关联项](https://rustwiki.org/zh-CN/rust-by-example/generics/assoc_items.html#关联项)

将类型占位符与 `trait` 联系起来的做法

 以便 `trait` 中的方法签名可以使用这些占位符类型

`trait` 的实现会指定占位符对应的具体类型

`trait` 的 使用者**必须**指出 `trait` 的全部泛型类型

定义一个容器结构体:`struct 容器(i32, i32)`

声明一个包含接口，指定要泛型参数`A, B`

```rust
trait Contains<A, B> {
    fn contains(&self, _: &A, _: &B) -> bool;
}
```

实现这个接口，显示的具体化泛型参数

```rust
impl Contains<i32, i32> for 容器 {
    fn contains(&self, num1: &A, num2: &B) -> bool {
        &self.0 == num1 && &self.1 == num2
    }
}
```

计算`容器`的差别：

```rust
fn diff<A, B, C>(container: &C) -> i32 where
	C: Container<A, B> {
        container.last() - container.first()
}
```

函数 `diff()` 只有 `container` 一个参数，但是容器 `C` 又包含了 `A` 和 `B` 类型，所以必须指出 `A` 和 `B` 

通过[关联类型](https://rustwiki.org/zh-CN/rust-by-example/generics/assoc_items/types.html#关联类型)重构

```rust
trait Contains {
    type A;
    type B;
    fn contains(&self, &Self::A, &Self::B) -> bool;
}
```

使用了关联参数占位符后，包含`Contains trait`的函数就不用再显示指明trait中的泛型参数

```rust
fn diff<C: Contains>(container: &C) -> i32 {
	...
}
```

泛型`trait Contains`的实现

```rust
impl Contains for Container {
    type A = i32;
    type B = i32;
    fn contains(&self, num1: &i32, num2: &i32) -> bool {
        (&self.0 == num1) && (&self.1 == num2)
    }
}
```

`diff`的实现

```rust
fn diff<C: Contains>(container: &C) -> i32 {
    container.last() - container.first()
}
```



14.9 [虚类型参数](https://rustwiki.org/zh-CN/rust-by-example/generics/phantom.html#虚类型参数)

仅在编译时进行静态检查的类型参数，运行时不出现

作用：类充当标记，用于编译期的静态类型检测，执行更严格的逻辑或者约束

特点：没有存储值，也没有运行时行为

理解：`PhantomData<T>`字段指示编译器认为结构体拥有类型为`T`的值，但实际上结构体并没有用到这个值。

比如：类型或生命期参数逻辑上与结构体关联，但是实际上并不是结构体字段的一部分

示例说明

`struct PhantomTuple<A, B>(A, PhantomData<B>);`

声明一个虚元组结构体，对 `A` 是泛型的，并且带有隐藏参数 `B`

`let tuple: PhantomTuple<char, f32> = PhantomTuple('Q', PhantomData);`

被指定为 `<char, f32>` 的 `PhantomTuple` 类型，这里的`f32`是隐藏参数

充当数据的单位

```rust
# 没有PhantomData
type USD = f64;
type CNY = f64;
struct Money<M>(M);

impl<M: Add<Output=M>> Add for Money<M> {
    type Output = Money<M>;
    fn add(self, rhs: Money<M>) -> Money<M> {
        Money(self.0 + rhs.0)
    }
}
# 相加
let usd = Money(5.0 as USD);
let cny = Money(5.0 as CNY);
usd + cny // 合法
```

通过类型系统本身完成货币单位的约束

```rust
mod currency {
    pub enum USE{}
    pub enum CNY{}
}
struct Money<M, P>(M, PhantomData<P>);
impl<M: Add<Output=M>, P> Add for Money<M, PhantomData<P>> {
    type Output = Money<M, PhantomData<P>>;
    fn add(self, rhs: Money<M, PhantomData<P>>) -> Money<M, PhantomData<P>> {
        Money(self.0 + rhs.0, PhantomData)
    }
}
let usd: Money<f64, PhantomData<currency::USD>> = Money(5.0, PhantomData);
let cny: Money<f64, PhantomData<currency::CNY>> = Money(5.0, PhantomData);
usd + cny // 错误
```

出现错误：

```bash
usd + cny
    ^ expected enum `currency::USD`, found enum `currency::CNY`
```

上面的`PhantomData`实际上作用于未使用的参数`<P>`上，比如下面的用法不能编译通过：

```rust
struct Money<Num, Unit> {
    num: Num,
}
```

要编译通过，需要使用`PhantomData<Unit>`标记一下：

```rust
struct Money<Num, Unit> {
    num: Num,
    _mark: PhantomData<Unit>,
}
```

类型或生命周期逻辑上与一个结构体关联起来了，但是却不属于结构体的任何一个成员

# [作用域规则](https://rustwiki.org/zh-CN/rust-by-example/scope.html#作用域规则)

作用域告诉编译器什么时候借用是合法的、什么时候资源可以释放、以及变量何时被创建或销毁

15.1 [RAII](https://rustwiki.org/zh-CN/rust-by-example/scope/raii.html#raii) （Resource Acquisition Is Initiallization，资源获取即初始化）

任何对象在离开作用域时，它的析构函数（destructor）就被调用，它占有的资源就被释放

## [析构函数](https://rustwiki.org/zh-CN/rust-by-example/scope/raii.html#析构函数)

通过 [`Drop`](https://doc.rust-lang.org/std/ops/trait.Drop.html) trait 提供，资源离开作用域，就调用析构函数。

[`Drop`](https://doc.rust-lang.org/std/ops/trait.Drop.html) trait 的工作机制

```rust
struct ToDrop;
impl Drop for ToDrop {
    fn drop(&mut self) {
        println!("ToDrop结构体被销毁");
    }
}
fn main () {
    let x = ToDrop;
} <-- 在此调用Drop::drop(&mut x);
```

15.2 [所有权和移动](https://rustwiki.org/zh-CN/rust-by-example/scope/move.html#所有权和移动)

所有权变量要负责释放它们拥有的资源，所以**资源只能拥有一个所有者**

Rust 中资源的**移动**（move）就是指的**所有权**（ownership）的转移

目的：避免悬挂指针

栈分配的整型：`let x = 5u32;`

值的复制： `x` *复制*到 `y`，不存在资源移动：`let y = x`

堆分配的整数的指针：`let a = Box::new(5u32)`

值的复制 `+` 所有权转移：`let b = a`

把 `a` 的指针地址（而非数据）复制到 `b`。现在两者都指向同一个堆分配的数据

但是现在是 `b` 拥有它

15.3.1 [可变性](https://rustwiki.org/zh-CN/rust-by-example/scope/move/mut.html#可变性)

所有权转移时，可以改变数据的可变性

```rust
let imbox = Box::new(1i32);
let mut mutbox = imbox;
*mutbox = 4;
```

15.3 [借用](https://rustwiki.org/zh-CN/rust-by-example/scope/borrow.html#借用)

访问数据，但不用管理数据的释放，这就是rust的借用机制。

对象可以通过引用（`&T`）来传递，从而取代通过 值（`T`）来传递

编译器有**借用检查**机制，保证引用**总是**指向有效的对象，引用不可能是野指针

15.3.1 [可变性](https://rustwiki.org/zh-CN/rust-by-example/scope/borrow/mut.html#可变性)

**可变引用**（mutable reference）使借用者可以读/写可变数据

实例：

不可变实例

```rust
let imbook = Book {
    author = "rayn",
    title = "bach",
}
```

可变实例，由`imbook`创建一个 `mutbook` 的可变拷贝

```rust
let mut mutbook = imbook;
```

引用

不可变引用实例

```rust
&imbook;
&mutbook;
```

可变地引用实例

```rust
&mut mutbook;
&mut imbook; //不能可变地借用一个不可变对象
```

15.3.2 [冻结](https://rustwiki.org/zh-CN/rust-by-example/scope/borrow/freeze.html#冻结)

当数据被不可变地借用时，它会在借用域中被**冻结**（freeze）。无法通过原始对象来修改，直到对这些数据的所有引用离开作用域为止

```rust
let mut mut_num = 100;
{
    let ref_mut_num = &mut_num;
    mut_num = 50; // `_mutable_integer` 在本作用域被冻结
}
mut_num = 200; //在这作用域没有冻结
```

15.3.3 [可变引用与不可变引用](https://rustwiki.org/zh-CN/rust-by-example/scope/borrow/alias.html#别名使用)

初始化一个可变的`Point`实例`point`

```rust
fn main() {
   let mut point = Point{x: 0, y: 0, z: 0};
```

不可变借用的期间，不能可变借用

```rust
{
    let brw_inst_a = &point;
    let brw_inst_b = &point;
    
    //不能可变地借用 `point` ，它已有不可变的借用
    let mut mbrw_inst = &mut point; 
}
```

可变借用后，不能不可变借用

```rust
let mut mbrw_inst = &mut point;
mbrw_inst.x = 5;

// 不能不可变地借用 `point`
//let y = &point.y;
// 不能打印，因为 `println!` 会创建一个不可变引用
//println!(point.z);
// 可变引用可以作为不可变的传给 `println!`
println!(mbrw_inst.x, mbrw_inst.y);
```

14.3.4 [`ref` 模式](https://rustwiki.org/zh-CN/rust-by-example/scope/borrow/ref.html#ref-模式)

`ref` 关键字用来创建字段的引用

`let ref a = c` === `let a = &c`

解构一个结构体时

`let Point{x: ref ref_x, y: _} = point;`指向 `point` 的 `x` 字段的引用

`ref` 可以与 `mut` 结合以创建可变引用

```rust
let mut mut_point = point;
let Point{x: _, y: ref mut mref_y} = mut_point;
//通过可变引用来改变 `mut_point` 的字段 `y`
*mref_y = 100;
```

15.4 [生命周期](https://rustwiki.org/zh-CN/rust-by-example/scope/lifetime.html#生命周期)

**已冻结**的数据无法通过原始对象来修改，直到对这些数据的所有引用离开作用域为止

生命周期说白了就是作用域的名字。每一个**引用**以及包含引用的数据结构，都要有一个生命周期来指定它保持有效的作用域。只要该引用在出借者（lender）被销毁前结束，引用就是有效的

15.4.1 [显式标注](https://rustwiki.org/zh-CN/rust-by-example/scope/lifetime/explicit.html#显式标注)

**借用检查器**使用显式的生命周期标记来明确引用的有效时间应该持续多久

**函数**显式地标出生命周期（作用域名字）

`foo<'a>` 符号表示

表明 了 `foo` 的生命周期不能超出 `'a` 的周期

`foo<'a, 'b>`

`foo` 的生命周期不能超出 `'a` 和 `'b` 中任一个的周期

对于函数`fn print_refs(x: &i32, y: &i32) { println!(x, y); }`

使用显示生命周期改造函数，函数标明`x`和`y`有不同的生命周期 `'a` 和 `'b`

`fn print_refs<'a, 'b>(x: &'a i32, &'b i32) { ... }`

`print_refs` 接受两个 `i32` 的引用

这两个生命周期都必须至少要和 `print_refs` 函数一样长



不带参数的函数，也可以指定一个生命周期参数 `'a`

```rust
fn func<'a>() {
    let x = 100;
    let y: &'a i32 = &x;
}
```

`_x` 的生命周期比 `y` 的`'a`短。短生命周期不能强制转换成长生命周期



被借用的对象都必须比借用者生存得更长

```rust
print_refs(&four, &nine);
```

`four` 和 `nine` 的生命周期都必须比 `print_refs` 的长

15.4.2 [函数](https://rustwiki.org/zh-CN/rust-by-example/scope/lifetime/fn.html#函数)

带上生命周期的函数签名：

签名中任何引用**都必须**拥有标注好的生命周期

`fn print_one<'a>(x: &'a i32)`

声明了生命周期`'a`之后，签名参数中的所有引用都要拥有标注好的生命周期

任何被返回的引用**都必须**有和某个输入量相同的生命周期或是静态类型（`static`）

`fn invalid_output<'a>() -> &'a String { &String::from("foo") }`

 `&String::from("foo")` 将会创建一个 `String` 类型，然后对它取引用

数据在离开作用域时删掉，返回一个指向无效数据的引用

15.4.3 [方法](https://rustwiki.org/zh-CN/rust-by-example/scope/lifetime/methods.html#方法)标注生命周期，就像独立的函数一样

```rust
impl Owner {
    fn add_on<'a>(&'a mut self) { self.0 += 1; }
}
```

方法一般是不需要标明生命周期的，因为 `self` 的生命周期会赋给所有的输出 生命周期参数

15.4.4 [结构体](https://rustwiki.org/zh-CN/rust-by-example/scope/lifetime/struct.html#结构体)

`struct Borrowed<'a>(&'a i32);`

这里的引用必须比这个结构体长寿

15.4.5 [给 impl 标注生命周期](https://rustwiki.org/zh-CN/rust-by-example/scope/lifetime/trait.html#trait)

```rust
impl<'a> Default for Borrowed<'a> {
	fn default() -> Self {
        Self {x: &10,}
    }
}
```

14.6[约束](https://rustwiki.org/zh-CN/rust-by-example/scope/lifetime/lifetime_bounds.html#约束)

`T: 'a`：

在 `T` 中的**所有**引用都必须比生命周期 `'a` 活得更长

`T: Trait + 'a`：

`T` 类型必须实现 `Trait` trait，并且在 `T` 中的**所有**引用 都必须比 `'a` 活得更长。

```rust
fn print_ref<'a, T>(t: &'a T) where
	T: Debug + 'a {
        println!(t);
}
```

函数`print_ref`接受一个指向 `T` 的引用

在 `T` 中的所有*引用*都必须比 `'a'` 存活时间更长

`'a` 也要比函数活得更长

15.4.7 [强制转换](https://rustwiki.org/zh-CN/rust-by-example/scope/lifetime/lifetime_coercion.html#强制转换)

长的生命周期可以强制转成一个短的生命周期

```rust
fn choose_first<'a: 'b, 'b>(first: &'a i32, second: &'b i32) -> &'b i32 {
    frist
}
```

`<'a: 'b, 'b>` 读作生命周期 `'a` 至少和 `'b` 一样长

接受一个 `&'a i32` 类型并返回一个 `&'b i32` 类型

15.4.8 [static](https://rustwiki.org/zh-CN/rust-by-example/scope/lifetime/static_lifetime.html#static)

`'static` 生命周期在整个程序运行的时期中存在

数据保存在可执行文件的只读内存区

两种方式使变量拥有 `'static` 生命周期

使用 `static` 声明来产生常量（constant）

`static NUM: i32 = 18;`

产生一个拥有 `&'static str` 类型的 `string` 字面量

`let static_string = "I'm in read-only memory";`

数据存在于二进制文件里面

15.4.9 [省略](https://rustwiki.org/zh-CN/rust-by-example/scope/lifetime/elision.html#省略)

隐式添加生命周期的过程称为省略（elision）

借用检查器将会隐式地添加生命周期以减少程序输入量、增强可读性

函数体内不需要显式给生命周期名字，因为在本地上下文里，没有必要关注生命周期。Rust 知道程序的全部信息，它会自动引入临时作用域

如果跨出函数的边界，就需要关心生命周期了。下面是去掉语法糖后的生命周期：

```rust
let x = 0;
let y = &x;
let z = &y;
```

包含生命周期（作用域的名字）的真实代码：

```rust
'a: {
    let x: i32 = 0;
    'b: {
        let y: &'b i32 = &'b x;
        'c: {
            let z: &'c &'b i32 = &'c y;
        }
    }
}
```

将引用传递到作用域以外会导致生命周期扩大

```rust
'a: { let x: i32 = 0;
	'b: { let z: &'b i32;
        'c: { let y: &'b i32 = &'b x;
            	z = y;
        }}}
```

必须使用`'b`，因为引用`&x`被传递到了`'b`的作用域

# [特性 trait](https://rustwiki.org/zh-CN/rust-by-example/trait.html#特性-trait)

`trait` 是对未知类型 `Self` 定义的方法集

任何数据类型都可以实现 `trait`

1. 首先，我们定义包含一系列方法的 `Animal trait`，即 `Animal` 方法集

    ```rust
    trait Animal {
        // 静态方法声明
        fn new(name: &'static str) -> Self;
        // 实例方法声明
        fn name(&self) -> &'static str;
        // 默认方法定义
        fn talk(&self) {
            println!(self.name(), self.noise())
        }
    }
    ```

2. 然后，针对 `Sheep` **数据类型**实现 `Animal` `trait` 方法集

    ```rust
    struct Sheep {naked: bool, name: &'static str}
    
    impl Aninmal for Sheep {
        fn new(name: &'static str) -> Sheep {
            Sheep { name, naked: false}
        }
    }
    ```

3. `Sheep` 的实例可以使用 `Animal` 中的所有方法

    ```rust
    let mut dolly: Sheep = Animal::new("Dolly");
    dolly.talk();
    ```

## [推导](https://rustwiki.org/zh-CN/rust-by-example/trait/derive.html#推导)

通过 `#[derive]` [属性](https://rustwiki.org/zh-CN/rust-by-example/attribute.html)，编译器能够推导某些 `trait` 的基本实现

可以自动推导的 trait：

- 比较 trait: [`Eq`](https://doc.rust-lang.org/std/cmp/trait.Eq.html), [`PartialEq`](https://doc.rust-lang.org/std/cmp/trait.PartialEq.html), [`Ord`](https://doc.rust-lang.org/std/cmp/trait.Ord.html), [`PartialOrd`](https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html)

    ```rust
    #[derive(PartialEq, PartialOrd)]
    struct Money(f64);
    ```

- [`Clone`](https://doc.rust-lang.org/std/clone/trait.Clone.html), 用来从 `&T` 创建副本 `T`。

- [`Copy`](https://doc.rust-lang.org/core/marker/trait.Copy.html)，使类型具有 “复制语义”（copy semantics）而非 “移动语义”（move semantics）。

- [`Hash`](https://doc.rust-lang.org/std/hash/trait.Hash.html)，从 `&T` 计算哈希值（hash）。

- [`Default`](https://doc.rust-lang.org/std/default/trait.Default.html), 创建数据类型的一个空实例。

- [`Debug`](https://doc.rust-lang.org/std/fmt/trait.Debug.html)，使用 `{:?} formatter` 来格式化一个值

    ```rust
    #[derive(Debug)]
    struct Inches(i32);
    ```

    ## [运算符重载](https://rustwiki.org/zh-CN/rust-by-example/trait/ops.html#运算符重载)

运算符可以通过 `trait` 来重载

运算符就是方法调用的语法糖

`a + b` 中的 `+` 运算符会调用 `add` 方法（也就是 `a.add(b)`）	

​	这个 `add` 方 法是 `Add` trait 的一部分

`std::ops::Add` trait 用来指明 `+` 的功能

要实现 `Foo + Bar = FooBar` 这样的运算，就需要对类型 `Foo` 实现 `Add` 方法

用于把 `Foo` 对象和 `Bar` 类型的右操作数加起来的 trait

```rust
impl ops::Add<Bar> for Foo {
    type Output = FooBar;
    fn add(self, _rhs: Bar) -> FooBar {
        println!("Foo + Bar");
        FooBar
    }
}
```

## [Drop](https://rustwiki.org/zh-CN/rust-by-example/trait/drop.html#drop)

当对象离开作用域时会自动调用 `Drop::drop` 方法

手动实现 `Drop trait` 的流程

```rust
struct MyType;
impl Drop for MyType {
    fn drop(&mut self) {}
}
```

## [Iterators](https://rustwiki.org/zh-CN/rust-by-example/trait/iter.html#iterators)

`Iterator` trait （迭代器方法集）用来对集合（collection）类型类实现迭代器

这个 `rait` 只需定义一个返回 `next`（下一个）元素的方法

`for` 结构会使用 [`.into_iterator()`](https://doc.rust-lang.org/std/iter/trait.IntoIterator.html) 方法将**集合**类型转换为**迭代器**

自定义迭代器流程

```rust
struct Fib {curr: u32, next: u32};
impl Iterator for Fib {
    type Item = u32;
    fn next(&mut self) -> Option<u32> {
        let new_next = self.curr + self.next;
        self.curr = self.next;
        self.next = new_next;
        
        Some(new_next)
    }
}
```

`Range` 由`..`标记产生，它生成步长为1的`iterator`

```rust
let mut seq = 0..3;
assert_eq!(seq.next(), Some(0))
```

`for` 遍历 `Iterator` 直到返回 `None`

并且每个 `Some` 值都被解包（unwrap），然后绑定给一个变量

```rust
for i in 0..3 {
    println!("{}", i);
}
// i = 0, 1, 2 不是 Some(0), Some(1), Some(2)
```

`take(n)` 方法提取 `Iterator` 的前 `n` 项

```rust
for i in fib().take(4) {...}
```

`skip(n)` 方法移除前 `n` 项

```rust
for i in fib().skip(4).take(4) { ... }
```

`iter` 方法对数组/slice 产生一个 `Iterator`

```rust
let array = [1u32, 2, 3, 4];
for i in array.iter() { ... }
```

## [Clone](https://rustwiki.org/zh-CN/rust-by-example/trait/clone.html#clone)

处理资源的默认行为是在赋值或函数调用的同时将它们的所有权转移

我们可以使用由 `Clone` trait 定义的 `.clone()` 方法把资源复制一份

实现了`Copy`的结构体使用复制行为

```rust
#[derive(Clone, Copy)]
struct Nil;

// 实例化 `Nil`，初始化 nil
let nil = Nil;
// 复制 `Nil`
let nil_copy = nil;
```

实现了 `Clone` 但没有实现 `Copy` 的类型，默认是`Move`转移行为，但可以手动指定用 `clone()` 方法复制

```struct
#[derive(Clone)]
let pair = Pair(Box::new(1), Box::new(2));
// 将 `pair` 绑定到 `moved_pair`，移动（move）了资源
let moved_pair = pair;
// 将 `moved_pair`（包括其资源）克隆到 `cloned_pair`
let cloned_pair = moved_pair.clone();
drop(moved_pair);
println!(cloned_pair);
```

# [使用 `macro_rules!` 来创建宏](https://rustwiki.org/zh-CN/rust-by-example/macros.html#使用-macro_rules-来创建宏)

宏并不产生函数调用，而是展开成源码，并和程序的其余部分一起被编译

Rust 的宏会展开为抽象语法树（AST，abstract syntax tree），而不是替换成字符串，这样就不会产生无法预料的优先权错误

宏通过 `macro_rules!` 宏来创建

```rust
macro_rules! hello_macro {
    () => {
        println!("Hello!");
    }
}

//这个调用将会展开成 `println("Hello");`!
hello_macro!();
```

## [指示符](https://rustwiki.org/zh-CN/rust-by-example/macros/designators.html#指示符)

宏的参数使用 `$` 作为前缀，`:` 注明参数类型

宏参数是 `ident` 类型的宏

```rust
macro_rules! create_func {
    ($func_name: ident) => (
    	fn $func_name() {
            println!(" ... {:?}()", 
                stringify!($func_name))
        }
    )
}
```

宏参数为 `func_name`， 参数类型是 `ident`，

说明宏接受一个 `ident` 指示符表示的参数，创建一个名为 `$func_name` 的函数

`ident` 指示符用于变量名或函数名类型

`stringify!` 宏把 `ident` 类型的参数转换成字符串

创建 `foo` 函数：`create_func!(foo)`

宏参数是 `expr` 类型的宏

```rust
macro_rules! print_result {
    ($expression: expr) => (
    	println!("{:?} = {:?}",
        	stringify!($expression),
        	$expression)
    )
}
```

接受一个 `expr` 类型的表达式

`stringify!` 把表达式*原样*转换成一个字符串

`$expression` 是表达式的结果

```bash
print_result!(1 + 1);
"1 + 1" = 2
```

全部指示符：

- `block`
- `expr` 用于表达式
- `ident` 用于变量名或函数名
- `item`
- `pat` (**模式** *pattern*)
- `path`
- `stmt` (**语句** *statement*)
- `tt` (**标记树** *token tree*)
- `ty` (**类型** *type*)

### [重载](https://rustwiki.org/zh-CN/rust-by-example/macros/overload.html#重载)

宏可以重载，从而接受不同的参数组合；类似 `scanf` 函数或者 `match` 匹配

```rust
macro_rules! test {
    ($left:expr; and $right:expr) => {
        println!("{:?} and {:?} = {:?}",
        	stringify!($left),
        	stringify!($right),
        	$left && $right)
    };
    ($left: expr; or $right: expr) => {
        println!("{:?} or {:?} = {:?}",
        	stringify!($left),
        	stringify!($right),
        	$left || $right)
    }
}
```

使用

```rust
test!(1+1 == 2; and 2*2 == 4);
test!(true; or flase)
"1 + 1 == 2" and "2 * 2 == 4" = true
"true" or "false" = true
```

### [重复](https://rustwiki.org/zh-CN/rust-by-example/macros/repeat.html#重复)

 `+` ：一个参数可能出现一次或多次

`*` ：参数可能出现零次或多次

 `$(...),+` 包围起来，可以匹配一个或多个用 `，` 隔开的表达式

宏定义的最后一个分支可以不用分号作为结束

求可变数量参数的最小值

```rust
macro_rules! find_min {
    ($x: expr) => {$x};
    ($x: expr, $($y: expr),+) => {
        std::cmp::min($x, find_min!$(($y)+))
    }
}
```

使用

```rust
find_min!(1);
find_min!(1+2, 2);
find_min!(5, 2*3, 4)
```

## [DRY (不写重复代码)](https://rustwiki.org/zh-CN/rust-by-example/macros/dry.html#dry-不写重复代码)

DRY: Don't Repeat Yourself 

示例，用一个宏实现并测试 `Vec<T>` 关于 `+=`、`*=` 和 `-=` 等运算符计算，`Vec<T>` 中的元素个数从 1 到 9。

测试方法

```rust
test!(add_assign, 1, 2, 3);
test!(mul_assign, 2, 3, 6);
test!(sub_assign, 3, 2, 1);
```

测试宏，要求 `+=`、`*=` 和 `-=` 等运算对 `Vec<T>` 的元素个数从 1 到 9 都测试一遍

```rust
macro_rules! test {
    ($func:ident, $x:expr, $y:expr, $z:expr) => {
        #[test]
        fn $func() {
            for sz in 0..10 {
                // 生成有sz个$x元素的 vec![$x, $x, ...]
                let mut x: Vec<_> = iter::repeat($x).take(sz).collection();
                let y: Vec<_> = iter::repeat($y).take(sz).collection();
                let z: Vec<_> = iter::repeat($z).take(sz).collection();
                
                // 用 op! 宏生成的函数
                super::$func(&mut x, &y); // x += y
                assert_eq!(x, z); // x == z, x+y == z 
            }
        }
    }
}
```

用一个宏 `op!` 实现 `add_assign`、`mul_assign` 和 `sub_assign` 等函数

```rust
op!(add_assign, Add, +=, add);
op!(mul_assign, Mul, *=, mul);
op!(sub_assign, Sub, -=, sub);
```

实现过程

```rust
macro_rules! op {
    ($func:ident, $bound:ident, $op:tt, $method:ident) => {
        fn $func<T>(xs: &mut Vec<T>, ys: &Vec<T>) where
        	T: $bound<T, Output=T> + Copy {
                assert_equal_len!(xs, ys, $func, $op);
                
                for (x, y) in xs.iter_mut().zip(ys.iter()) {
                    *x = $bound::$method(*x, *y);
                }
        }
    }
}
```

`assert_equal_len` 宏的实现

```rust
macro_rules! assert_equal_len {
    ($a:ident, $b:ident, $func:ident, $op:tt) => {
        assert!($a.len() == $b.len(),
        	"{:?}: 维度不匹配: {:?} {:?}, {:?}",
        	stringify!($func),
            ($a.len(),),
            stringify!($op),
            ($b.len()));
    }
}
```

`tt`（token tree，标记树）指示符表示运算符和标记

## [DSL（领域专用语言）](https://rustwiki.org/zh-CN/rust-by-example/macros/dsl.html#dsl领域专用语言)

DSL 是 Rust 的宏中集成的微型 “语言”，自己编语言

```rust
macro_rules! calculate {
    (eval $e: expr) => {
       	{
            let val: usize = $e;
            println!("{} = {}", stringify!($e), val);
        }
    }
}
calculate!(eval 1+2)
calculate!{
    eval (1+2) * (3/5)
}
```

## [可变参数接口](https://rustwiki.org/zh-CN/rust-by-example/macros/variadics.html#可变参数接口)

通过 `$(...),+`实现可变参数数目

```rust
macro_rules! calculate {
    (eval $e:expr) => {
        println!("{}", $e);
    };
    (eval $e:expr, $(eval $es:expr),+) => {
        calculate!(eval $e);
        calculate!($(eval $es),+);
    }
}
```

# [错误处理](https://rustwiki.org/zh-CN/rust-by-example/error.html#错误处理)

错误处理（error handling）是**处理**可能发生的**失败**情况的过程。如果在失败基础上继续运行，会造成问题。

显式的 `panic` 主要用于测试，以及处理不可恢复的错误

`Option` 类型是为了值是可选的、或者缺少值并不是错误的情况

- 寻找 父目录时，`/` 和 `C:` 这样的目录就没有父目录，这应当并不是一个错误
- 在测试或者原型开发中
    - `unwrap` 可用于原型开发，也可以用于能够确定 `Option` 中一定有值 的情形
    - `expect` 更有用，因为它允许你指定一条错误信息，以免万一还是出现 了错误
    - `unwrap` 然后使用 `expect`

当错误有可能发生，且应当由调用者处理时，使用 `Result`

## [`panic`](https://rustwiki.org/zh-CN/rust-by-example/error/panic.html#panic)

最简单的错误处理机制

- 打印一个错误消息
- 回退（unwind）任务
- 显式地在错误条件下调用 `panic`
- 当公主收到蛇这件不合适的礼物时，我们就让程序 `panic`

```bash
thread 'main' panicked at 'aaaaaaa!!', src/main.rs:14:5
```

## [`Option` 和 `unwrap`](https://rustwiki.org/zh-CN/rust-by-example/error/option_unwrap.html#option-和-unwrap)

如果公主期待收到礼物，却没收到

使用标准库中 `Option<T>`（ “选项/存在”）的枚举类型，它表现为：

- `Some(T)`：存在一个属于 `T` 类型的元素
- `None`：不存在相应元素

通过 `match` 显式地处理，或使用 `unwrap` 隐式地处理

隐式处理要么 返回 `Some` 内部的元素，要么就 `panic`。

平民（commoner）处理 `gift`

```rust
//gift: Option<&str>
match gift {
    Some("snake") => println!("snake"),
    Some(inner) => println!("{}", inner),
    None => printlin!("None"),
}
```

公主处理 `gift`

- 没收到礼物就会 `panic`（恐慌），用 `unwrap`

```rust
// `unwrap` 在接收到 `None` 时将返回 `panic`
let inside = gift.unwrap();
```

- 见到蛇就会 `panic`（恐慌）

    ```rust
    if inside == "snake" {panic!("AAAaaa!!!");}
    ```

    ### [组合算子：`map`](https://rustwiki.org/zh-CN/rust-by-example/error/option_unwrap/map.html#组合算子map)

    [组合算子](https://doc.rust-lang.org/book/glossary.html#combinators)（combinator），以 模块化的风格来管理控制流

    `Option` 有一个内置方法 `map()`，用于简单映射

    - `Some -> Some`
    - `None -> None`

`map()` 以链式调用的方式来简化 `match` 语句

削皮。如果没有食物，就返回 `None`。否则返回削好皮的食物

```rust
fn 削皮(food: Option<Food>) -> Option<已去皮> {
    match food {
        Some(food) => Some(已去皮(food)),
        None => None,
    }
}
```

削皮切块烹饪一条龙，用 `Option::map`

```rust
fn process(food: Option<Food>) -> Option<Cooked> {
    food.map(|f| Peeled(f)) //削皮
    	.map(|Peeled(f)| Chopped(f)) //切块
    	.map(|Chopped(f)| Cooked(f)) //烹饪
}
```

当 `f = None` 时， `Option::map(None) -> None`，`None`就会一直传下去

### [组合算子：`and_then`](https://rustwiki.org/zh-CN/rust-by-example/error/option_unwrap/and_then.html#组合算子and_then)

如果以返回类型是 `Option<T>` 的函数作为 `map()` 的参数，会导致出现嵌套形式 `Option<Option<T>>`

比如上一节的示例

```rust
Some(food).map(|f| Peeled(f)).map(|f| Chopped(f))
```

就会变成 `Option<Option<Chopped>>`

引入 `and_then()` 来处理这种情况，在某些语言中它叫做 `flatmap`

`and_then()` 使用被 `Option` 包裹的值来调用其**输入函数**并返回结果

- `Some(t).and_then(f) -> Option::and_then(f(t))`
- None -> None

通过原材料和食谱做出一道菜

```rust
fn cookable(food: Food) -> Option<Food> {
    have_ingredients(food).and_then(have_recipe)
}
```

## [结果 `Result`](https://rustwiki.org/zh-CN/rust-by-example/error/result.html#结果-result)

[`Result`](https://doc.rust-lang.org/std/result/enum.Result.html) 是 [`Option`](https://doc.rust-lang.org/std/option/enum.Option.html) 类型的更丰富的版本，描述的是可能的**错误**而不是可能的**存在**

`Result<T，E>` 可以有两个结果

- `Ok<T>`：找到 `T` 元素
- `Err<E>`：找到 `E` 元素，`E` 即表示错误的类型

 `parse()` 返回一个 `Result` 表示可能的失败

`Result::unwrap()`

- `Ok(t) ->` 举出元素 `t`
-  `Err("ParseIntError { kind: InvalidDigit }") -> panic(e)`

### [`Result` 的 `map`](https://rustwiki.org/zh-CN/rust-by-example/error/result/result_map.html#result-的-map)

一般地把错误返回给调用者，以决定回应错误的正确方式

- `Result::and_then(self, f)`，`and_then`把`self`中的`t`取出来了
    - `Ok(t) => f(t)`
    - `Err(e) => Err(e)`
- `Result::map(self, f)`，`map`把`self`中的`t`取出来了
    - `Ok(t) => Ok(f(t))`
    - `Err(e) => Err(e)`

```rust
use std::num::ParseIntError;

fn multiply(num1: &str, num2: &str) -> Result<i32, ParseIntError> {
    num1.parse::<i32>().and_then(|num1| {
        num2.parse::<i32>().map(|num2| num1 * num2)
    })
}
```

### [给 `Result` 取别名](https://rustwiki.org/zh-CN/rust-by-example/error/result/result_alias.html#给-result-取别名)

同一模块中的错误常常会有相同的 `Err` 类 型

单个别名就能简便地定义**所有**相关的 `Result`

