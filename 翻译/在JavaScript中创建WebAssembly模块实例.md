> ---
>
> title: 在JavaScript中创建WebAssembly模块实例
>
> date: 2018-3-22 23:58:00
>
> categories: WebAssembly, 翻译
>
> tags: WebAssembly
>
> source: [原文地址](https://hacks.mozilla.org/2017/07/creating-a-webassembly-module-instance-with-javascript/)
>
> auther: [Lin Clark](http://code-cartoons.com/)
>
> ---

# 在JavaScript中创建WebAssembly模块实例

---

这是此系列的第一篇文章：

- Creating a WebAssembly module instance with JavaScript
- [Memory in WebAssembly (and why it’s safer than you think)](https://hacks.mozilla.org/2017/07/memory-in-webassembly-and-why-its-safer-than-you-think/)
- [WebAssembly table imports… what are they?](https://hacks.mozilla.org/2017/07/webassembly-table-imports-what-are-they/)

---

WebAssembly是在Web上[运行代码的新方法](https://hacks.mozilla.org/2017/02/a-cartoon-intro-to-webassembly/)。有了它，你可以用 C/C++ & Rust 等语言编写模块，并在浏览器中运行它们。

但是，目前模块无法独立运行。随着浏览器对ES模块化的支持，预计会发生变化。一旦到位，WebAssembly模块将或许可以能够与其他ES模块使用相同的方式加载，例如，使用```<script type="module">```。

但是到目前为止，要在浏览器中使用WebAssembly还是需要使用JavaScript包装的。这将会创建一个模块的实例，之后你的JavaScript就能够调用在WebAssembly模块中定义的方法。

举个栗子，我们可以看看如何在React中实例化一个WebAssembly模块。
(您可以在[此视频](https://www.youtube.com/watch%3Fv%3D3GHJ4cbxsVQ)中了解更多关于React如何应用WebAssembly的相关内容。)

当用户加载页面时，它会以相同的方式启动。

浏览器会先下载JS文件，此外.wasm文件也会被Fetch，这个.wasm文件中包含了WebAssembly的二进制代码

![](https://hacks.mozilla.org/files/2017/07/1-1-768x614.png)

我们需要加载文件中的代码才能运行它。
首先是.js文件，该文件承载了React的JavaScript部分。然后，JavaScript将创建一个WebAssembly模块的实例————调解器。

为此，JS将调用`WebAssembly.instantiate`。

![](https://hacks.mozilla.org/files/2017/07/1-2-768x367.png)

让我们深入一点探究到底发生了什么。

我们传入`WebAssembly.instantiate`的第一个参数将是我们在.wasm文件中获得的二进制代码。这就是模块代码。

所以我们将二进制文件提取到缓冲区中，然后作为参数传入

![](https://hacks.mozilla.org/files/2017/07/1-03-768x236.png)

这使主线程可以回到其他工作。主线程知道，一旦编译器编译完这个模块，它就会通过promise回调来通知。Promise的resolve会携带解析好的模块胜利归来。

但经过编译的模块并不是创建实例唯一所需的东西。我认为这个模块就像一本说明书。

这个实例就像是一个试图用指令书做点什么的人。为了做到这一点，他们也需要原材料。他们需要能够与之配合的东西。

![](https://hacks.mozilla.org/files/2017/07/1-05-768x461.png)

这就是WebAssembly.instantiate的第二个参数出场的地方————imports对象。

![](https://hacks.mozilla.org/files/2017/07/1-06-768x145.png)

我将这些进口物品看作是一箱原材料，就像你从宜家那里得到的那样。实例使用这些原材料和这些导入的资源，再 按照指令说明构建一个东西。正如说明手册所预期的某些原材料一样，每个模块都需要一组特定的导入资源。

![](https://hacks.mozilla.org/files/2017/07/1-07-768x457.png)

所以，当你实例化一个模块时，你需要传递一个imports对象和附加在imports上的其他东西。每个导入都可以是以下四种imports中的一种：

- values
- function closures
- memory
- tables

** *Values* **
它可以是原始全局变量的值。 WebAssembly现在支持的唯一类型是整数和浮点数，所以值必须是这两种类型之一。这将随着WebAssembly规范中添加更多类型而改变。

** *Function Closures* **
它也可以有函数闭包。这意味着您可以传递JavaScript函数，然后WebAssembly可以调用该函数及闭包中的变量。

这会特别有用！因为目前WebAssembly不能直接调用DOM方法。直接访问DOM在WebAssembly路线图上，但并没有成为规范的一部分。

同时你可以做的是传递一个JavaScript函数，它可以包含你所需要的DOM操作。然后WebAssembly可以调用该JS函数。

** *Memory* **
另一种导入资源是内存对象。该对象使WebAssembly代码可以模拟手动内存管理。内存对象的概念容易让人产生困惑，所以我在[下一篇文章中进行了深入探讨](https://hacks.mozilla.org/2017/07/webassembly-table-imports-what-are-they/)

** *Tables* **
最后的类型与安全有关。它被称为`Tables`。它使你可以使用被称为函数指针的东西。Again，这有点复杂，所以我在[本系列的第三部分](https://hacks.mozilla.org/2017/07/webassembly-table-imports-what-are-they/)中解释它。

下面就是你可以装载进你的实例的不同种类的imports资源

![](https://hacks.mozilla.org/files/2017/07/1-08.png)

当 `WebAssembly.instantiate` 完成了解析，promise状态转换为resolved，promise回调就会进入microtask queue，它携带两件东西：**实例**和**独立的编译完成的模块**。

返回已编译的模块的好处是可以快速创建同一模块的其他实例。你所需要做的只是将模块作为源参数传入。模块本身没有任何状态(状态在实例上)。这意味着实例可以共享已编译的模块代码。

你的实例现在已经装备齐全并准备好了。它是已编译的代码, 有它的指令手册，以及它所有的mports资源。你现在可以调用它的方法。

在接下来的两篇文章中，我们将深入研究[内存导入](https://hacks.mozilla.org/2017/07/memory-in-webassembly-and-why-its-safer-than-you-think/)和[表格导入](https://hacks.mozilla.org/2017/07/webassembly-table-imports-what-are-they/)。