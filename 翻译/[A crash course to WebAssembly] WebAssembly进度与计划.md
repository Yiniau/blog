> ---
>
> title: [A crash course in WebAssembly] 为什么WebAssembly这么快
>
> date: 2018-3-24  10:26:00
>
> categories: 翻译
>
> tags: WebAssembly
>
> source: [原文地址](https://hacks.mozilla.org/2017/02/where-is-webassembly-now-and-whats-next/)
>
> auther: [Lin Clark](http://code-cartoons.com/)
>
> ---

# [A carsh course to WebAssembly] WebAssembly 进度与计划

---

*这是WebAssembly系列文章的第六部分。如果您还没有阅读其他文章，我们建议您[从头开始](https://hacks.mozilla.org/2017/02/a-cartoon-intro-to-webassembly/)。*

2月28日，四大主流浏览器宣布他们一致认为WebAssembly的MVP已经完成。这提供了浏览器可以开始发布的稳定的初始版本。

![](https://hacks.mozilla.org/files/2017/02/logo_party01-768x259.png)

这为浏览器提供了一个稳定的核心。此核心不包含社区组正在计划的所有功能，但它确实提供了足够的功能来确保WebAssembly快速且可用。

借此，开发人员可以开始发布WebAssembly代码。对于较早版本的浏览器，开发人员可以发送一个asm.js版本的代码。因为asm.js是JavaScript的子集，所以任何JS引擎都可以运行它。通过Emscripten，你可以将相同的应用程序编译为WebAssembly和asm.js。
> 或者学习Rust，通过 LLVM toolchain 编译到 .wasm 文件。

即使是较早的版本，WebAssembly依旧能体现性能优势。通过修复和添加新功能，未来性能会进一步提升。

## 提升 WebAssembly 在浏览器中的性能

随着浏览器在其引擎中改进对WebAssembly的支持，性能将逐渐提升。浏览器供应正在各自独立处理这些问题。

### JS 与 WebAssembly之间更快的调用

目前，在JS代码中调用WebAssembly函数比预期的慢。那是因为它必须做一些叫做“trampolining”的事情。
JIT不知道如何直接处理WebAssembly，所以它必须将WebAssembly发送可以处理它的东西。这是段引擎本身的慢速代码，它被设置为运行优化的WebAssembly代码。

![](https://hacks.mozilla.org/files/2017/02/06-02-trampoline01-768x613.png)

如果JIT知道如何直接处理它，这将会快100倍！！。

你或许不会在传递一个大型任务的时候察觉到差别，但如果有许多的JS - WebAssembly之间的互相操作，性能差异就会格外明显(就像传递一大堆小任务的时候，每个任务都会经历这个过程)。

### 更快的加载

JIT必须在加载时间和执行时间之间作出权衡。如果您提前花更多时间进行编译和优化，这会就会加快执行速度，同时减慢启动速度。

有很多正在进行的工作来平衡预编译(确保代码开始运行后不会出现跳动)并且事实上，大部分代码不会有进行优化编译所需的足够多的运行时间。

由于WebAssembly不需要推测类型，因此引擎不必在运行时监视类型。这给了他们更多选择，例如将编译工作和执行并行化。

另外，最近增加的JavaScript API将允许流式编译WebAssembly。这意味着引擎可以在一边下载一边编译。

在Firefox中，我们正在研究一个双编译器系统。
其中一个编译器会提前运行，并且擅长优化代码。另一个编译器会在运行代码时在后台执行完全优化。代码的完全优化版将在其准备就绪时与正在运行的代码切换。

## 为规范添加post-MVP功能

WebAssembly的目标之一是在指定的一个小块中进行测试，而不是事先设计好所有的东西。

这意味着有很多预期的功能，但还没有全部经过完备的思考。它们将不得不经历所有浏览器供应商都积极参与的规范流程。

这些功能称为未来功能。这里列出了一小部分

### Working directly with the DOM

目前还没有办法与DOM直接进行交互。这意味着你不能像`element.innerHTML`那样通过WebAssembly更新一个节点。

作为代替，你必须通过JS来设定值。这可能意味着将值传递回JavaScript调用方。另一方面，它可能意味着在WebAssembly中调用JavaScript函数 ———— JavaScript和WebAssembly函数都可以作为导入的资源在WebAssembly模块中使用。

![](https://hacks.mozilla.org/files/2017/02/06-03-dom01-768x642.png)

无论哪种方式，通过JavaScript访问的速度可能比直接访问慢。某些使用WebAssembly的应用程序可能会被妨碍，直到问题被解决。

### 共享内存并发 Shared memory concurrency

加速代码运行的一种方法是使代码的不同部分能够并行运行。然而，这有时会适得其反，因为线程之间的通信开销可能比任务花费更多的时间。

但是如果可以在线程之间共享内存，这种开销在一定程度上会减少。为此，WebAssembly将使用JavaScript的新的`SharedArrayBuffer`。一旦在浏览器就绪，工作组就可以开始规范WebAssembly如何与它们配合使用。

### SIMD

如果你阅读过其他文章或参与过关于WebAssembly的讨论，您可能会听说过SIMD。这个缩写代表**单指令，多数据**。这是另一种并行运行的方式。

SIMD使得获取庞大的数据结构成为可能，如不同数字的向量，并将同一条指令同时应用于不同的部分。通过这种方式，它可以极大地加速游戏或VR所需的复杂计算。

这对普通的Web应用程序开发人员来说并不重要。但对于游戏开发人员这样使用多媒体的开发人员来说，这非常重要。

### 异常处理

像 C++ 这样的语言的许多代码库都使用异常。但是，异常尚未被指定为WebAssembly的一部分。

如果您使用Emscripten编译代码，它将模拟某些编译器优化级别的异常处理。但是，这很慢，所以你可能想使用DISABLE_EXCEPTION_CATCHING标志来关闭它。

如果异常在原生WebAssembly中得到处理，这种仿真将不是必需的。

### 其他改进 - 为开发人员提供服务

一些未来的功能在不影响性能的前提下，能够使开发人员更轻松地使用WebAssembly。

- First-class source-level developer tools. - 一流的源代码级开发人员工具 - 目前，在浏览器中调试WebAssembly就像调试原始汇编。只有一丢丢开发人员可以在心中将其源代码映射到汇编(人形编译器吗？？)。我们正在研究如何改进工具支持，以便开发人员可以调试他们的源代码。

- Garbage collection - 垃圾回收 - 如果你可以提前定义类型，就应该能够将你的代码转换为WebAssembly。所以使用类似TypeScript的代码应该也可以编译为WebAssembly。但是，目前唯一的困难是WebAssembly不知道如何与现有的垃圾收集器进行交互，比如内置于JS引擎的垃圾收集器。这个未来功能的想法是通过一系列底层GC的原始类型和操作为WebAssembly提供对内置GC的顶级访问。

- ES6 module integration - ES6模块整合 - 浏览器目前正在使用脚本标签(<script></script>)添加对加载JavaScript模块的支持。添加此功能后，即使url指向WebAssembly模块，标签(如<script src = url type = "module">)依旧能够正常工作。

## 总结

WebAssembly如今速度很快，而且随着新功能的实现和浏览器实现方面的改进，未来会变得更快。