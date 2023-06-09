# 常见编程概念

我最近在重新学rust ，巩固一下细节，也写一个Chaineye rust 教程，供想要学习的小伙伴查看。

推特：@seek_web3

Chainey 社群： 官网 chaineye.info | Chaineye Rust 教程 | 微信: LGZAXE, 加微信之后拉各社群

所有代码和教程开源在github: https://github.com/0xchaineye/chaineye-rust

-----------------------------------------------------------------------------------------------------------------------------------------------------------


本章涵盖了几乎所有编程语言中出现的概念以及它们在 Rust 中的工作方式。许多编程语言的核心有很多共同点。本章介绍的所有概念都不是 Rust 独有的，但我们将在 Rust 的上下文中讨论它们并解释使用这些概念的约定。

具体来说，您将了解变量、基本类型、函数、注释和控制流。这些基础将存在于每个 Rust 程序中，尽早学习它们将为您提供强大的核心。

```
关键词
Rust 语言有一组关键字，这些关键字仅供该语言使用，就像在其他语言中一样。请记住，您不能将这些词用作变量或函数的名称。大多数关键字都有特殊含义，您将在 Rust 程序中使用它们来完成各种任务；一些当前没有与之关联的功能，但已保留用于将来可能添加到 Rust 的功能。您可以在附录 A中找到关键字列表。
```

## 一. 变量和可变性

### 1. 不可改变变量和可变变量

如“使用变量存储值”部分所述，默认情况下变量是不可变的。这是 Rust 为您提供的以利用 Rust 提供的安全性和简单并发性的方式编写代码的众多推动之一。但是，您仍然可以选择使变量可变。让我们探讨一下 Rust 如何以及为什么鼓励您支持不变性，以及为什么有时您可能想要选择退出。

当一个变量是不可变的，一旦一个值被绑定到一个名字上，你就不能改变那个值。 为了说明这一点，让我们在项目目录中生成一个名为variables的新项目，使用.cargo new variables

然后，在您的新变量目录中，打开src/main.rs并将其代码替换为以下代码。这段代码还不能编译，我们将首先检查不变性错误。

文件名：src/main.rs

```
fn main() {
    let x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

使用 保存并运行程序cargo run。您应该会收到一条错误消息，如以下输出所示：

```
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         -
  |         |
  |         first assignment to `x`
  |         help: consider making this binding mutable: `mut x`
3 |     println!("The value of x is: {}", x);
4 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable

