> ---
>
> title: [A crash course in WebAssembly] 为什么WebAssembly这么快
> date: 2018-3-22 23:58:00
> categories: 翻译
> tags: WebAssembly
> source: [原文地址](https://hacks.mozilla.org/2017/02/what-makes-webassembly-fast/)
> auther: [Lin Clark](http://code-cartoons.com/)
>
> ---

# [A crash course to WebAssembly] 为什么WebAssembly这么快

---

*这是WebAssembly系列文章的第五部分，说明了它的快速之处。如果您还没有阅读其他文章，我们建议您[从头开始](https://hacks.mozilla.org/2017/02/a-cartoon-intro-to-webassembly/)。*

在[上一篇文章](https://hacks.mozilla.org/2017/02/creating-and-working-with-webassembly-modules/)中，我解释说使用WebAssembly或JavaScript进行编程不是一种是或不是的选择。我们并不期望太多的开发人员会编写完整的WebAssembly代码库。

所以开发人员不需要在他们的应用程序中选择使用WebAssembly还是JavaScript。但是，我们希望开发人员将Web部件的JavaScript代码部分换掉。

例如，使用React的团队可以将他们的调解器代码（reconciler code，又名虚拟DOM）用WebAssembly版本替换，而使用React的人不需要做任何事情......他们的应用程序不但可以像以前一样工作，还能够获得使用WebAssembly的好处。

开发人员之所以喜欢React团队的开发人员进行这种替换，是因为WebAssembly速度更快。但关键是什么让它更快？

## 今天的JavaScript性能如何？

在我们理解JavaScript和WebAssembly之间的性能差异之前，我们需要了解JS引擎所做的工作。

这张图给出了一个应用程序启动性能的粗略情况。

> JS引擎执行这些任务的时间取决于页面使用的JavaScript。此图并不意味着代表精确的性能数字。相反，它旨在提供一个高级模型，用于说明JS和WebAssembly中相同函数的性能会有所不同。

![](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2017/02/05-01-diagram_now01.png)

每个格子显示花在完成特定任务上的时间。

- Parsing - 解析 - 将源代码处理成解释器可以运行的东西所需的时间。

- Compiling + optimizing - 编译 + 优化 - 在基线编译器和优化编译器中花费的时间。一些优化编译器的工作不在主线程中，所以不包含在这里。

- Re-optimizing - 重优化(去优化 + 优化) - 当JIT的*假设(assumptions)*失败时，JIT重新优化代码和将优化后的代码回退到基线代码(baseline code)花费的格外的时间。

- Execution - 执行 - 运行代码所需的时间。

- Garbage collection - 垃圾回收 — 画在内存清理上的时间。

需要注意的一件重要事情是：这些任务不会以离散块或特定顺序发生。相反，它们是交错的。一些解析将会发生，然后是一些执行，然后是一些编译，然后是一些更多的解析，然后是更多的执行等等。

这种改进所带来的性能是JavaScript早期的一大改进，从前的性能图看起来更像这样：

![](https://hacks.mozilla.org/files/2017/02/05-02-diagram_past01-768x225.png)

一开始，当它只是一个运行JavaScript的解释器时，执行速度很慢。当JIT被引入时，它大大加快了执行时间。

JIT权衡了监视和编译代码的开销。如果JavaScript开发人员一直以相同的方式编写JavaScript，那么解析和编译时间将会大幅减少。但性能的提升诱使开发人员创建更大的JavaScript应用程序。

这也意味着还有改进的余地。

## WebAssembly的优势又在哪里？

下面是WebAssembly与典型Web应用程序的性能比较示意图。

![](https://hacks.mozilla.org/files/2017/02/05-03-diagram_future01-768x328.png)

不同浏览器之间在处理这些阶段方面各有实现。我在这里使用SpiderMonkey作为我的模型。

## Fetching

这在示意图中没有标示，但确实需要花费时间的一件事就是从服务器上获取文件。

因为WebAssembly比JavaScript更紧凑，所以抓取速度更快。尽管压缩算法可以显着减小JavaScript包的大小，但WebAssembly的压缩的二进制表示仍然具有优势。

这意味着它在服务器和客户端之间传输所花费的时间更少。这在慢速网络上尤其明显。

## Parsing

一旦它到达浏览器，JavaScript源代码就会被解析为抽象语法树(AST)。

浏览器通常会惰性地执行此操作，只先解析它们真正需要的内容，然后为尚未调用的函数创建存根(stubs)。

在这个阶段，AST被转换为特定于该JS引擎的中间表示(intermediate representation - IR，为 bytecode - 字节码)

相反，WebAssembly不需要经过这个转换，因为它已经是一个IR。它只需要解码和验证，以确保其中没有错误。

![](https://hacks.mozilla.org/files/2017/02/05-04-diagram_compare02-768x259.png)

## Compiling + optimizing

正如我在[关于JIT的文章](https://hacks.mozilla.org/2017/02/a-crash-course-in-just-in-time-jit-compilers/)中解释的那样，JavaScript是在执行代码期间编译的。根据运行时(Runtime)使用的类型，可能需要编译相同代码的多个版本。

不同的浏览器处理编译WebAssembly的方式不同。一些浏览器在开始执行WebAssembly之前进行基线编译，一些则使用JIT。

无论哪种方式，WebAssembly从一开始就更接近机器码。例如: 类型是程序的一部分。以下是几个快速的原因：

1. 编译器不必花费时间运行代码，以在开始编译优化的代码之前观察正在使用的类型。

2. 编译器不必根据它观察到的不同类型编译相同代码的不同版本。

3. 在LLVM中已经提前做了很多的优化。因此编译和优化工作花费的时间更少。

## Reoptimizing

有时JIT必须丢弃代码的优化版本并再次尝试优化。

当JIT基于已运行代码做出的假设不正确时，就会发生这种情况。例如，当进入循环的变量与先前迭代中的变量不同时，或者在原型链中插入新函数时，就会发生去优化。

去优化有两个成本。首先，退出优化后的代码并返回到基准版本需要一段时间。其次，如果该函数仍被大量调用，则JIT可能决定再次向优化编译器发送它，重复优化。

在WebAssembly中，像类型这样的东西是显式的，所以JIT不需要根据它在运行时收集的数据来对类型进行假设。这意味着重优化将被省略。

![](https://hacks.mozilla.org/files/2017/02/05-06-diagram_compare04-768x309.png)

## Executing

要编写能够如预期执行的JavaScript代码，你需要了解JIT所做的优化。例如，你需要知道怎么样编写代码，才便于编译器优化代码，这在[JIT文章](https://hacks.mozilla.org/2017/02/a-crash-course-in-just-in-time-jit-compilers/)中有所描述。

但是大多数开发人员并不了解JIT内部。 即使对那些了解JIT内部的开发人员来说，也很难达到最佳状态。 人们用来使代码更易读的许多编码模式（例如将通用任务抽象为跨类型函数的函数，范型）妨碍了编译器优化代码。

另外，不同浏览器之间JIT使用的优化是不同的，所以同样的编码可能在另一个浏览器中表现出更低的性能。

因此，在WebAssembly中代码的执行通常更快。 WebAssembly并不需要JIT对JavaScript进行的许多优化（例如类型专用化）。

另外，WebAssembly被设计为编译导向的。 这意味着它被设计为由编译器生成，而不是由程序员手工编写。

由于人类程序员不需要直接对其进行编程，因此WebAssembly可以提供一系列对机器更为理想的指令。根据代码的工作类型，这些指令的运行增速从10％到800％不等。

![](https://hacks.mozilla.org/files/2017/02/05-07-diagram_compare05-768x263.png)

## Garbage collection

在JavaScript中，开发人员无需担心变量的回收处理，JS引擎通过使用称为垃圾回收器的东西自动执行该操作。
> v8 wiki 中有提到GC的工作机制————整个世界都会为之停止

但是，如果你想要可预测的性能，这就可能会成为问题。你无法控制垃圾回收器何时工作，它可能会在不大方便的时候发生。大多数浏览器已经很好地调度它，但这依旧会阻碍代码的执行。

WebAssembly不支持垃圾收集(至少现在)。内存是手动管理的（就像 C/C++ & Rust 这样的语言）。虽然这可能会使开发人员的编程变得更加困难，但它也可以使性能更加一致。

![](https://hacks.mozilla.org/files/2017/02/05-08-diagram_compare06-768x314.png)

## 总结

在许多情况下，WebAssembly比JavaScript快，因为：

- 获取WebAssembly所需的时间较少，压缩率相比JS也更高。

- 解码WebAssembly比解析JavaScript花费更少的时间。

- 编译和优化花费更少的时间，因为WebAssembly比JavaScript更接近机器码，并且已经在服务器端进行了优化。

- 重新优化不会发生，因为WebAssembly内置了类型和其他信息，所以JS引擎不需要像优化JavaScript时那样推测它何时可以优化。

- 执行通常花费的时间更少，因为减少了不同浏览器的优化差异，而且WebAssembly的指令集对于机器来说更为理想。

- 不需要GC。

这就是为什么在许多情况下，WebAssembly在执行相同任务时性能将大大超越JavaScript。

在某些情况下，WebAssembly的表现并不像预期的那样好，但同样也在某些场景中获得更高的性能。我将在[下一篇文章](https://hacks.mozilla.org/2017/02/where-is-webassembly-now-and-whats-next/)中介绍这些内容。

