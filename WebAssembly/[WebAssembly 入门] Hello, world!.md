> ---
>
> title: [WebAssembly 入门] Hello, world!
>
> date: 2018-3-29  14:45:00
>
> categories: WebAssembly, 笔记
>
> tags: WebAssembly, JavaScript, Rust, LLVM toolchain
>
> auther: [Yiniau](https://yiniau.com/)
>
> ---

# [WebAssembly 入门] Hello, world!

---

进过一段时间的基础知识学习，是时候正式开始WebAssembly编程了！

我花了2个月左右的时间入门Rust，拿它写了一个sudoku游戏。
因此选择 `Rust` -> `LLVM toolchain` -> `WebAssembly` 对我而言是比较自然的。

## 准备
**第一** 通过 Cargo 新建一个 lib 项目

```bash
cargo new hello_world --lib && cd hello_world
```

**第二** 需要一个HTML承载JS代码并显示效果

```bash
vim index.html
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>WebAssembly</title>
</head>
<body>
<script src="main.js"></script>
</body>
</html>
```

**第三** 创建JS文件

```bash
vim main.js
```

## 正式开始探险！

开始之前。确认一下现在的目录情况
```bash
ll .
-rw-r--r--  1 yiniau  staff    52B Mar 25 22:14 Cargo.lock
-rw-r--r--  1 yiniau  staff   112B Mar 25 22:14 Cargo.toml
-rw-r--r--  1 yiniau  staff   165B Mar 29 15:00 index.html
-rw-r--r--  1 yiniau  staff   743B Mar 29 15:00 main.js
drwxr-xr-x  3 yiniau  staff    96B Mar 29 14:58 src

ll src
-rw-r--r--  1 yiniau  staff   494B Mar 29 14:35 lib.rs
```

依照之前的几片翻译, 我们能够通过 `fetch` 获取.wasm文件并通过转换成类型数组并传入 `WebAssembly.instantiate` 进行编译
但是我们还没有一个.wasm文件，当务之急是在lib.rs中编辑Rust文件并通过 `rustc` 编译输出 .wasm 文件
> rustup 工具链请自行搜索安装，十分简单。

千军万马，注释先行
```rust
//! hello world with WebAssembly
```

作为开始的一小步，先从最简单的开始————在WebAssembly中调用JS传递过来的函数及其闭包，主要的逻辑在JS函数中封装好

### 声明imports导入资源
我们可以通过 在imports导入资源中添加环境属性来实现，先来创建一个imports对象
```javascript
const imports = {
  env: {}
}
```

### 添加函数/闭包
在`env`属性中添加想要传递的函数

```javascript
env: {
  function hello_world() {
    const h1 = document.createElement('h1');
    h1.innerHTML = 'Hello, world';
    const body = document.querySelector('body');
    body.appendChild(h1);
  }
}
```

然后让我们转战 `lib.rs`

### 声明外部函数

要使用JS的函数，我们需要利用ffi，先在extern中声明外部函数

```rust
//! a WebAssembly module with Rust
extern {
  fn hello_world();
}
```

这下就能够在函数体中调用了

### WebAssembly函数体

实现js中调用的函数接口

```rust
/// call js function to access DOM
#[no_mangle]
pub extern fn hello_call_js() { // equal pub extern "C" fn ...
    unsafe {
        hello_insert_dom();
    }
}
```

ok, 目前`lib.rs`应该长这样了

```rust
//! a WebAssembly module with Rust

extern {
    fn hello_insert_dom();
}

/// return "Hello, world"'s bytes array
#[no_mangle]
pub extern fn hello_call_js() { // equal pub extern "C" fn ...
    unsafe {
        hello_insert_dom();
    }
}
```

到此，rust的准备工作已经就绪，下一步就是**编译**了

### 编译

在zsh中输入

```bash
mkdir build
rustc +nightly --target=wasm32-unknown-unknown -O --crate-type=cdylib src/lib.rs -o build/hello.wasm

# +nightly 指明使用nightly版本
# --target 指定编译器后
# -O == -C opt-level=2
# -C opt-level=2 指定优化级别为2，范围是 0-3
# --create-type=cdylib 添加一个由编译器接受的crate类型，称为cdylib，它对应于从Rust动态库中导出的C接口。
# -o 指定输出文件名
```
> // TODO：记录第一个问题，动态库是啥，使用这个库有什么用。

**DONE!!**

我们可以在 `build/` 目录中找到 hello.wasm 文件
> 如果想看看长什么样子，可以用 `hexdump` 查看

### 在JS中导入.wasm文件并使用

```javascript
fetch('build/hello.wasm') // 获取到的是以16进制表达的二进制文件
  .then(res => res.arrayBuffer()) // 放入数组缓冲区
  .then(bytes => WebAssembly.instantiate(bytes, imports)) // 将二进制数据传入，交给JIT处理，同时携带imports导入资源
  .then(results => { // 返回的结果中有，有两个属性
                     // 一个是`module`, 这是已编译的 `WebAssembly.module`
                     // 一个是`instance`, `WebAssembly.Instance`, 也就是WebAssembly.module的第一个实例
    const exports = results.instance.exports; // instance.exports 携带了 rust 中 pub extern 声明的函数(即导出的函数)
    exports.hello_call_js(); // 调用
  })
```

现在我们的`main.js`应该长这样：

```javascript
const imports = {
  env: {
    hello_insert_dom: () => {
      const h1 = document.createElement('h1');
      h1.innerHTML = 'Hello, world';
      const body = document.querySelector('body');
      body.appendChild(h1);
    }
  }
};

fetch('build/hello.wasm')
  .then(response => response.arrayBuffer())
  .then(bytes => WebAssembly.instantiate(bytes, imports))
  .then(results => {
    console.log(results);
    const exports = results.instance.exports;
    exports.hello_call_js();
    // exports.hello_call_js_pass_data();
  });
```

## 测试，激动人心！

首先开启一个本地服务, 我用的lighttpd, 这个随意

localhost!

![](https://user-gold-cdn.xitu.io/2018/3/29/16270e143b7c0385?w=2610&h=1830&f=png&s=673335)

## 接下来的计划

hello_world并不会就此结束，为了进一步了解WebAssembly
我准备通过各种方式实现hello_world来实践WebAssembly


