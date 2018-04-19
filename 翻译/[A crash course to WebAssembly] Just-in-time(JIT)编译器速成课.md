> ---
>
> title: [A crash course in WebAssembly] Just-in-time(JIT)编译器速成课
> date: 2017-07-11 20:03:14
> categories: 翻译
> tags: WebAssembly, JavaScript
> source: [原文地址](https://hacks.mozilla.org/2017/02/a-crash-course-in-just-in-time-jit-compilers/)
> auther: [Lin Clark](http://code-cartoons.com/)
>
> ---

# Just-in-time(JIT)编译器速成课

---

这是「是什么让WebAssembly这么快」系列的第二部分。如果您还没有阅读其他的，我们建议[从头开始](https://hacks.mozilla.org/2017/02/a-cartoon-intro-to-webassembly/)。

JavaScript起初很慢，后来在JIT的帮助下得到了性能的大幅提升。但JIT又是如何工作的？

---

### Javascript是如何在浏览器中运行的

当你作为一个开发人员向一个页面中添加Javascript是，你会有一个目标和一个问题。

**Goal**：你想告诉计算机该做什么
**Problem**：你和电脑说的不是一种语言

你说的是人类语言，而电脑说机器语言。就像你不把JavaScript或者其他高级编程语言当成人类语言，他们也是如此。即便已经被设计为人类的认知，编程语言依旧无法被机器认知。

所以JavaScript引擎的工作就是把人类语言变成机器可以理解的东西。

我觉得这个就像电影Arriva，那里有人类和外星人在试图互相交谈。

![example image](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2017/02/02-01-alien03.png)

在那部电影里，人类和外星人不只是逐字翻译。这两个群体对世界有着不同的思考方式。人类和机器也是如此（我将在下一篇文章中详细解释这一点）。

那么翻译是如何发生的呢？

在程序世界中，通常有两种翻译成机器语言的途径。即使用 **解释器(interpreter)** 或 **编译器(compiler)** 。

其中解释器逐行翻译，类似听译

![interpreter](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2017/02/02-02-interp02.png)

编译器则一口气完成所有翻译，并记录在新的文件中。

![compiler](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2017/02/02-03-compile02.png)

处理翻译的每种方式都有优点(pros)和缺点(cons)

#### 解释器的优点和缺点

解释器可以快速启动并运行。在开始运行代码之前，您不必经历整个编译步骤。只需要开始翻译第一行并运行它。

正因为如此，解释器似乎解释器天生适合Javascript这样的语言。 Web开发人员能够快速开始并运行代码是非常重要的。

这就是浏览器开始使用JavaScript解释器的原因。

但是当你不止一次运行相同的代码时，使用解释器的缺点就来了。例如，如果你在一个循环。那么你必须一遍又一遍地做同样的翻译。

#### 编译器的优缺点

编译器有相反的权衡。

开始时必须经过编译步骤，启动就需要更多一点的时间，但是，循环中的代码不需要在每个循环中重复翻译，得以运行得更快。

另一个区别是，编译器有更多的时间来查看代码并对其进行解析，从而获得更好的性能。这样的处理被称为优化(optimizations)。

相对的解释器在运行时(runtime)做它的工作，所以在翻译阶段不会有太多的时间来找出这些优化。

### Just-in-time 编译器: 两全其美

作为一种摆脱解释器每次浏览循环时必须不断翻译代码的方法————浏览器开始将编译器混合进来。

不同的浏览器以稍微不同的方式来做这件事，但基本的思路是一样的。他们在JavaScript引擎中增加了一个新的部分，叫做监视器(a monitor, aka a profiler)。该监视器在运行时(runtime)监视代码，并记录运行的次数以及使用的类型。

起初，监视器只是通过看着解释器运行

![monitor](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2017/02/02-04-jit02.png)

如果同一行代码重复运行几次，那么这段代码被称为温暖的(warm)。如果它运行了非常多次，那就称为热门(hot)。

#### baseline 编译器

当一个函数开始变暖时，JIT将把它发送出去编译，然后存储该编译。

![getting warm](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2017/02/02-05-jit06.png)

函数的每一行都被编译成一个“stub”。stub以行号和变量类型为索引(我会解释为什么这很重要)。如果监视器看到execution是使用相同的变量类型再次触发相同的代码，那么它将只提取其编译的版本。

这有助于加快速度。但正如我所说，编译器可以做更多。找出最高效的方法可能需要更多的时间..来进行优化。

baseline编译器将进行一些优化(我给出了一个下面的例子)。它不想花太多时间，也因为它不想要太长的执行时间。

但是，如果代码真的很热(hot)————如果它运行了很多次，那么值得花费额外的时间来进行更多的优化。

#### Optimizing 编译器

当部分代码变热(hot)时，监视器将把它发送给优化编译器。这将创建另一个更快的函数，这个版本也将被存储。

![Optimizing compiler](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2017/02/02-06-jit09.png)

为了制作更快的代码版本，优化编译器必须做出一些假设(assumptions)。

例如，如果它可以假设由特定构造函数创建的所有对象具有相同的形状(shape)，即它们总是具有相同的属性名称，并且这些属性是以相同的顺序添加的 ———— 然后可以根据这些属性切割一些角落。

> For example, if it can assume that all objects created by a particular constructor have the same shape—that is, that they always have the same property names, and that those properties were added in the same order— then it can cut some corners based on that.

优化编译器使用监视器通过监视代码执行来进行判断产生的信息。如果以前所有的循环都是事实，那么它就会认为它将继续是真实的(be true)。

但是，对于JavaScript，当然也没有任何保证。你可以有99个objects都具有相同的shape，但是第100个Object可能缺少一个属性。

所以编译的代码在运行之前需要检查，看看这些假设是否有效。如果是，则运行编译的代码。但如果不是，JIT假定自己做出了错误的假设，并且破坏了(trashes)优化的代码。

![deoptimization](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2017/02/02-07-jit11.png)

然后执行回到解释器或baseline编译版本。这个过程被称为去优化(deoptimization, or bailing out)。

通常优化编译器会使代码更快，但是有时它们可​​能会导致意外的性能问题。如果你的代码不断优化，然后取消优化，那么最终的执行速度比只执行基准编译版本要慢。

大多数浏览器增加了限制，以突破优化/去优化循环。例如，如果JIT做了10次以上的优化尝试，并且不断抛出，那么就会停止尝试。

#### 一个优化的例子: Type specialization

优化有很多不同的类型，但我想举例一种类型，让你们可以感觉到优化如何发生。在优化编译器方面最大的胜利之一来自类型专业化(Type specialization)。

JavaScript使用的动态类型系统在运行时需要一些额外的工作。例如以下代码：

```javascript
function arraySum(arr) {
  var sum = 0;
  for (var i = 0; i < arr.length; i++) {
    sum += arr[i];
  }
}
```

循环中的 `+=` 步骤可能看起来很简单。看起来好你可以一步计算出来，但是由于动态类型(dynamic type)，它需要比预期更多的步骤。

我们假设 `arr` 是一个100个整数的数组。一旦代码变暖，基线编译器将为函数中的每个操作创建一个stub。所以会有一个stub `sum + = arr [i]` ，它将 `+=` 操作作为整数加法处理。

JIT处理这个问题的方式是编译多个 baseline stubs 。如果一段代码是单形的（即总是用相同的类型调用），它将得到一个stub。如果它是多态的（从一次通过代码到另一次通过不同的类型调用），那么它将获得通过该操作的每个类型组合的存根。

这意味着JIT在选择存根之前必须提出许多问题。

![judge type](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2017/02/02-08-decision_tree01.png)

由于每行代码在baseline编译器中都有自己的一组stub，所以每次执行代码时，JIT都需要继续检查这些类型。因此，对于循环中的每一次迭代，都必须提出相同的问题。

![iteration](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2017/02/02-09-jit_loop02.png)

如果JIT不需要重复这些检查，代码将会执行得更快。这是优化编译器所做的事情之一。

在优化编译器中，整个函数被编译在一起。类型检查被移动，以便它们在循环之前发生。

![optimized iteration](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2017/02/02-10-jit_loop02.png)

一些JIT甚至进一步优化。例如，在Firefox中，对于只包含整数的数组有一个特殊的分类。如果arr是这些数组之一，那么JIT不需要检查 `arr[i]` 是否是一个整数。这意味着JIT可以在进入循环之前完成所有的类型检查。

## 总结

简而言之，这就是JIT。它通过监视运行它的代码并发送热代码路径进行优化，使JavaScript运行得更快。使大多数JavaScript应用程序性能的大幅提升。

但即使有了这些改进，JavaScript的性能依旧是不可预测的。为了加快速度，JIT在运行时增加了一些开销，包括：

 - optimization and deoptimization(优化和去优化)
 - memory used for the monitor’s bookkeeping and recovery information for when bailouts happen(储存在deoptimation发生时需要的监视器的簿记和恢复信息)
 - memory used to store baseline and optimized versions of a function(储存baseline版本的函数和优化后的函数)

这里还有改进的空间：可以消除开销，使性能更可预测。这也是WebAssembly所做的一件事情。

在[下一篇文章](https://hacks.mozilla.org/?p=30503)中，我将解释更多关于汇编以及编译器如何使用它的知识。