For more information about this error, try `rustc --explain E0384`.
error: could not compile `variables` due to previous error
```

这个例子展示了编译器如何帮助你发现程序中的错误。编译器错误可能令人沮丧，但实际上它们仅意味着您的程序还没有安全地执行您希望它执行的操作；他们并不意味着你不是一个好的程序员！有经验的 Rustaceans 仍然会遇到编译器错误。

错误消息表明错误的原因是您cannot assign twice to immutable variable `x`，因为您尝试为不可变x变量分配第二个值。

当我们尝试更改指定为不可变的值时，我们会遇到编译时错误，这一点很重要，因为这种情况可能会导致错误。如果我们的代码的一部分假设一个值永远不会改变，而我们的另一部分代码改变了这个值，那么代码的第一部分可能不会按照它的设计来做。这种错误的原因在事后很难追查，尤其是当第二段代码只是偶尔改变值时。Rust 编译器保证当你声明一个值不会改变时，它真的不会改变，所以你不必自己跟踪它。因此，您的代码更容易推理。

但是可变性可能非常有用，并且可以使代码更方便编写。变量仅在默认情况下是不可变的；正如您在第 2 章中所做的那样，您可以通过mut在变量名前面添加来使它们可变。添加mut还通过指示代码的其他部分将更改此变量的值来向代码的未来读者传达意图。

例如，让我们将src/main.rs更改为以下内容：

```
fn main() {
    let mut x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

当我们现在运行程序时，我们得到：

```
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30s
     Running `target/debug/variables`
The value of x is: 5
The value of x is: 6
```

我们可以将绑定到xfrom的值更改5为6whenmut 使用的值。除了防止错误之外，还有多种权衡需要考虑。例如，在您使用大型数据结构的情况下，就地修改实例可能比复制和返回新分配的实例更快。对于较小的数据结构，创建新实例并以更具功能性的编程风格编写可能更容易思考，因此较低的性能可能是为了获得这种清晰度而付出的代价。

### 2. 常数

与不可变变量一样，常量是绑定到名称且不允许更改的值，但常量和变量之间存在一些差异。

首先，不允许使用mut常量。常量不仅在默认情况下是不可变的——它们总是不可变的。const您使用关键字而不是关键字声明常量 ，并且必须 注释let值的类型。我们将在下一节“数据类型”中介绍类型和类型注释，所以现在不要担心细节。只知道您必须始终注释类型。

常量可以在任何范围内声明，包括全局范围，这使得它们对于代码的许多部分需要了解的值很有用。

最后一个区别是常量只能设置为常量表达式，而不是只能在运行时计算的值的结果。

下面是一个常量声明的例子：

```
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```

常量的名称是THREE_HOURS_IN_SECONDS，其值设置为 60（一分钟的秒数）乘以 60（一小时的分钟数）乘以 3（我们要在此程序中计算的小时数）的结果. Rust 的常量命名约定是在单词之间使用全大写和下划线。编译器能够在编译时评估一组有限的操作，这让我们可以选择以更容易理解和验证的方式写出这个值，而不是将此常量设置为值 10,800。有关在声明常量时可以使用哪些操作的更多信息，请参阅Rust 参考中关于常量评估的部分。

常量在程序运行的整个过程中都有效，在它们声明的范围内。此属性使常量对于程序的多个部分可能需要了解的应用程序域中的值很有用，例如任何点的最大数量游戏的玩家被允许赚取或光速。

将整个程序中使用的硬编码值命名为常量有助于将该值的含义传达给代码的未来维护者。如果将来需要更新硬编码的值，在代码中只需要更改一个位置也很有帮助。


### 3. 重影

重影的概念与其他面向对象语言里的"重写"（Override）或"重载"（Overload）是不一样的。重影就是刚才讲述的所谓"重新绑定"，之所以加引号就是为了在没有介绍这个概念的时候代替一下概念。



正如您在第 2 章的猜谜游戏教程中看到的，您可以声明一个与先前变量同名的新变量。Rustaceans 说第一个变量被第二个变量遮蔽，这意味着第二个变量的值是程序在使用该变量时看到的值。我们可以通过使用相同变量的名称并重复使用let关键字来隐藏变量，如下所示：

```
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {}", x);
    }

    println!("The value of x is: {}", x);
}
```

该程序首先绑定x到 的值5。然后它x通过重复进行阴影let x =化，获取原始值并添加1，因此 的值 x就是 then 6。然后，在内部范围内，第三条let语句也隐藏x了 ，将前一个值乘以2得到x的值12。当该范围结束时，内部阴影结束并x恢复为存在6。当我们运行这个程序时，它会输出以下内容：

```
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/variables`
The value of x in the inner scope is: 12
The value of x is: 6
```

重影与将变量标记为 不同mut，因为如果我们不小心尝试重新分配给这个变量而不使用let关键字，我们会得到一个编译时错误。通过使用let，我们可以对一个值执行一些转换，但在这些转换完成后变量是不可变的。

和重影之间的另一个区别mut是，因为我们在let再次使用关键字时有效地创建了一个新变量，所以我们可以更改值的类型但重用相同的名称。例如，假设我们的程序要求用户通过输入空格字符来显示他们想要在某些文本之间有多少空格，然后我们想将该输入存储为一个数字：

```
let spaces = "   ";
let spaces = spaces.len();
```

第一个spaces变量是字符串类型，第二个spaces变量是数字类型。因此，阴影使我们不必想出不同的名称，例如spaces_strand spaces_num; 相反，我们可以重用更简单的spaces名称。但是，如果我们尝试使用mut它，如此处所示，我们将得到一个编译时错误：

此代码无法编译！
```
    let mut spaces = "   ";
    spaces = spaces.len();
```
该错误表明我们不允许改变变量的类型：

```
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
error[E0308]: mismatched types
 --> src/main.rs:3:14
  |
3 |     spaces = spaces.len();
  |              ^^^^^^^^^^^^ expected `&str`, found `usize`

For more information about this error, try `rustc --explain E0308`.
error: could not compile `variables` due to previous error
```

现在我们已经探索了变量是如何工作的，让我们看看它们可以拥有的更多数据类型。


## 二. 数据类型

Rust 中的每个值都是特定的数据类型，它告诉 Rust 指定了哪种数据，因此它知道如何处理这些数据。我们将研究两种数据类型子集：标量和复合。

请记住，Rust 是一种静态类型语言，这意味着它必须在编译时知道所有变量的类型。编译器通常可以根据值以及我们如何使用它来推断我们想要使用什么类型。在可能有多种类型的情况下，例如当我们使用第 2 章的“将猜测值与秘密数字比较”String部分中将 a 转换为数字类型时，我们必须添加类型注释，如下所示：parse

```
let guess: u32 = "42".parse().expect("Not a number!");
```

如果我们不在这里添加类型注释，Rust 将显示以下错误，这意味着编译器需要我们提供更多信息才能知道我们要使用哪种类型：

```
$ cargo build
   Compiling no_type_annotations v0.1.0 (file:///projects/no_type_annotations)
error[E0282]: type annotations needed
 --> src/main.rs:2:9
  |
2 |     let guess = "42".parse().expect("Not a number!");
  |         ^^^^^ consider giving `guess` a type

For more information about this error, try `rustc --explain E0282`.
error: could not compile `no_type_annotations` due to previous error
```
您将看到其他数据类型的不同类型注释。

### 1.标量类型

标量类型表示单个值。Rust 有四种主要的标量类型：整数、浮点数、布尔值和字符。您可以从其他编程语言中识别出这些。让我们来看看它们是如何在 Rust 中工作的。

#### 1.1.整数类型

整数是没有小数部分的数字。我们在第 2 章中使用了一种整数类型，u32类型。此类型声明表明它关联的值应该是占用 32 位空间的无符号整数（有符号整数类型以 开头i，而不是）。u表 3-1 显示了 Rust 中的内置整数类型。我们可以使用这些变体中的任何一个来声明整数值的类型。

表 1：Rust 中的整数类型


| 长度 | 有符号 | 无符号 |
|:---------:|:---------::|:---------::|
| 8-bit | i8 |  u8 |
| 16-bit | i16 | u16|
| 32-bit | i32 |  u32|
| 64-bit |  i64  | u64 |
| 128-bit | i128 |  u128 |
| arch |  isize |  usize |
每个变体都可以是有符号或无符号的，并且具有明确的大小。 有符号和无符号是指数字是否可能为负数 - 换句话说，数字是否需要带有符号（有符号），或者它是否只会是正数，因此可以在没有符号的情况下表示（无符号）。这就像在纸上写数字：当符号很重要时，数字会以加号或减号显示；但是，当可以安全地假设该数字为正时，它会显示为没有符号。使用二进制补码表示存储有符号数 。

每个有符号变量可以存储从 -(2 n - 1 ) 到 2 n - 1 - 1 的数字，其中n是变量使用的位数。因此 an i8可以存储从 -(2 7 ) 到 2 7 - 1 的数字，等于 -128 到 127。无符号变体可以存储从 0 到 2 n - 1 的数字，因此 au8可以存储从 0 到 2 8 - 1 的数字，等于 0 到 255。

此外，isize和usize类型取决于运行程序的计算机的体系结构，在表中表示为“arch”：如果您使用的是 64 位架构，则为 64 位；如果您使用的是 32 位，则为32 位架构。

您可以按表 2 中所示的任何形式编写整数文字。请注意，可以是多个数字类型的数字文字允许使用类型后缀（例如57u8）来指定类型。数字文字也可以_用作视觉分隔符，使数字更易于阅读，例如1_000，它的值与您指定的 相同1000。

表 2：Rust 中的整数文字

|  数字文字 | 例子 |
|:---------:|:---------::|
|Decimal |98_222 |
|Hex | 0xff|
|Octal | 0o77|
|Binary  | 0b1111_0000|
|Byte  |(u8 only)	b'A'|

那么你怎么知道使用哪种类型的整数呢？如果您不确定，Rust 的默认值通常是不错的起点：整数类型默认为i32. 您将使用isize或usize索引某种集合时的主要情况。

```
整数溢出
假设您有一个类型的变量u8可以保存 0 到 255 之间的值。如果您尝试将变量更改为该范围之外的值，例如 256，则会发生整数溢出，这可能会导致两种行为之一。当你在调试模式下编译时，Rust 会检查整数溢出，如果发生这种行为会导致你的程序在运行时崩溃。当程序因错误退出时，Rust 使用术语恐慌；我们将在第 9 章的“不可恢复的错误 panic!”部分更深入地讨论恐慌。

当您使用--release标志在发布模式下编译时，Rust 不 包括对导致恐慌的整数溢出的检查。相反，如果发生溢出，Rust 会执行二进制补码包装。简而言之，大于该类型可以保持的最大值的值“环绕”到该类型可以保持的最小值。在 a 的情况下u8，值 256 变为 0，值 257 变为 1，依此类推。程序不会恐慌，但变量的值可能不是您期望的值。依赖整数溢出的包装行为被认为是错误的。

要显式处理溢出的可能性，您可以使用标准库为原始数字类型提供的这些方法系列：

使用方法包装在所有模式中wrapping_*，例如wrapping_add
None如果checked_*方法溢出，则返回值
overflowing_*返回值和一个布尔值，指示方法是否溢出
saturating_* 使用方法在值的最小值或最大值处饱和
```

#### 1.2 浮点类型
Rust 也有两种用于浮点数的原始类型，它们是带小数点的数字。Rust 的浮点类型是f32和f64，它们的大小分别为 32 位和 64 位。默认类型是f64 因为在现代 CPU 上它的速度大致相同，f32但精度更高。所有浮点类型都是有符号的。

这是一个显示实际浮点数的示例：

文件名：src/main.rs

```
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}
```

浮点数根据 IEEE-754 标准表示。该 f32类型是单精度浮点数，并且f64具有双精度。

数值运算
Rust 支持所有数字类型的基本数学运算：加法、减法、乘法、除法和余数。整数除法向下舍入到最接近的整数。以下代码显示了如何在let语句中使用每个数字运算：

文件名：src/main.rs

```
fn main() {
    // addition
    let sum = 5 + 10;

    // subtraction
    let difference = 95.5 - 4.3;

    // multiplication
    let product = 4 * 30;

    // division
    let quotient = 56.7 / 32.2;
    let floored = 2 / 3; // Results in 0

    // remainder
    let remainder = 43 % 5;
}
```
这些语句中的每个表达式都使用数学运算符并计算为单个值，然后将其绑定到一个变量。附录 B包含 Rust 提供的所有运算符的列表。

布尔类型
与大多数其他编程语言一样，Rust 中的布尔类型有两个可能的值：true和false. 布尔值大小为一个字节。Rust 中的布尔类型使用bool. 例如：

文件名：src/main.rs

```
fn main() {
    let t = true;

    let f: bool = false; // with explicit type annotation
}
```
使用布尔值的主要方法是通过条件，例如if 表达式。我们将在“控制流”部分介绍if表达式在 Rust 中是如何工作的。

字符类型
Rust 的char类型是该语言最原始的字母类型。下面是一些声明char值的例子：

文件名：src/main.rs

```
fn main() {
    let c = 'z';
    let z = 'ℤ';
    let heart_eyed_cat = '😻';
}
```
请注意，我们char使用单引号指定文字，而不是使用双引号的字符串文字。Rust 的char类型大小为 4 个字节，表示一个 Unicode 标量值，这意味着它可以表示的不仅仅是 ASCII。重音字母；中文、日文、韩文字符；表情符号；和零宽度空格都是charRust 中的有效值。Unicode 标量值的范围从U+0000到U+D7FF到U+E000包括U+10FFFF在内。但是，“字符”在 Unicode 中并不是真正的概念，因此您对“字符”的人类直觉可能与charRust 中的 a 不匹配。我们将在第 8 章的“使用字符串存储 UTF-8 编码文本”中详细讨论这个主题。

### 2.复合类型
复合类型可以将多个值组合为一种类型。Rust 有两种原始的复合类型：元组和数组。

#### 2.1. 元组类型

元组是将具有多种类型的多个值组合成一个复合类型的一般方法。元组具有固定长度：一旦声明，它们的大小就不能增长或缩小。

我们通过在括号内编写一个逗号分隔的值列表来创建一个元组。元组中的每个位置都有一个类型，元组中不同值的类型不必相同。我们在这个例子中添加了可选的类型注解：

文件名：src/main.rs

```
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```
该变量tup绑定到整个元组，因为元组被视为单个复合元素。要从元组中获取单个值，我们可以使用模式匹配来解构元组值，如下所示：

文件名：src/main.rs

```
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {}", y);
}
```
该程序首先创建一个元组并将其绑定到变量tup。然后它使用模式 withlet将tup其转换为三个单独的变量，x，y和z。这称为解构，因为它将单个元组分成三个部分。最后，程序打印 的值 y，即6.4。

我们还可以通过使用句点 ( .) 后跟我们要访问的值的索引来直接访问元组元素。例如：

文件名：src/main.rs

```
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```

该程序创建元组x，然后使用它们各自的索引为每个元素创建新变量。与大多数编程语言一样，元组中的第一个索引是 0。

没有任何值的元组(), 是一种只有一个值的特殊类型，也写成()。类型称为单位类型，值称为单位值。如果表达式不返回任何其他值，则表达式会隐式返回单位值。

数组类型
另一种收集多个值的方法是使用数组。与元组不同，数组的每个元素都必须具有相同的类型。与其他一些语言中的数组不同，Rust 中的数组具有固定长度。

我们将数组中的值写为方括号内的逗号分隔列表：

文件名：src/main.rs

```
fn main() {
    let a = [1, 2, 3, 4, 5];
}
```

当您希望将数据分配在堆栈而不是堆上（我们将在第 4 章中详细讨论堆栈和堆）或希望确保始终拥有固定数量的元素时，数组很有用。不过，数组不像向量类型那么灵活。向量是标准库提供的类似集合类型，允许大小增加或缩小。如果您不确定是使用数组还是向量，您可能应该使用向量。第 8 章更详细地讨论了向量。

但是，当您知道不需要更改元素的数量时，数组会更有用。例如，如果您在程序中使用月份的名称，您可能会使用数组而不是向量，因为您知道它将始终包含 12 个元素：


```
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
```

您使用方括号编写数组的类型，其中包含每个元素的类型、分号，然后是数组中的元素数，如下所示：

```
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

