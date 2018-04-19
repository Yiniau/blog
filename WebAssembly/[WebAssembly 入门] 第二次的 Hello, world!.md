> ---
>
> title: [WebAssembly 入门] 第二次的 Hello, world!
>
> date: 2018-3-29  14:45:00
>
> categories: WebAssembly, 笔记
>
> tags: WebAssembly, JavaScript, Rust, LLVM toolchain
>
> Reference link: [参考链接](https://stackoverflow.com/questions/41353389/how-can-i-return-a-javascript-string-from-a-webassembly-function)
>
> auther: [Yiniau](https://yiniau.com/)
>
> ---

# [WebAssembly 入门] 第二次的 Hello, world!

---

上一次Hello，world做了最基础的实现，主要的代码如下

**src/lib.rs**

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

**main.js**

```javascript
const imports = {
  env: {
    hello_insert_dom: () => {
      const h1 = document.createElement('h1');
      h1.innerHTML = 'Hello, world';
      const body = document.querySelector('body');
      body.appendChild(h1);
    },
  }
};

fetch('build/hello.wasm')
  .then(response => response.arrayBuffer())
  .then(bytes => WebAssembly.instantiate(bytes, imports))
  .then(results => {
    console.log(results);
    const exports = results.instance.exports;
    exports.hello_call_js();
  });
```

ok, 现在让我们来换一个方式实现这个hello，world ———— 通过在WebAssembly中向JS函数传递参数来实现

## 传递参数

但是传参在 MVP 版本的WebAssebly中并不是一件容易的事, WebAssembly只支持整数或浮点数
(支持 i32/i64/f32/f64值类型以及 i8/i16 存储。), 并没有对String的原生支持

想要与实例交互, 可以通过以下属性

- `exports` - JS调用WebAssembly函数的地方, WebAssembly返回的值
- `imports` - 当WebAssembly调用JS时, 具有任意数量的值类型(请注意: 这即不是数组也不是可变参数, 长度必须在Module编译时已知)
- `Memory.buffer` - 这是一个ArrayBuffer，可以使用Uint8Array(等等)进行序列化

所有的方法中似乎直接访问缓冲区是最简单的:
首先我们要在JS中创建一个内存对象并序列化它：
```javascript
// create a memory, Size is in pages.
const memory = new WebAssembly.Memory({ initial: 2 }); // 创建了2页的内存，每个page 65536B -> 64KiB
const buffer = new Uint8Array(memory.buffer); // index arrayBuffer
                                              // 当字符全部是英文时u8足够使用
                                              // 即只有0～255
```

然后在env属性中创建存值函数：

```javascript
env: {
  ...
  save_char_to_memory(num, index) {
    buffer[index] = num;
  },
  ...
}
```

以及hello函数：

```javascript
env: {
  save_char_to_memory(num, index) {
    buffer[index] = num;
  },
  log_string(size, index) {
    let s = "";
    for (let i = index; i < index + size; ++i) {
      s += String.fromCodePoint(buffer[i]);
    }
    console.log(s);
  }
}
```

接下来在rust中完成函数

```rust
extern {
    fn save_char_to_memory(num: u8, index: usize);
    fn log_string(size: usize, index: usize);
}

/// return "Hello, world"'s bytes array
#[no_mangle]
pub extern fn pass_str() { // equal pub extern "C" fn ...
    let s = b"Hello, world";
    unsafe {
        for i in 0..s.len() {
            save_char_to_memory(s[i], i);
        }
        log_string(s.len(), 0);
    }
}
```

## 测试！

测试之前我们的代码应该长这样：

`src/lib.rs`:

```rust
//! a WebAssembly module with Rust

extern {
    fn save_char_to_memory(num: u8, index: usize);
    fn log_string(size: usize, index: usize);
}

/// return "Hello, world"'s bytes array
#[no_mangle]
pub extern fn pass_str() { // equal pub extern "C" fn ...
    let s = b"Hello, world";
    unsafe {
        for i in 0..s.len() {
            save_char_to_memory(s[i], i);
        }
        log_string(s.len(), 0);
    }
}
```

`main.js`:

```javascript
// create a memory, Size is in pages.
const memory = new WebAssembly.Memory({ initial: 2 });
const buffer = new Uint8Array(memory.buffer); // index arrayBuffer

const imports = {
  memory: memory,
  env: {
    save_char_to_memory(num, index) {
      buffer[index] = num;
    },
    log_string(size, index) {
      let s = "";
      for (let i = index; i < index + size; i++) {
        s += String.fromCodePoint(buffer[i]);
      }
      console.log(s);
    }
  }
};

fetch('build/hello.wasm')
  .then(response => response.arrayBuffer())
  .then(bytes => WebAssembly.instantiate(bytes, imports))
  .then(results => {
    console.log(results);
    const exports = results.instance.exports;

    exports.pass_str();
  });
```

**编译运行吧！**

![](https://user-gold-cdn.xitu.io/2018/3/29/16271ecc34f5524a?w=1228&h=636&f=png&s=68490)

## 但是，难道JS与WebAssembly之间的传值只能由JS主导吗？

结果显而易见，之前的文章中有讲到WebAssembly也能够使用memory，但是无法接触到memory范围之外的数据。

那么让我们先打印一下WebAssembly实例的内容

main.js:

```javascript
fetch('build/hello.wasm')
  .then(response => response.arrayBuffer())
  .then(bytes => WebAssembly.instantiateStreaming(bytes, imports))
  .then(results => {
    console.log(results);
  });
```

src/lib.rs:

```rust
//! a WebAssembly module with Rust

extern {
    fn log_string(ptr: *const u8, len: u16);
}

/// pass str | slice to js side
#[no_mangle]
pub extern fn pass_str() { // equal pub extern "C" fn ...
    ...
}
```

result:
  
![](https://user-gold-cdn.xitu.io/2018/3/30/16276091671d6721?w=660&h=302&f=png&s=59800)

嗯？？，memory？
看来这个就是WebAssembly自动创建的Memory实例，并且在附加在`exports.instance`中传递到了JS端

那么我们就能够自然地假设js直接从WebAssembly中获取字符串的裸指针和长度就能够从memory中截取一段buffer并使用`Uint8Array`序列化，再使用`String.fromCharCode`翻译为js String

**try：**

main.js:

```javascript
...
const imports = {
  memory: memory,
  env: {
    store_string_to_buffer(ptr, len) {
      // buffer slice
      let buf = new Uint16Array(mem.buffer, ptr, len);
      let msg = new TextDecoder('utf8').decode(buf);
      // 这是个新的api，你也可以用下面的方法
      // let msg = String.fromCharCode(...buf);
      console.log('msg: ', msg);
      console.log('mbuf: ', buf);
    }
  }
};
...
```

src/lib.rs:

```rust
//! a WebAssembly module with Rust

extern {
    fn log_string(ptr: *const u8, len: u16);
}

/// return "Hello, world"'s bytes array
#[no_mangle]
pub extern fn pass_str() { // equal pub extern "C" fn ...
    let s = "Hello, world";
    unsafe {
        log_string(s.as_ptr(), s.len() as u16);
    }
}

```

**Second attempt:**

![](https://user-gold-cdn.xitu.io/2018/3/30/162761fcd7a402a7?w=1334&h=260&f=png&s=66381)

yeap! 一切如自己所料的感觉真不错！😙

**但是！**

memory对象应该是共享的才对！这样JS端也能够向WebAssembly传值

算了，还是留待下回分解吧