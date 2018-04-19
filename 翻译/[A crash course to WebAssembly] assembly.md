> ---
>
> title: [A crash course in WebAssembly] assembly
> date: 2018-3-22 23:58:00
> categories: 翻译
> tags: WebAssembly
> source: [原文地址](https://hacks.mozilla.org/2017/02/a-crash-course-in-assembly/)
> auther: [Lin Clark](http://code-cartoons.com/)
>
> ---

# [A carsh course to WebAssembly] assembly

---

这是WebAssembly系列中的第三部分。如果您还没有阅读其他文章，我们建议您[从头开始](*starting from the beginning*)。

为了理解WebAssembly是如何工作的，对汇编的理解是必须的，这篇文章有助于理解assembly是什么以及编译器如何生成它。

在上篇讲JIT的文章中，我谈到了与机器进行通信就像是与外星人进行交流。

![](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2017/02/03-01-alien03.png)

我想现在看看这个外星人的大脑是如何工作的————这个机器的大脑如何解析和理解传递给它的信息。

这个大脑的一部分专门用于思考，比如加法和减法，或者逻辑运算。还有一部分大脑提供短期记忆，另一部分提供长期记忆。

这些不同的部分有不同的名字：

- The part that does the thinking is the Arithmetic-logic Unit (ALU).
- The short term memory is provided by registers.
- The longer term memory is the Random Access Memory (or RAM).

![](https://hacks.mozilla.org/files/2017/02/03-02-computer_architecture09-768x464.png)



机器代码中的句子称为指令(instruction)。

当一条指令进入大脑时发生了什么呢？指令被划分为不同的部分并且代表了不同的意义。

指令的分割方式取决于大脑的布线方式(the wiring of this brain)。

例如，像这样连线的大脑可能总是会占用前六位，并将它传送给ALU。 ALU会根据1和0的位置来确定它需要将两件事情加在一起。

这块被称为“操作码(opcode)”或operation code，因为它告诉ALU要执行的操作。

![](https://hacks.mozilla.org/files/2017/02/03-03-computer_architecture12-768x543.png)

然后，这个大脑会分别接下来的两个三位数据块，以确定它应该添加哪两个数字。这些将是寄存器的地址。

![](https://hacks.mozilla.org/files/2017/02/03-04-computer_architecture17-768x540.png)

请注意机器代码上方的注释，这使得我们人类更容易理解正在发生的事情。这就是assembly。它被称为符号机器码。这是人类理解机器码的一种方式。它被称为符号机器码。这是人类理解机器码的一种方式。

你可以在这里看到，这个机器的assembly和机器码之间有一个非常直接的关系。正因为如此，您可以拥有不同种类的机器体系结构。当你在机器内部有不同的架构时，它可能需要自己的装配方言(dialect of assembly)。

因此我们不会只有一个翻译的目标。这不只是一种被称为机器码的语言。就像我们人与人之间也会说不同的语言，机器码也有许多种。

要是人类语翻译到外星语，你可能使用的是英语、俄语然后转换成外星文A、外星文B，从编程的角度来看，这就像从C或C++或Rust到x86或到ARM。

你如果想要能够将一个高级编程语言向下编译到一个汇编语言(这对应于不同的体系结构)，做到这一点的一种方法是创建一大堆不同的编译器，将每种汇编码映射到机器码。

![](https://hacks.mozilla.org/files/2017/02/03-05-langs05-768x474.png)

这会是十分低效的。为了解决这个问题，大多数编译器至少添置一个中间层，编译器会获取高级编程语言并将其编译到并不那么高级也不是机器码的中间码，这个中间码被称为 **IR(intermediate representation)**。

![](https://hacks.mozilla.org/files/2017/02/03-06-langs06-768x487.png)

这意味着编译器可以获取任何高级语言并且翻译到一种IR语言。从那里开始，编译器的另一部分可以采用该IR并将其编译为特定于目标体系结构的内容。然后，另一部分的编译器可以将IR语言向下编译到目标架构。

编译器的前端将高级语言翻译到IR，编译器的后端将IR翻译到目标架构的汇编码。

## 结论

这就是assembly到底是什么，并且编译器是如何将高级编程语言编译到汇编码的。在[下一篇文章](https://hacks.mozilla.org/2017/02/creating-and-working-with-webassembly-modules/)中，我们会看到WebAssembly如何适应这种处理模型。