这里，i32是每个元素的类型。分号后的数字5 表示数组包含五个元素。

您还可以通过指定初始值、后跟分号和方括号中的数组长度来初始化数组以包​​含每个元素的相同值，如下所示：

```
let a = [3; 5];
```

命名的数组a将包含最初5全部设置为该值的元素 。3这与写作相同，let a = [3, 3, 3, 3, 3];但以更简洁的方式。

访问数组元素
数组是可以在堆栈上分配的已知固定大小的单个内存块。您可以使用索引访问数组的元素，如下所示：

文件名：src/main.rs

```
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
```

在此示例中，名为的变量first将获得 value 1，因为这是[0]数组中索引处的值。命名的变量将从数组中的索引中second获取值。2[1]

无效的数组元素访问
让我们看看如果您尝试访问超出数组末尾的数组元素会发生什么。假设你运行这段代码，类似于第 2 章中的猜谜游戏，从用户那里获取数组索引：

文件名：src/main.rs

此代码恐慌！

```
use std::io;

fn main() {
    let a = [1, 2, 3, 4, 5];

    println!("Please enter an array index.");

    let mut index = String::new();

    io::stdin()
        .read_line(&mut index)
        .expect("Failed to read line");

    let index: usize = index
        .trim()
        .parse()
        .expect("Index entered was not a number");

    let element = a[index];

    println!(
        "The value of the element at index {} is: {}",
        index, element
    );
}
```
此代码编译成功。如果您使用cargo run并输入 0、1、2、3 或 4 运行此代码，程序将打印出数组中该索引处的相应值。如果您在数组末尾输入一个数字，例如 10，您将看到如下输出：

