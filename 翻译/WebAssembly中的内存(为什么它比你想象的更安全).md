> ---
>
> title: WebAssembly中的内存(为什么它比你想象的更安全)
>
> date: 2018-3-22 23:58:00
>
> categories: 翻译
>
> tags: WebAssembly
>
> source: [原文地址](https://hacks.mozilla.org/2017/07/memory-in-webassembly-and-why-its-safer-than-you-think/)
>
> auther: [Lin Clark](http://code-cartoons.com/)
>
> ---

# WebAssembly中的内存(为什么它比你想象的更安全)

---

这是此系列的第二篇文章：

- [Creating a WebAssembly module instance with JavaScript](https://hacks.mozilla.org/2017/07/creating-a-webassembly-module-instance-with-javascript/)
- Memory in WebAssembly (and why it’s safer than you think)
- [WebAssembly table imports… what are they?](https://hacks.mozilla.org/2017/07/webassembly-table-imports-what-are-they/)

---

WebAssembly中的内存与JavaScript中的内存稍有不同。使用WebAssembly，您可以直接访问原始字节...并且会让一些人感到担忧。但它实际上比你想象的更安全。

## 什么是memory对象？

当一个WebAssembly模块被实例化时，它需要一个内存对象。你可以创建一个新的WebAssembly.Memory对象并将该对象传入。如果不存在，则会创建一个内存对象并将其自动附加到该实例。

所有JS引擎在内部都会创建一个ArrayBuffer（我在[另一篇文章](https://hacks.mozilla.org/2017/06/a-cartoon-intro-to-arraybuffers-and-sharedarraybuffers/)中解释过）。ArrayBuffer是一个对象，JS对其保持引用。JS为你分配内存。你告诉它需要多少内存，它会创建一个给定大小的ArrayBuffer。

![](https://hacks.mozilla.org/files/2017/07/02-01-768x590.png)

数组的索引可以看作是内存地址。如果需要更多的内存，你可以做一些叫做*growing(增长)*的事情来扩大数组。

将WebAssembly的内存作为JavaScript中的一个对象 - 作为ArrayBuffer处理 - 做到了两件事：

 1. 便于JS和WebAssembly之间的传值
 2. 使内存管理更加安全

## 在JS和WebAssembly之间传递值

因为这只是一个JavaScript对象，这意味着JavaScript也可以挖掘出这些内存中的字节。于此，WebAssembly和JavaScript可以通过共享内存并来回传递值。

他们不使用内存地址，而是使用数组索引来访问每个box(内存单元)。

例如，WebAssembly可以在内存中放置一个字符串。它会将它编码成字节......

![](https://hacks.mozilla.org/files/2017/07/02-02-768x569.png)

...然后将这些字节放入数组中。

![](https://hacks.mozilla.org/files/2017/07/02-03-768x569.png)

然后它会将第一个索引（整数）返回给JavaScript。所以JavaScript可以将字节拉出来并使用它们。

现在，大多数JavaScript方法不知道如何直接使用字节。因此，你需要JavaScript端的一些东西，就你您在WebAssembly端做的那样，它可以将字节转换为更有用的值，比如字符串。

在某些浏览器中，你可以使用TextDecoder和TextEncoder API。或者你可以在你的.js文件中添加辅助函数。例如，像Emscripten这样的工具可以添加编码和解码辅助。

![](https://hacks.mozilla.org/files/2017/07/02-05-768x594.png)

这就是使用JS对象作为WebAssembly内存的第一个好处。WebAssembly和JavaScript可以通过内存直接传递值。

### 内存泄漏 Memory leaks

正如我在关于内存管理的文章中提到的那样，当你手动管理内存时，你可能会忘记清除它。这可能会导致系统内存不足。

如果WebAssembly模块实例可以直接访问内存，并且在内存离开作用域之前忘记清除内存，则浏览器可能会发生内存泄露。

但是因为内存对象只是一个JavaScript对象，所以它本身被垃圾收集器跟踪（即使它的内容不是）。

这意味着，当内存对象所附的WebAssembly实例离开作用域时，整个内存数组可能会被垃圾收集。

![](https://hacks.mozilla.org/files/2017/07/02-06-768x567.png)

### 内存隔离 Memory isolation

当人们听到WebAssembly能让你直接访问内存时，它可能会让一些人有点紧张。他们认为一个恶意的WebAssembly模块可以进入内存并且在内存中进行挖掘，但它不应该这样做。但事实并非如此。

ArrayBuffer的数组范围天然提供了一个边界。这是WebAssembly模块可以直接触摸内存的限制。

![](https://hacks.mozilla.org/files/2017/07/02-07-768x574.png)

它可以直接接触该数组内部的字节，但它不能看到超出此数组边界的任何内容。

例如，内存中的任何其他JS对象（如window）都不能被WebAssembly访问。这对安全性非常重要。

当在WebAssembly中加载或存储时，引擎会执行数组边界检查（array bounds checks）以确保地址位于WebAssembly实例的内存中。

如果代码尝试访问超出界限的地址，则引擎将抛出异常。这种行为保护了其余的内存。

![](https://hacks.mozilla.org/files/2017/07/02-08-768x570.png)

这就是内存导入。在下一篇文章中，我们将看看另一种导入更安全的导入方法...[table import(导入表格)](https://hacks.mozilla.org/2017/07/webassembly-table-imports-what-are-they/)。
