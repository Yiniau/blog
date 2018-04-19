> ---
>
> title: [WebAssembly 入门] 与 Webpack 联动
>
> date: 2018-4-6  19:40:00
>
> categories: WebAssembly, 笔记
>
> tags: WebAssembly, JavaScript, Rust, LLVM toolchain
>
> auther: [Yiniau](https://yiniau.com/)
>
> ---

# 与 Webpack 联动

---

常规的进行rust代码编写再手动编译为wasm文件是十分缓慢的，目前有几种解决方案，接下来我将基于webpack来提升WebAssembly的编写效率。

首先，**webpack 4** 是必须的，此文写下时的version是 *webpack 4.5.0*

具体的webpack安装自行解决

## 配置 webpack

创建一个`webpack.config.js`，输入如下代码

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');
const webpack = require('webpack');

const paths = {
  src: path.resolve(__dirname, 'src'),
  entryFile: path.resolve(__dirname, 'src', 'index.js'),
  dist: path.resolve(__dirname, 'dist'),
  wasm: path.relative(__dirname, 'build'),
}

module.exports = {
  mode: 'development',
  devtool: 'inline-source-map',
  devServer: {
    contentBase: __dirname,
    hot: true,
    port: 10001,
    open: true, // will open on browser after started
  },
  entry: paths.entryFile,
  output: {
    path: paths.dist,
    filename: 'main.js'
  },
  resolve: {
    alias: {
      wasm: paths.wasm,
    }
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: [{
          loader: 'babel-loader',
          options: {
            cacheDirectory: true,
          }
        }],
      },
    ],
  },
  plugins: [
    new CleanWebpackPlugin(['dist']),
    new HtmlWebpackPlugin({
      title: 'WebAssembly Hello World'
    }),
    new webpack.NamedModulesPlugin(),
    new webpack.HotModuleReplacementPlugin(),
  ],
};
```

配置文件解读就不做了，这是个很基础的配置，如果想要学习进阶配置可以看看`create-react-app`里eject出来的webpack配置文件

## 配置 babel

`.babelrc`

```javascript
{
  "presets": [
    "env"
  ],
  "plugins": [
    "syntax-dynamic-import",
    "syntax-async-functions"
  ]
}
```

这里开启了`async`支持和`import()`动态导入的支持

要注意的是，静态的import导入`.wasm`文件并不被webpack内置支持，webpack会在控制台打印错误信息，提示你换成动态导入(Dynamic import)

## 不添加社区loader支持

webpack 4 内置支持解析 `.wasm` 文件，并且import不写后缀时搜索的优先级最高

首先让我们看看目录的情况

```bash
𝝪 yiniau @ yiniau in /Users/yiniau/code/WebAssembly/hello_world
↪ ll
total 952
-rw-r--r--    1 yiniau  staff    52B  3 25 22:14 Cargo.lock
-rw-r--r--    1 yiniau  staff   143B  4  6 21:51 Cargo.toml
drwxr-xr-x    4 yiniau  staff   128B  4  6 01:36 build
-rw-r--r--    1 yiniau  staff    12B  3 26 21:53 build.sh
-rw-r--r--    1 yiniau  staff   170B  4  4 16:26 index.html
drwxr-xr-x  809 yiniau  staff    25K  4  6 20:34 node_modules
-rw-r--r--    1 yiniau  staff   782B  4  6 20:34 package.json
drwxr-xr-x    3 yiniau  staff    96B  4  4 16:41 rust
drwxr-xr-x    4 yiniau  staff   128B  4  4 17:16 src
drwxr-xr-x    5 yiniau  staff   160B  3 29 15:29 target
-rw-r--r--    1 yiniau  staff   1.2K  4  6 15:51 webpack.config.js
-rw-r--r--    1 yiniau  staff   230K  4  6 01:07 yarn-error.log
-rw-r--r--    1 yiniau  staff   216K  4  6 16:09 yarn.lock
```

其中
- `rust` 中存放 `.rs` 文件
- `src` 中存放 `.js` 文件
- `build` 中存放 `.wasm` 文件
- `index.js` 为 entry 指定的入口文件，我在这里引入polyfill

```bash
𝝪 yiniau @ yiniau in /Users/yiniau/code/WebAssembly/hello_world
↪ ll src
total 16
-rw-r--r--  1 yiniau  staff    77B  4  6 20:39 index.js
-rw-r--r--  1 yiniau  staff   1.9K  4  6 21:30 main.js
```

ok，让我们在`main.js`中完成主要的逻辑吧

`main.js`

```javascript
(async () => {
  import('../build/hello.wasm')
    .then(bytes => bytes.arrayBuffer())
    .then(res => WebAssembly.instantiate(bytes, imports))
    .then(results => {
      console.log(results);
      const exports = results.instance.exports;
      console.log(exports);
      mem = exports.memory;
    });
})()
```

oh heck!! 为什么会报错！！

![](https://user-gold-cdn.xitu.io/2018/4/6/1629ba0f83c9051a?w=1128&h=312&f=png&s=106109)

没事，错误信息很明确
> WebAssembly.Instance is disallowed on the main thread, if the buffer size is larger than 4KB. Use WebAssembly.instantiate.

如果 buffer的大小超过了 **4KB**，`WebAssembly.Instance` 在主线程中不被允许使用。需要使用`WebAssembly.instantiate`代替，但是问题来了。

`import()` 并不能传递 importsObject。让我们去 webpack 的github上找找看issue：

[github issue](https://github.com/webpack/webpack/issues/6433)

linclark(a cartoon to WebAssembly 的作者) 提出使用 `instantiateStreaming` 代替 `compileStreaming`，以避免在ios上因快速内存的限制造成的影响。

sokra 对此表示有点反对（应该是非常反对！）

### 不支持的原因

#### 预备信息

webpack试图像ESM一样对待WASM。
将适用于ESM的所有规则/假设也应用于WASM。假设将来WASM JS API可能会被W​​ASM集成到ESM模块图中。

这意味着WASM文件中的`imports`部分（importsObject）与ESM中的`import`语句一样被解析，`exports`部分(instance.exports)被视为像ESM中的`export`部分。

WASM模块也有一个`start`部分，在WASM实例化时执行。

在WASM中，JS API的导入通过`importsObject`传递给实例化的WASM模块。

#### ESM规范

ESM规范指定了多个阶段。一个阶段是`ModuleEvaluation`。在这个阶段，所有的模块都按照明确的顺序进行评估。这个阶段是同步的。所有模块都以相同的“tick”进行评估。

当WASM在模块图中时，这意味着：

- `start`部分在相同的“tick”中执行
- WASM的所有依赖关系都在相同的“tick”中执行
- 导入WASM的ESM在相同的“tick”中执行

对于使用Promise的`instantiate`，这种行为是不可能的。一个Promise总是将它的履行延迟到另一个“tick”中。

只有在使用实例化同步版本（WebAssembly.Instance）时才可能。

注意：从技术上讲，可能会有一个没有`start`部分并且没有依赖关系的WASM。在这种情况下，这不适用。但我们不能认为情况总是如此。

webpack想要并行下载/编译wasm文件与下载JS代码。使用`instantiateStreaming`不会允许这样做（当WASM具有依赖关系时），因为实例化需要传递一个`importsObject`。创建`importsObject`需要评估WASM的所有依赖项/导入，因此需要在开始下载WASM之前下载这些依赖项。

当使用`compileStreaming` + `new WebAssembly.Instance`并行下载和编译是可能的，因为`compileStreaming`不需要一个`importsObject`。可以在WASM和JS下载完成时创建`importsObject`。

#### WebAssembly规范

我也想引用WebAssembly规范。它指出编译发生在后台线程中`compile`和实例化发生在主线程上。

它没有明确说明，但在我看来JSC的行为是不规范的。

![](https://user-images.githubusercontent.com/1365881/35886862-bc229eb4-0b92-11e8-9246-893826282574.png)

![](https://user-images.githubusercontent.com/1365881/35886829-a4eec77c-0b92-11e8-9fad-4bf74a2787d6.png)

#### 其他说明

WASM也缺乏使用导入标识符的实时绑定的能力。相反，`importsObject`将被复制。这可能会伴随奇怪的循环依赖和WASM上的问题。

在`importsObject`中支持`getter`并且能够在执行`start`部分之前获得exports会更好。

## 尝试使用loader直接解析`.rs`

`wasm-loader` 对于直接使用 rustup wasm32-unknown-unknown 编译的.wasm文件支持有问题，看了下wasm-loader使用了基于emcc工具链产出的wasm文件，我试过直接使用

```javascript
rules: [
  {
    test: /\.(js|jsx)$/,
    exclude: /node_modules/,
    use: [{
      loader: 'babel-loader',
      options: {
        cacheDirectory: true,
      }
    }],
  },
  {
    test: /\.wasm$/,
    include: path.resolve(__dirname, 'wasm'),
    use: 'wasm-loader',
  },
],
```

但是会报错：

![](https://user-gold-cdn.xitu.io/2018/4/7/1629f49bc424449f?w=1776&h=192&f=png&s=79955)


这应该是工具链产出的编码问题

于是我再次尝试了使用`rust-native-wasm-loader`：

`webpack.config.js`

```javascript
rules: [
  {
    test: /\.(js|jsx)$/,
    exclude: /node_modules/,
    use: [{
      loader: 'babel-loader',
      options: {
        cacheDirectory: true,
      }
    }],
  },
  {
    test: /\.rs$/,
    include: paths.rust,
    use: [{
      loader: 'wasm-loader'
    }, {
      loader: 'rust-native-wasm-loader',
      options: {
        release: true,
      },
    },]
  },
],
```

`rust/add.rs`

```rust
#[no_mangle]
pub fn add(a: i32, b: i32) -> i32 {
    eprintln!("add({:?}, {:?}) was called", a, b);
    a + b
}
```

`main.js`

```javascript
import loadAdd from 'rust/add.rs';