```
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is 10', src/main.rs:19:19
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

该程序在索引操作中使用无效值时导致运行时错误。程序退出并显示错误消息并且没有执行最后的println!语句。当您尝试使用索引访问元素时，Rust 将检查您指定的索引是否小于数组长度。如果索引大于或等于长度，Rust 会恐慌。这种检查必须在运行时进行，尤其是在这种情况下，因为编译器不可能知道用户稍后运行代码时将输入什么值。

这是 Rust 内存安全原则的一个示例。在许多低级语言中，并没有进行这种检查，并且当您提供不正确的索引时，可以访问无效内存。Rust 通过立即退出而不是允许内存访问和继续来保护您免受此类错误的影响。第 9 章讨论了更多 Rust 的错误处理。


## 三. 函数

函数在 Rust 代码中很普遍。您已经看到了该语言中最重要的函数之一：main函数，它是许多程序的入口点。您还看到了fn允许您声明新函数的关键字。

Rust 代码使用蛇形大小写作为函数和变量名称的常规样式，其中所有字母都是小写并用下划线分隔单词。这是一个包含示例函数定义的程序：

文件名：src/main.rs

```
fn main() {
    println!("Hello, world!");

    another_function();
}

fn another_function() {
    println!("Another function.");
}
```

我们在 Rust 中定义一个函数，输入fn后跟一个函数名和一组括号。花括号告诉编译器函数体在哪里开始和结束。

我们可以通过输入其名称和一组括号来调用我们定义的任何函数。因为another_function是在程序中定义的，所以可以从main函数内部调用。注意我们在源代码中定义了函数another_function 之后；main我们之前也可以定义它。Rust 不关心你在哪里定义你的函数，只关心它们是在某个地方定义的。

让我们开始一个名为functions的新二进制项目来进一步探索函数。将another_function示例放在src/main.rs中并运行它。您应该看到以下输出：

```
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 0.28s
     Running `target/debug/functions`
