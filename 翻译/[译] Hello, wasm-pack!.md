> ---
>
> title: [译] Hello, wasm-pack!
>
> date: 2018-4-28  7:29:00
>
> categories: 翻译
>
> tags: WebAssembly
>
> source: [原文地址](https://hacks.mozilla.org/2018/04/hello-wasm-pack/?utm_source=dev-newsletter&utm_medium=email&utm_campaign=apr26-2018)
>
> auther: [Ashley Williams](https://github.com/ashleygwilliams)
>
> ---

# [译] Hello, wasm-pack!

---

正如[Lin Clark在她的关于Rust和WebAssembly的文章](https://hacks.mozilla.org/2018/03/making-webassembly-better-for-rust-for-all-languages/)中强调的那样：WebAssembly的目标不是取代JavaScript，而是**成为一个非常棒的JavaScript工具**。为了简化跨越JavaScript和WebAssembly之间的语言界限，已经涌现了许多许多令人惊叹的成果，你可以阅读[Alex Crichton在wasm-bindgen上发布的所有内容](https://hacks.mozilla.org/2018/04/javascript-to-rust-and-back-again-a-wasm-bindgen-tale/)。本文重点介绍不同类型的 JavaScript/Rust 集成：软件包生态系统(package ecosystem)和开发人员工作流程(developer workflows)。

Rust和JavaScript都有充满活力的包管理生态系统。Rust有[cargo](https://github.com/rust-lang/cargo)和[crates.io](https://crates.io/)。JavaScript有 [几个](https://yarnpkg.com/en/) [CLI](https://github.com/pnpm/pnpm) [工具](https://github.com/cnpm/cnpm)，包括与npm注册表连接的npm CLI。为了使WebAssembly获得成功，我们需要这两个系统良好地协同工作，特别是：

- Rust开发人员应该能够生成用于JavaScript的WebAssembly包，而不需要Node.js开发环境
- JavaScript开发人员应该能够使用WebAssembly而不需要Rust开发环境

✨📦 Enter: `wasm-pack`.

`wasm-pack` **是用于组装和包装以WebAssembly为目标的Rust包装箱的工具。这些软件包可以发布到npm Registry并与其他软件包一起使用。这意味着你可以将它们与JS和其他软件包并行使用，并且可以在许多类型的应用程序中使用**，例如Node.js服务器端应用程序，Webpack捆绑的客户端应用程序或任何其他类型的应用程序使用npm依赖关系。你可以在[crates.io](https://crates.io/crates/wasm-pack)和[GitHub](https://github.com/ashleygwilliams/wasm-pack)上找到  `wasm-pack` 。

这个工具的开发刚刚开始，我们很高兴能够从涉及Rust和JavaScript的开发人员中获得更多的帮助。JavaScript和Rust生态系统都专注于开发者体验。我们首先知道，生产高效的生态系统的关键是使自动执行枯燥任务并摆脱开发人员方式的好工具。首先我们知道，一个高效生产的生态系统的关键是自动执行枯燥任务并使开发人员摆脱无聊方式的好工具。在本文中，我将讨论我们的进度，我们的目标，如何开始使用工具，以及如何参与塑造未来。

## 💁 What it does today

![](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/04/s_A9A8C3816B05444A18E82A6772DDBA985038B68FF6B90B7266556F8069E039E0_1523913938177_wasm-pack2-02.png)

现在，`wasm-pack` 引导你完成四个基本步骤，准备将你的Rust代码作为WebAssembly包发布到npm注册表中：

### 1. Compile to WebAssembly

`wasm-pack` 将使用 `rustup` 添加相应的WebAssembly编译目标，并以发布模式编译Rust到WebAssembly。

- 如果需要，添加wasm32-unknown-unknown编译目标
- 将你的项目编译到发布版本

### 2. Run wasm-bindgen

`wasm-pack` 包装了 `wasm-bindgen` 工具的CLI部分并为你运行它！这可以将WebAssembly模块封装在JS包装中，这样人们可以更轻松地与模块进行交互。`wasm-bindgen` 同时支持ES6模块和CommonJS，你可以使用 `wasm-pack` 生成任意类型的包！

### 3. Generate package.json

`wasm-pack` 会读取你的 `Cargo.toml` 并生成将你的软件包发布到npm注册表所需的package.json文件。它会：

- 复制你的项目名称和说明
- 链接到你的Rust项目的存储库
- 列出文件密钥中生成的JavaScript文件。这可以确保有且只有这些文件包含在npm包中。如果你打算在浏览器中使用此软件包或包含此软件包的软件包，这对于确保良好的性能尤为重要！

### 4. Documentation

`wasm-pack` 会将你的Rust项目的README.md复制到它生成的npm包中。我们有很多关于如何进一步扩展以支持Rust生态系统的文档功能rustdoc的更多想法 - 更多内容请参见下一节！

## 🔮 未来计划

### 与`rustdoc`集成

crates.io团队对开发人员进行了调查，并了解到开发人员在使用crate时的情况时最希望获得的是优秀的文档。贡献者[Yoshua Wuyts](https://github.com/yoshuawuyts)介绍了通过将rust-pack与Rust API文档工具`rustdoc`集成来生成更多README.md内容的绝妙想法。Rust-wasm团队致力于使Rust成为编写WebAssembly的一流方式。提供Rust生成的WebAssembly软件包的文档，这些软件包易于编写，易于发现，与我们的目标完美匹配。详细了解团队在[这个iusse](https://github.com/ashleygwilliams/wasm-pack/issues/7#issuecomment-368551714)上的想法并加入讨论！

### 管理和优化你的Rust和JS依赖关系图

下一个关于`wasm-pack`的大型开发工作将着重于在编译的WebAssembly中使用自定义段来声明对本地Javascript文件或其他npm包的依赖关系。

这个功能的初步工作已经落在`wasm-bindgen`中，所以下一步将把它整合到`wasm-pack`中。开始的整合不会太棘手 - 但我们很高兴拥有必须简化和优化Rust依赖关系树的探索机会，这些依赖关系树在几个层次上包含npm依赖关系！这项工作与Webpack这样的捆绑软件提供的优化类似，但是Rust层面的依赖关系。

我们仍然需要解决很多问题，并且要做许多简单的工程工作。在几个星期内，这个主题会有一个完整的帖子，所以请留意！

![](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/04/s_A9A8C3816B05444A18E82A6772DDBA985038B68FF6B90B7266556F8069E039E0_1523916327709_wasm-pack3-03.png)

### 在Rust中增加Node.js工具链

这个项目最大且最雄心勃勃的目标是在Rust中重写所需的`npm login`，`npm pack`和`npm publish`步骤，以便对于那些目前不使用Node.js的人来说，对Node.js开发环境的所需依赖在其工作流程中变得可选。正如我们之前所说的，我们希望确保WebAssembly包开发者和用户都可以继续使用他们熟悉的工作流程。目前，JavaScript开发人员的确如此 - 他们不需要拥有Rust开发环境或Rust的任何知识即可开始使用已使用`wasm-pack`发布的使用Rust生成的WebAssembly模块。

### 与npm和打包器协作

我们一直与npm CLI团队成员[Kat Marchan](https://github.com/zkat)和[Rebecca Turner](https://github.com/iarna)以及[webpack](https://webpack.js.org/)和[Parcel](https://parceljs.org/)工作人员进行交流 - 我们很高兴与他们继续合作，以便开发人员轻松发布和使用WebAssembly代码！

## 🛠 Start using it today!

`wasm-pack`目前是通过Cargo发布的命令行工具。要安装它，请[安装Rust开发环境](https://www.rust-lang.org/en-US/install.html)，然后运行：
```bash
cargo install wasm-pack
```

如果您不确定从哪里开始，我们为您提供了[一个教程](https://rust-lang-nursery.github.io/rust-wasm/wasm-pack/introduction.html)！本教程由[Michael Gattozzi](https://github.com/mgattozzi)和Rust-wasm工作小组带领您：

- 写一个小的Rust库
- 将其编译为WebAssembly，打包并使用wasm-pack进行发布
- 捆绑webpack制作一个小型网站

![](https://d2mxuefqeaa7sj.cloudfront.net/s_A9A8C3816B05444A18E82A6772DDBA985038B68FF6B90B7266556F8069E039E0_1524011130215_demo.gif)

## 👯‍♀️贡献

所有优秀的开发人员工具的关键是该工具的开发人员与在日常工作流程中使用该工具的开发人员之间的短反馈周期。

为了让wasm-pack更完美，也为我们所有的WebAssembly开发人员工具，我们需要所有技能水平和背景的开发人员参与其中！

看看我们的[贡献者指南](https://github.com/ashleygwilliams/wasm-pack/blob/master/CONTRIBUTING.md)和[问题跟踪器](https://github.com/ashleygwilliams/wasm-pack/issues)（我们定期将事情标记为“[优先的好问题](https://github.com/ashleygwilliams/wasm-pack/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22)”，并提供指导和指导！） - 我们很高兴与您合作！

## 关于 [Ashley Williams](https://github.com/ashleygwilliams)

Ashley Williams是Integer32的一名工程师，负责Mozilla的Rust编程语言。她是Rust Core团队的成员，领导Rust社区团队，并帮助维护Rust的软件包注册表crates.io。之前，她曾在Javascript的包管理员npm担任工程师，目前是Node.js基金会董事会的个人会员。作为一名有长期教育经历的老师，Ashley将大部分精力都集中在开源项目的教育计划上，2016年创建了NodeTogether，目前正在领导RustBridge计划。她曾代表教师向TC39会议提出看法来影响JavaScript的发展，并热衷于通过她在WebAssembly上的工作来推动web的发展。