> ---
>
> title: 什么是 WebAssembly table imports
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

# 什么是 WebAssembly table imports

---

这是此系列的第三篇文章：

- [Creating a WebAssembly module instance with JavaScript](https://hacks.mozilla.org/2017/07/creating-a-webassembly-module-instance-with-javascript/)
- [Memory in WebAssembly (and why it’s safer than you think)](https://hacks.mozilla.org/2017/07/memory-in-webassembly-and-why-its-safer-than-you-think/)
- WebAssembly table imports… what are they?

---

在[第一篇文章](https://hacks.mozilla.org/2017/07/creating-a-webassembly-module-instance-with-javascript/)中，我介绍了WebAssembly模块实例可以具有的四种不同类型的导入：

- values - 值
- function imports - 函数及其闭包
- memory - 内存对象
- table - 表

最后一个可能有点不熟悉。什么是table imports，它能干什么？

有时在一个程序中，你希望能够有一个指向函数的变量，比如回调函数。然后你可以用它做一些事情，比如将它传递给另一个函数。

![](https://hacks.mozilla.org/files/2017/07/03-01-768x161.png)

在C中，这些被称为函数指针。该函数驻留在内存中。指向函数的变量，即函数指针只是指向那个内存地址。

![](https://hacks.mozilla.org/files/2017/07/03-02-768x484.png)

如果需要，稍后可以将变量指向另一个函数。这应该是一个熟悉的概念。

![](https://hacks.mozilla.org/files/2017/07/03-03-768x482.png)

在网页中，所有函数都只是JavaScript对象。而且因为它们是JavaScript对象，所以它们驻留在WebAssembly内存地址之外的内存中。

![](https://hacks.mozilla.org/files/2017/07/03-04-768x176.png)

如果我们想要一个指向这些函数之一的变量，我们需要把它的地址写入我们的记忆中。

![](https://hacks.mozilla.org/files/2017/07/03-05-768x245.png)

但作为网页安全的一部分，保持这些内存地址隐藏是必要的。你不会希望页面上的代码能够查看或操纵该内存地址。如果页面上存在恶意代码，它可以利用内存中的布局位置信息创建漏洞。

例如，它可以改变你已使用的一个内存地址，指向一个不同的内存位置。

然后，当您尝试调用该函数时，实际加载的就是攻击者给予您的内存地址中的任何内容。

![](https://hacks.mozilla.org/files/2017/07/03-06-768x497.png)

恶意代码可能以某种方式插入到内存中，比如嵌入在字符串中。

tables使使用函数指针成为可能，但是以一种不易受到这类攻击的方式。

table是一个数组，它位于WebAssembly的内存之外。这些值具有对函数的引用。

![](https://hacks.mozilla.org/files/2017/07/03-07-768x217.png)

在内部，这些引用包含内存地址，但由于它不在WebAssembly的内存中，因此WebAssembly无法看到这些地址。

不过，它可以访问数组索引。

![](https://hacks.mozilla.org/files/2017/07/03-08-768x172.png)

如果WebAssembly模块想要调用其中一个函数，它会将索引传递给名为`call_indirect`的操作，它会帮忙调用目标函数。

![](https://hacks.mozilla.org/files/2017/07/03-09-768x309.png)

现在table的用例非常有限。它们被添加到规范中以支持这些函数指针，因为 C/C++ 非常依赖这些函数指针。

正因为如此，目前你可以放入table中的唯一引用是对函数的引用。但是，随着WebAssembly功能的扩展（例如，添加直接访问DOM的功能），除了`call_indirect`之外，你还可能会在table中存储其他类型的引用以及能够使用table中的其他操作。