Hello, world!
Another function.
```
这些行按照它们在main函数中出现的顺序执行。首先，“你好，世界！” 消息打印，然后another_function被调用并打印其消息。

### 1. 函数参数

我们可以将函数定义为具有参数，它们是作为函数签名一部分的特殊变量。当一个函数有参数时，你可以为这些参数提供具体的值。从技术上讲，具体值称为arguments，但在随意的谈话中，人们倾向于交替使用参数和参数这两个词来表示函数定义中的变量或调用函数时传入的具体值。

在这个版本中，another_function我们添加了一个参数：

文件名：src/main.rs

```
fn main() {
    another_function(5);
}

fn another_function(x: i32) {
    println!("The value of x is: {}", x);
}
```

尝试运行这个程序；你应该得到以下输出：

```
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 1.21s
     Running `target/debug/functions`
The value of x is: 5
```

的声明another_function有一个名为 的参数x。的类型 x指定为i32。当我们传入5toanother_function时， println!宏会将5大括号对放在格式字符串中的位置。

在函数签名中，您必须声明每个参数的类型。这是 Rust 设计中的一个深思熟虑的决定：在函数定义中要求类型注释意味着编译器几乎不需要你在代码的其他地方使用它们来确定你的意思是什么类型。

定义多个参数时，用逗号分隔参数声明，如下所示：

文件名：src/main.rs

```
fn main() {
    print_labeled_measurement(5, 'h');
}

fn print_labeled_measurement(value: i32, unit_label: char) {
    println!("The measurement is: {}{}", value, unit_label);
}
```

此示例创建一个以print_labeled_measurement两个参数命名的函数。第一个参数被命名value并且是一个i32. 第二个被命名unit_label并且是 type char。然后该函数打印包含value和 的文本unit_label。

让我们尝试运行这段代码。将您的函数 项目的src/main.rs文件中当前的程序替换为前面的示例，并使用以下命令运行它cargo run：

```
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/functions`
The measurement is: 5h
```

因为我们将函数5称为value和'h'的值unit_label，所以程序输出包含这些值。

### 2.语句和表达式

函数体由一系列可选地以表达式结尾的语句组成。到目前为止，我们介绍的函数还没有包含结束表达式，但是您已经看到了表达式作为语句的一部分。因为 Rust 是一种基于表达式的语言，所以这是一个需要理解的重要区别。其他语言没有相同的区别，所以让我们看看什么是语句和表达式，以及它们的区别如何影响函数体。

语句是执行某些操作且不返回值的指令。表达式计算为结果值。让我们看一些例子。

我们实际上已经使用过语句和表达式。创建一个变量并使用let关键字为其赋值是一个语句。在清单 3-1 中， let y = 6;是一个语句。

文件名：src/main.rs

```
fn main() {
    let y = 6;
}
```
示例 3-1：main包含一条语句的函数声明

函数定义也是语句；前面的整个示例本身就是一个语句。

语句不返回值。因此，您不能将let语句分配给另一个变量，就像下面的代码尝试做的那样；你会得到一个错误：

文件名：src/main.rs
```
此代码无法编译！
fn main() {
    let x = (let y = 6);
}
```
当你运行这个程序时，你会得到如下错误：

```
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
error: expected expression, found statement (`let`)
 --> src/main.rs:2:14
  |
2 |     let x = (let y = 6);
  |              ^^^^^^^^^
  |
  = note: variable declaration using `let` is a statement

error[E0658]: `let` expressions in this position are experimental
 --> src/main.rs:2:14
  |
2 |     let x = (let y = 6);
  |              ^^^^^^^^^
  |
  = note: see issue #53667 <https://github.com/rust-lang/rust/issues/53667> for more information
  = help: you can write `matches!(<expr>, <pattern>)` instead of `let <pattern> = <expr>`

warning: unnecessary parentheses around assigned value
 --> src/main.rs:2:13
  |
2 |     let x = (let y = 6);
  |             ^         ^
  |
  = note: `#[warn(unused_parens)]` on by default
help: remove these parentheses
  |
2 -     let x = (let y = 6);
2 +     let x = let y = 6;
  | 

For more information about this error, try `rustc --explain E0658`.
warning: `functions` (bin "functions") generated 1 warning
error: could not compile `functions` due to 2 previous errors; 1 warning emitted
```
该let y = 6语句不返回值，因此没有任何 x要绑定的内容。这与在其他语言中发生的情况不同，例如 C 和 Ruby，其中赋值返回赋值的值。在这些语言中，您可以编写x = y = 6并拥有两者x并y具有价值 6；在 Rust 中情况并非如此。

表达式评估为一个值，并构成了您将用 Rust 编写的大部分代码。考虑一个数学运算，例如5 + 6，它是一个计算值为 的表达式11。表达式可以是语句的一部分：在清单 3-1中，6语句中的let y = 6;是一个计算结果为 value 的表达式6。调用函数是一个表达式。调用宏是一个表达式。用大括号创建的新范围块是一个表达式，例如：

文件名：src/main.rs

```
fn main() {
    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {}", y);
}
```
这个表达式：

```
{
    let x = 3;
    x + 1
}
```
是一个块，在这种情况下，计算结果为4. 该值被绑定为语句y 的一部分。let请注意，该x + 1行的末尾没有分号，这与您目前看到的大多数行不同。表达式不包括结束分号。如果在表达式的末尾添加分号，则将其转换为语句，然后它将不返回值。在接下来探索函数返回值和表达式时，请记住这一点。

### 3.有返回值的函数

函数可以将值返回给调用它们的代码。我们不命名返回值，但我们必须在箭头 ( ->) 之后声明它们的类型。在 Rust 中，函数的返回值与函数体块中的最终表达式的值同义。您可以通过使用return关键字并指定一个值从函数中提前返回，但大多数函数会隐式返回最后一个表达式。下面是一个返回值的函数示例：

文件名：src/main.rs

```
fn five() -> i32 {
    5
}

