> ---
>
> title: [A crash course in WebAssembly] 创作并使用 WebAssembly 模块
>
> date: 2018-3-22 16:42:00
>
> categories: 翻译
>
> tags: WebAssembly
>
> source: [原文地址](https://hacks.mozilla.org/2017/02/creating-and-working-with-webassembly-modules/)
>
> auther: [Lin Clark](http://code-cartoons.com/)
>
>
> ---

# [A crash course in WebAssembly] 创作并使用 WebAssembly 模块

---

*这是WebAssembly系列文章的第四部分。如果您还没有阅读其他文章，我们建议您[从头开始](https://hacks.mozilla.org/2017/02/a-cartoon-intro-to-webassembly/)。*

WebAssembly是一种在网页上运行JavaScript以外的编程语言的方法。在过去，当您想在浏览器中运行代码以与网页的不同部分进行交互时，您唯一的选择就是JavaScript。

所以当人们谈论WebAssembly变得越来越快时，比较的对象就是JavaScript。但是，这并不意味着它是一种或者两种情况————您使用的是WebAssembly，或者您正在使用JavaScript。

事实上，我们预计开发人员将在同一个应用程序中使用WebAssembly和JavaScript。即使你自己不写WebAssembly，你也可以利用它。

WebAssembly定义的模块函数可以让JavaScript调用，未来有一天你可以像从npm上下载loadsh之类的各种模块一样下载WebAssembly模块并调用它们的api。

so，让我们来看看如何创建一个WebAssembly模块然后在JS中使用。

## Where does WebAssembly fits

在上一篇讲assembly的文章中，我们谈到了编译器接受讲高级编程语言并将其翻译为机器码

![](https://hacks.mozilla.org/files/2017/02/04-01-langs09-768x469.png)

那么WebAssembly因该出现在这张图的什么位置呢？

你可能会觉得只是增加了一个目标汇编语言(与x86/ARM类似)。除了不针对某个架构外，基本上是对的。

当您通过Web在用户计算机上传递代码时，您不知道代码将在哪个目标架构上运行。

所以WebAssembly与其他类型的assembly有点不同。它是概念机器的机器语言，而不是实际的物理机器。

因此，WebAssembly指令有时被称为虚拟指令。它们比JavaScript代码更直接地映射到机器码。它们代表了可以在普通流行硬件中高效完成的一种交集。但它们不是直接映射到特定硬件的特定机器码。

![](https://hacks.mozilla.org/files/2017/02/04-02-langs08-768x501.png)

浏览器下载WebAssembly。然后，它能够快速将WebAssembly翻译为目标机器的汇编代码。

## Compiling to .wasm

目前对WebAssembly支持最多的编译器工具链称为LLVM。有许多不同的前端和后端可以插入到LLVM中。

> **note**：大多数WebAssembly模块开发人员将使用C和Rust等语言进行编码，然后编译为WebAssembly，但还有其他方法可以创建WebAssembly模块。例如，有一个实验工具可以帮助您使用TypeScript构建WebAssembly模块，或者您可以直接编写WebAssembly的文本表示形式。

假设我们想从C到WebAssembly。我们可以使用`clang`前端从C到LLVM中间表示。一旦它在LLVM的IR中，LLVM就会理解它，所以LLVM可以执行一些优化。

要从LLVM的IR（中间表示）转到WebAssembly，我们需要一个后端。目前LLVM项目正在进行中。这个后端重要的部分，会尽快完善。目前来说使用WebAssembly可能会非常棘手。

还有另一个名为Emscripten的工具，目前使用起来更容易一些。它有自己的后端，可以通过编译到另一个目标（称为asm.js），然后将其转换为WebAssembly来生成WebAssembly。不过，它使用LLVM，因此你可以在Emscripten的两个后端之间切换。

!()[https://hacks.mozilla.org/files/2017/02/04-03-toolchain07-768x631.png]

Emscripten包含许多额外的工具和库，允许移植整个 C/C++ 代码库，因此它比编译器更像是软件开发工具包（SDK）。例如，系统开发人员习惯于拥有可以读取和写入的文件系统，而Emscripten使用IndexedDB模拟文件系统。

无论您使用的工具链如何，最终结果都是以.wasm结尾的文件。我将在下面详细解释.wasm文件的结构。首先，让我们看看如何在JS中使用它。

## Loading a .wasm module in JavaScript

.wasm文件就是WebAssembly模块，可以使用JavaScript加载。截至目前，加载过程有点复杂。

```javascript
function fetchAndInstantiate(url, importObject) {
  return fetch(url).then(response =>
    response.arrayBuffer()
  ).then(bytes =>
    WebAssembly.instantiate(bytes, importObject)
  ).then(results =>
    results.instance
  );
}
```

您可以在我们的[文档](https://developer.mozilla.org/en-US/docs/WebAssembly)中更深入地看到这一点。

我们正在努力使这个过程更容易。我们希望对工具链进行改进，并将其与现有的模块捆绑器（打包器如Webpack或装载器如SystemJS）集成。我们相信加载WebAssembly模块可以像加载JavaScript模块一样简单。

不过，WebAssembly模块和JS模块之间有一个主要区别。目前，WebAssembly中的函数只能使用数字（整数或浮点数）作为参数或返回值。

![参数说明](https://hacks.mozilla.org/files/2017/02/04-04-memory04-768x143.png)

对于任何更复杂的数据类型（比如字符串），您必须使用WebAssembly模块的内存。

如果你主要使用JavaScript，对直接访问内存不是很熟悉。C/C++和Rust等性能更高的语言往往具有手动内存管理功能。 WebAssembly模块的内存模拟了您在这些语言的堆(heap)。

为此，它使用JavaScript中称为ArrayBuffer的东西。数组缓冲区是一个字节数组。数组的索引则作为内存地址。

如果你想在JavaScript和WebAssembly之间传递一个字符串，你可以将这些字符转换为它们的字符代码。然后你就可以将它写入数组缓冲区(memory array)。由于索引是整数，所以可以将索引传递给WebAssembly函数。因此，字符串的第一个字符的索引可以用作指针。

![](https://hacks.mozilla.org/files/2017/02/04-05-memory12-768x614.png)

任何开发WebAssembly模块以供Web开发人员使用的人都可能会为该模块创建一个包装。作为模块的使用者，您就能够不需要了解内存管理。

如果您想了解更多信息，请查看关于[使用WebAssembly内存的文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/WebAssembly/Memory)。

## .wasm 文件的结构

如果您使用更高级别的语言编写代码，然后将其编译为WebAssembly，则无需知道WebAssembly模块的结构。但它可以帮助你理解基础。

如果你还没有准备好，我们建议阅读[关于装配的文章](https://hacks.mozilla.org/2017/02/a-crash-course-in-assembly/)（本系列的第3部分）。

这是一个我们将翻译为WebAssembly的C函数：

```c
int add42(int num) {
  return num + 42;
}
```

您可以尝试使用WASM资源管理器来编译此功能。

如果你打开.wasm文件（如果你的编辑器支持显示它），你会看到类似的东西。

```webassembly
00 61 73 6D 0D 00 00 00 01 86 80 80 80 00 01 60
01 7F 01 7F 03 82 80 80 80 00 01 00 04 84 80 80
80 00 01 70 00 00 05 83 80 80 80 00 01 00 01 06
81 80 80 80 00 00 07 96 80 80 80 00 02 06 6D 65
6D 6F 72 79 02 00 09 5F 5A 35 61 64 64 34 32 69
00 00 0A 8D 80 80 80 00 01 87 80 80 80 00 00 20
00 41 2A 6A 0B
```

这是“二进制(binary)”表示的模块。我将引号放在二进制文件中，因为它通常以十六进制表示法显示，但可以很容易地转换为二进制符号或可读格式。

例如，这里是`num + 42`的样子:

![形态对比图](https://hacks.mozilla.org/files/2017/02/04-06-hex_binary_asm01-768x391.png)

## 代码如何工作：一个堆栈机器 a stack machine

以防万一您想知道，那么这些会说明它们做了什么

![代码注解](https://hacks.mozilla.org/files/2017/02/04-07-hex_binary_asm02-768x269.png)

您可能已经注意到，`add`操作没有说明其值应来自哪里。这是因为WebAssembly就像一个堆栈机器。这意味着在执行操作之前，操作所需的所有值都在堆栈中排队。

像`add`操作知道他们需要多少个值。由于add需要两个，因此它会从堆栈的顶部取两个值。这意味着add指令可以很短（一个字节），因为指令不需要指定源寄存器或目标寄存器。这会减小.wasm文件的大小，从而减少它的下载时间。

尽管WebAssembly是根据堆栈计算机指定的，但这并不是它在物理机器上的工作方式。当浏览器将WebAssembly转换为正在运行浏览器的机器的机器码时，它将使用寄存器。由于WebAssembly代码不指定寄存器，因此可以使浏览器更灵活地为该机器分配寄存器，达到最佳的分配方案。

## 模块部分

除了`add42`函数本身，.wasm文件中还有其他部分。这些被称为sections。某些sections对于任何模块都是必需的，有些是可选的。

必须的：

- **类型Type**
  包含此模块中定义的函数的函数签名和任何导入的函数。

- **函数Function**
  为此模块中定义的每个函数提供索引。

- **代码Code**
  此模块中每个函数的实际函数体。

可选的：

- **Export**
  使函数，内存，表和全局变量对其他WebAssembly模块和JavaScript可用。这允许单独编译的模块动态链接在一起。这是Web组件版的.dll。

- **Import**
  指定要从其他WebAssembly模块或JavaScript导入的函数，内存，表(tables)和全局变量。

- **Start**
  加载WebAssembly模块时会自动运行的函数（基本上类似于main函数​​）。

- **Global**
  为模块声明全局变量。

- **Memory**
  定义此模块将使用的内存。

- **Table**
  使映射到WebAssembly模块之外的值成为可能，例如JavaScript对象。
  这对允许间接函数调用特别有用。

- **Data**
  初始化导入的或本地的内存。

- **Element**
  初始化导入的或本地的表。

有关章节的更多信息，请参阅[sections的工作原理](https://rsms.me/wasm-intro)。

## coming up next

现在您已经知道如何使用WebAssembly模块，让我们来看看[为什么WebAssembly速度很快](https://hacks.mozilla.org/2017/02/what-makes-webassembly-fast/)。