loadAdd().then(result => {
  const add = result.instance.exports['add'];
  console.log('return value was', add(2, 3));
});
```

BUT!

![](https://user-gold-cdn.xitu.io/2018/4/8/162a5837b2a916ff?w=1576&h=220&f=png&s=117026)

臣妾做不到啊！！

我已经完全按照rust-native-wasm-loader的例子改了，但似乎现在的插件都是在asm.js时代遗留的，都是在解析成`.wasm`那一步失败，是因为WebAssembly不适合以同步方式调用吗。。就目前来看，如果在rust中调用`std::mem`来操作Memory对象，文件大小会非常大——使用`wasm-gc`后依旧有200多的KB

```rust
#![feature(custom_attribute)]
#![feature(wasm_import_memory)]
#![wasm_import_memory]

use std::mem;
use std::ffi::{CString, CStr};
use std::os::raw::{c_char, c_void};

/// alloc memory
#[no_mangle]
// In order to work with the memory we expose (de)allocation methods
pub extern fn alloc(size: usize) -> *mut c_void {
    let mut buf = Vec::with_capacity(size);
    let ptr = buf.as_mut_ptr();
    mem::forget(buf);
    ptr as *mut c_void
}
```

或许webpack的做法并不适合全部的web assembly的应用模式，以ESM的方式处理`.wasm`似乎很美好，但是实际使用可能会成问题，目前主要还是js处理逻辑，为了兼容低版本浏览器使用异步处理（或许是）必须的？

实践于此，与君共勉。