fn main() {
    let x = five();

    println!("The value of x is: {}", x);
}
```
函数中没有函数调用、宏，甚至没有let语句five ——只有数字5本身。这是 Rust 中一个完全有效的函数。请注意，函数的返回类型也被指定为-> i32. 尝试运行此代码；输出应如下所示：

```
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30s
     Running `target/debug/functions`
The value of x is: 5
```
5in是函数的five返回值，这就是返回类型为 的原因i32。让我们更详细地研究一下。有两个重要的部分：首先，该行let x = five();显示我们正在使用函数的返回值来初始化变量。因为该函数five返回 a 5，所以该行与以下内容相同：



let x = 5;
其次，该five函数没有参数并且定义了返回值的类型，但是函数的主体是一个5没有分号的孤独的，因为它是一个我们想要返回其值的表达式。

让我们看另一个例子：

文件名：src/main.rs

```
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1
}
```
运行此代码将打印The value of x is: 6. 但是，如果我们在包含 的行的末尾放置一个分号x + 1，将其从表达式更改为语句，我们会得到一个错误。

文件名：src/main.rs

此代码无法编译！
```
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}
```

fn plus_one(x: i32) -> i32 {
x + 1;
}
编译此代码会产生错误，如下所示：

```
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
error[E0308]: mismatched types
 --> src/main.rs:7:24
  |
7 | fn plus_one(x: i32) -> i32 {
  |    --------            ^^^ expected `i32`, found `()`
  |    |
  |    implicitly returns `()` as its body has no tail or `return` expression
8 |     x + 1;
  |          - help: consider removing this semicolon

For more information about this error, try `rustc --explain E0308`.
error: could not compile `functions` due to previous error
```
主要错误消息“类型不匹配”揭示了此代码的核心问题。该函数的定义plus_one表明它将返回一个 i32，但语句不会计算出一个值，该值由()单位类型 表示。因此，没有返回任何内容，这与函数定义相矛盾并导致错误。在这个输出中，Rust 提供了一条消息，可能有助于纠正这个问题：它建议删除分号，这将修复错误。

## 四. 注释

所有程序员都努力使他们的代码易于理解，但有时需要额外的解释。在这些情况下，程序员在他们的源代码中留下编译器会忽略的注释，但阅读源代码的人可能会觉得有用。

这是一个简单的评论：

```
// hello, world
```
在 Rust 中，惯用的注释样式以两个斜杠开始注释，并且注释一直持续到行尾。对于超出单行的注释，您需要//在每一行中包含，如下所示：

```
// So we’re doing something complicated here, long enough that we need
// multiple lines of comments to do it! Whew! Hopefully, this comment will
// explain what’s going on.
```

注释也可以放在包含代码的行的末尾：

文件名：src/main.rs

```
fn main() {
    let lucky_number = 7; // I’m feeling lucky today
}
```

但是你会更经常看到它们以这种格式使用，注释在它所注释的代码上方的单独一行中：

文件名：src/main.rs

```
fn main() {
    // I’m feeling lucky today
    let lucky_number = 7;
}
```

Rust 还有另一种注释，文档注释，我们将在第 14 章的“将 Crate 发布到 Crates.io”部分中讨论。

## 五. 流程控制

根据条件是否为真运行某些代码或在条件为真时重复运行某些代码的能力是大多数编程语言的基本构建块。让您控制 Rust 代码执行流程的最常见结构是if表达式和​​循环。

### 1. if表达式

if表达式允许您根据条件分支代码。您提供一个条件，然后声明：“如果满足此条件，请运行此代码块。如果不满足条件，请不要运行此代码块。”

在您的项目目录中创建一个名为分支的新项目以探索表达式。在src/main.rs文件中，输入以下内容：if

文件名：src/main.rs

```
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```

所有if表达式都以关键字开头if，后跟一个条件。在这种情况下，条件检查变量number的值是否小于 5。如果条件为真，我们将代码块放在大括号内的条件之后立即执行。与表达式中的条件相关的代码块if有时称为arm，就像我们在第 2 章的“将猜测值与秘密数字比较”match一节中讨论的表达式中的 arm 一样。

可选地，我们还可以包含一个else表达式，我们在这里选择这样做，以便在条件评估为 false 时为程序提供一个替代代码块来执行。如果您不提供else表达式并且条件为假，则程序将跳过该if块并继续执行下一段代码。

尝试运行此代码；您应该看到以下输出：

```
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/branches`
condition was true
```
让我们尝试将 的值更改为number产生条件的值， false看看会发生什么：

```
    let number = 7;
```

再次运行程序，查看输出：

```
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/branches`
condition was false
```

还值得注意的是，此代码中的条件必须是bool. 如果条件不是 a bool，我们会得到一个错误。例如，尝试运行以下代码：

文件名：src/main.rs

```
此代码无法编译！
fn main() {
    let number = 3;

    if number {
        println!("number was three");
    }
}
```

if条件计算为这个时间的值，3Rust 抛出一个错误：

```
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
error[E0308]: mismatched types
 --> src/main.rs:4:8
  |
4 |     if number {
  |        ^^^^^^ expected `bool`, found integer

For more information about this error, try `rustc --explain E0308`.
error: could not compile `branches` due to previous error
```

该错误表明 Rust 期望 abool但得到了一个整数。与 Ruby 和 JavaScript 等语言不同，Rust 不会自动尝试将非布尔类型转换为布尔类型。您必须明确并始终提供 if布尔值作为其条件。如果我们希望if代码块仅在数字不等于 时运行0，例如，我们可以将if 表达式更改为以下内容：

文件名：src/main.rs

```
fn main() {
    let number = 3;

    if number != 0 {
        println!("number was something other than zero");
    }
}
```

运行此代码将打印number was something other than zero.

### 2. 处理多个条件else if
您可以通过在 表达式中组合if和来使用多个条件。例如：elseelse if

文件名：src/main.rs

```
fn main() {
    let number = 6;

    if number % 4 == 0 {
        println!("number is divisible by 4");
    } else if number % 3 == 0 {
        println!("number is divisible by 3");
    } else if number % 2 == 0 {
        println!("number is divisible by 2");
    } else {
        println!("number is not divisible by 4, 3, or 2");
    }
}
```
该程序有四种可能的路径。运行后，您应该会看到以下输出：

```
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/branches`
number is divisible by 3
```

当这个程序执行时，它依次检查每个if表达式并执行第一个条件成立的主体。请注意，即使 6 可以被 2 整除，我们也看不到 output number is divisible by 2，也看不到块中的number is not divisible by 4, 3, or 2文本else。这是因为 Rust 只执行第一个真正条件的块，一旦找到一个，它甚至不检查其余的。

使用过多的else if表达式会使您的代码变得混乱，因此如果您有多个表达式，您可能需要重构您的代码。match第 6 章描述了针对这些情况调用的强大的 Rust 分支结构。

### 3. if在let语句中使用
因为if是一个表达式，我们可以在语句的右侧使用它let 来将结果分配给一个变量，如清单 3-2 所示。

文件名：src/main.rs

```
fn main() {
    let condition = true;
    let number = if condition { 5 } else { 6 };

    println!("The value of number is: {}", number);
}
```
示例 3-2：将if表达式的结果分配给变量

该number变量将根据if 表达式的结果绑定到一个值。运行这段代码看看会发生什么：

```
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30s
     Running `target/debug/branches`
The value of number is: 5
```
请记住，代码块的计算结果是其中的最后一个表达式，而数字本身也是表达式。在这种情况下，整个if表达式的值取决于执行的代码块。这意味着可能来自每个分支的结果的值if必须是相同的类型；在示例 3-2 中，ifarm 和else arm 的结果都是i32整数。如果类型不匹配，如下例所示，我们将收到错误消息：

文件名：src/main.rs

此代码无法编译！
```
fn main() {
    let condition = true;
    let number = if condition { 5 } else { "six" };
    println!("The value of number is: {}", number);
}
```
当我们尝试编译这段代码时，我们会得到一个错误。if和arm 具有不兼容的else值类型，Rust 准确地指出了在程序中哪里可以找到问题：

```
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
error[E0308]: `if` and `else` have incompatible types
 --> src/main.rs:4:44
  |
4 |     let number = if condition { 5 } else { "six" };
  |                                 -          ^^^^^ expected integer, found `&str`
  |                                 |
  |                                 expected because of this

For more information about this error, try `rustc --explain E0308`.
error: could not compile `branches` due to previous error
```

块中的表达式if计算为整数，else块中的表达式计算为字符串。这是行不通的，因为变量必须具有单一类型，并且 Rust 需要在编译时明确地知道 number变量是什么类型。知道 的类型number可以让编译器在我们使用的任何地方验证该类型是否有效number。number如果仅在运行时确定类型，Rust 将无法做到这一点；如果编译器必须跟踪任何变量的多个假设类型，编译器会更复杂，并且对代码的保证更少。

### 4. 循环重复
多次执行一段代码通常很有用。对于这个任务，Rust 提供了几个循环，它们将遍历循环体内部的代码直到结束，然后立即回到开头。为了试验循环，让我们创建一个名为loops的新项目。

Rust 有三种循环：loop、while和for。让我们尝试每一个。

#### 4.1. 重复代码loop
loop关键字告诉 Rust 永远一遍又一遍地执行一段代码，或者直到你明确告诉它停止。

例如，将循环目录中的src/main.rs文件更改为如下所示：

文件名：src/main.rs

```
fn main() {
    loop {
        println!("again!");
    }
}
```
当我们运行这个程序时，我们会看到again!不断地打印，直到我们手动停止程序。大多数终端支持键盘快捷键 ctrl-c来中断陷入连续循环的程序。试试看：

```
$ cargo run
   Compiling loops v0.1.0 (file:///projects/loops)
    Finished dev [unoptimized + debuginfo] target(s) in 0.29s
     Running `target/debug/loops`
again!
again!
again!
again!
^Cagain!
```
该符号^C代表您按下ctrl-c 的 位置。您可能会或可能不会看到在again!之后打印的单词^C，具体取决于代码在收到中断信号时在循环中的位置。

幸运的是，Rust 还提供了一种使用代码跳出循环的方法。您可以break在循环中放置关键字来告诉程序何时停止执行循环。回想一下，我们在第 2 章的 “猜对后退出”部分的猜谜游戏中这样做了，以在用户通过猜对数字赢得游戏时退出程序。

我们还在continue猜谜游戏中使用过，它在循环中告诉程序跳过此循环迭代中的任何剩余代码并进入下一个迭代。

如果循环中有循环，break则continue在该点应用于最内层循环。您可以选择在循环上指定一个循环标签，然后我们可以使用它break或continue指定这些关键字应用于标记的循环而不是最内层的循环。这是一个包含两个嵌套循环的示例：

```
fn main() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {}", count);
        let mut remaining = 10;

        loop {
            println!("remaining = {}", remaining);
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {}", count);
}
```
外部循环有标签'counting_up，它会从 0 计数到 2。没有标签的内部循环从 10 倒数到 9。第一个break没有指定标签的将只退出内部循环。该break 'counting_up;语句将退出外循环。此代码打印：

```
$ cargo run
   Compiling loops v0.1.0 (file:///projects/loops)
    Finished dev [unoptimized + debuginfo] target(s) in 0.58s
     Running `target/debug/loops`
count = 0
remaining = 10
remaining = 9
count = 1
remaining = 10
remaining = 9
count = 2
remaining = 10
End count = 2
```

#### 4.2. 从循环返回值

a 的用途之一loop是重试您知道可能会失败的操作，例如检查线程是否已完成其工作。您可能还需要将该操作的结果从循环中传递到代码的其余部分。为此，您可以在break用于停止循环的表达式之后添加您想要返回的值；该值将从循环中返回，以便您可以使用它，如下所示：

```
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {}", result);
}
```
在循环之前，我们声明一个名为的变量并将counter其初始化为 0. 然后我们声明一个名为的变量result来保存循环返回的值。在循环的每次迭代中，我们添加1变量counter，然后检查计数器是否等于10。如果是，我们使用 break带有 value 的关键字counter * 2。在循环之后，我们使用分号来结束为 赋值的语句result。最后，我们打印 中的值result，在本例中为 20。

### 5. 条件循环while

程序通常需要评估循环内的条件。当条件为真时，循环运行。当条件不再为真时，程序调用break，停止循环。loop使用, if,else和break;的组合来实现这样的行为是可能的。如果你愿意，你现在可以在一个程序中尝试。然而，这种模式非常普遍，以至于 Rust 有一个内置的语言结构，称为while循环。在示例 3-3 中，我们使用while循环程序 3 次，每次倒计时，然后在循环后打印一条消息并退出。

文件名：src/main.rs

```
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{}!", number);

        number -= 1;
    }

    println!("LIFTOFF!!!");
}
```
示例 3-3：while在条件成立时使用循环运行代码

loop如果您使用、if、else和，此构造消除了许多必要的嵌套 break，并且更清晰。当条件成立时，代码运行；否则，它退出循环。

### 6. 循环通过一个集合for
您可以选择使用该while构造来遍历集合的元素，例如数组。例如，清单 3-4 中的循环打印数组中的每个元素a。

文件名：src/main.rs

```
fn main() {
    let a = [10, 20, 30, 40, 50];
    let mut index = 0;

    while index < 5 {
        println!("the value is: {}", a[index]);

        index += 1;
    }
}
```
示例 3-4：使用循环遍历集合的每个while元素

在这里，代码通过数组中的元素进行计数。它从 index 开始 0，然后循环直到到达数组中的最终索引（即 whenindex < 5不再为真）。运行此代码将打印数组中的每个元素：

```
$ cargo run
   Compiling loops v0.1.0 (file:///projects/loops)
    Finished dev [unoptimized + debuginfo] target(s) in 0.32s
     Running `target/debug/loops`
the value is: 10
the value is: 20
the value is: 30
the value is: 40
the value is: 50
```
正如预期的那样，所有五个数组值都出现在终端中。即使index 会在某个点达到 的值5，循环也会在尝试从数组中获取第六个值之前停止执行。

但是，这种方法容易出错；如果索引值或测试条件不正确，我们可能会导致程序崩溃。例如，如果您将a数组的定义更改为有四个元素，但忘记将条件更新为while index < 4，则代码会出现恐慌。它也很慢，因为编译器添加了运行时代码以在循环的每次迭代中执行条件检查索引是否在数组的范围内。

作为更简洁的替代方案，您可以使用for循环并为集合中的每个项目执行一些代码。for循环类似于清单 3-5 中的代码。

文件名：src/main.rs

```
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {}", element);
    }
}
```

示例 3-5：使用循环遍历集合的每个for元素

当我们运行这段代码时，我们将看到与清单 3-4 相同的输出。更重要的是，我们现在提高了代码的安全性，并消除了可能由于超出数组末尾或不够远而丢失某些项目而导致的错误的可能性。

使用for循环，如果您更改了数组中值的数量，您不需要记住更改任何其他代码，就像使用清单 3-4 中使用的方法一样。

循环的安全性和简洁性for使它们成为 Rust 中最常用的循环结构。即使在您想要运行某些代码一定次数的情况下，如while清单 3-3 中使用循环的倒计时示例，大多数 Rustaceans 也会使用for循环。做到这一点的方法是使用Range标准库提供的 a ，它按顺序生成从一个数字开始到另一个数字之前结束的所有数字。

for这是使用循环和我们尚未讨论的另一种方法rev来反转范围的倒计时的样子：

文件名：src/main.rs

```
fn main() {
    for number in (1..4).rev() {
        println!("{}!", number);
    }
    println!("LIFTOFF!!!");
}
```

这段代码更好一些，不是吗？

## 六. 总结

你做到了！那是相当大的一章：您学习了变量、标量和复合数据类型、函数、注释、if表达式和循环！要练习本章讨论的概念，请尝试构建程序来执行以下操作：

在华氏温度和摄氏度之间转换温度。
生成第 n 个斐波那契数。
利用歌曲中的重复，打印圣诞颂歌“圣诞节的十二天”的歌词。
当你准备好继续前进时，我们将讨论 Rust 中一个在其他编程语言中不 常见的概念：所有权。
