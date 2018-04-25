> ---
>
> title: [WebAssembly 入门] 实现数独游戏 - 如何优雅的组织Rust代码
>
> date: 2018-4-23  22:55:00
>
> categories: WebAssembly, 笔记
>
> tags: WebAssembly, JavaScript, Rust, LLVM toolchain
>
> auther: [Yiniau](https://yiniau.com/)
>
> ---

# [WebAssembly 入门] 实现数独游戏 - 如何优雅的组织Rust代码

---

## 前言

最近探索了一下WebAssembly在不使用 `imports.env.memory` 的情况下如何分配内存，即在Rust端主导内存时如何组织代码

主要是WebAssembly学到了一个阶段，需要一个项目来从整体结构设计上入门它，我之前学习Rust的时候用Piston游戏引擎做过一个Sudoku游戏，把它迁移到WebAssembly + JS上感觉不错，于是有了这片文章。

## 项目初始结构

```bash
ll .
-rw-r--r--    1 yiniau  staff   357B  4 17 18:09 index.html
-rw-r--r--    1 yiniau  staff    81B  4 17 15:37 index.js
drwxr-xr-x  811 yiniau  staff    25K  4 17 16:50 node_modules
-rw-r--r--    1 yiniau  staff   844B  4 21 19:25 package.json
drwxr-xr-x    4 yiniau  staff   128B  4 23 22:23 src
drwxr-xr-x    9 yiniau  staff   288B  4 21 02:26 sudoku
-rw-r--r--    1 yiniau  staff   1.3K  4 17 16:50 webpack.config.js
-rw-r--r--    1 yiniau  staff   224K  4 23 19:39 yarn-error.log
-rw-r--r--    1 yiniau  staff   211K  4 17 16:13 yarn.lock
```

rust模块放在sudoku文件夹下

```bash
ll sudoku
total 16
-rw-r--r--  1 yiniau  staff   3.3K  4 21 19:32 Cargo.lock
-rw-r--r--  1 yiniau  staff   168B  4 21 19:32 Cargo.toml
drwxr-xr-x  4 yiniau  staff   128B  4 23 22:21 src
drwxr-xr-x  6 yiniau  staff   192B  4 19 18:13 target
```

## 基础文件内容

项目已上传github

[project address](https://github.com/Yiniau/webassembly-sudoku)

## 数独实现

### 结构设计

为交互，需要一个检测当前矩阵是否合法的方法，可以是方法，也能独立的函数，权衡之后方法的形式更好，这里有考虑到Rust端的代码组织

对于矩阵的生成，我的思路是先生成一个9*9的数独终盘矩阵，再通过一个挖洞函数生成题目

目前暂时不考虑唯一解，没啥大意思

那么大体上的设计就如下图：

![sudoku game struct](https://user-gold-cdn.xitu.io/2018/4/25/162fbca679a992ec?w=1784&h=1496&f=png&s=255869)

其中backend的结构主要就分两层，Rust -> LLVM -> wasm
全局可变(mutable)静态结构体 `SUDOKU` 实例化 `SudokuMatrix`，初始化data属性，这里不能用Vec(静态声明的限制)，通过ffi语法，`extern fn` 可以导出函数到JS，需要注意的是要用 `#[no_mangle]` 避免编译器打破函数命名。
> LLVM会负责编译代码，但是文件体积巨大，可以通过 `wasm-gc` 将文件大小从600多K减少到200多K

Rust主导内存的控制，这需要JS端在合适的时机从Memory对象中截取数据，不过因为使用了静态变量，我们可以直接使用方法语法，数据头指针也不会变动，数据的修改会变得简便，不过我看过其他人的实现，有人加了Mutux互斥，我倒是觉得JS本身就是单线程，并不是很需要，如果要互斥锁，就不能直接使用静态变量，而是需要使用 `lazy-static` crate ，用lazy-static 宏包裹一个静态指针，e.g.

```rust
lazy-static! {
  static ref SUDOKU = Mutux::new(SudokuMatrix {
    data: [u8; 81]
  });
}
```

但是这样就不能使用可变的方法了～
权衡之下，我还是选择了 `static mut` 声明，主要是就这个项目而言，使用静态变量带来的便利远超过扰乱静态域的副作用来的大。

> 其实我并没有实际体验过滥用静态变量的后果，有踩过坑的小伙伴评论一下呗

因为可以使用可变的方法，导出函数和结构体函数可以分的很明白，接口的控制和补充，以及测试代码的组织都会十分明确，也不用手动alloc/dealloc内存。

### 逻辑实现

#### check函数
check函数检查矩阵是否合法，由几个循环组成

先循环判断行是否合法

```rust
for y in 0..9 { // check row
    let mut checked_value: Vec<u8> = vec![];
    // 直接使用 序列操作符[] 获得的是实际的对象而不是一个reference | pointer | copy
    let row = &m[y];
    row.into_iter()
        .for_each(|&x| {
            checked_value.push(x);
        });
    checked_value.sort();
    if !&SudokuMatrix::arr_repeat_check(&checked_value) {
        return false;
    }
}
```

再判断列和3*3块

```rust
for x in 0..9 {
    { // check if there is any duplication of numbers in a column
        let mut checked_value: Vec<u8> = vec![];
        for y in 0..9 { // check column
            checked_value.push(m[y][x]);
        }
        checked_value.sort();
        if !&SudokuMatrix::arr_repeat_check(&checked_value) {
            return false;
        }
    }
    { // check 3 x 3 matrix
        // x use to point which matrix
        let mut mm_pos: Vec<(usize, usize)> = vec![];
        let y_range = match x / 3 {
            0 => 0..3,
            1 => 3..6,
            2 => 6..9,
            _ => panic!("index err"),
        };
        let x_range = match x % 3 {
            0 => 0..3,
            1 => 3..6,
            2 => 6..9,
            _ => panic!("index err"),
        };
        for y in y_range {
            for x_inm in x_range.clone() {
                mm_pos.push((y, x_inm));
            }
        }
        let mut checked_value: Vec<u8> = vec![];
        mm_pos.into_iter().for_each(|(y, x)| {
            checked_value.push(m[y][x]);
        });
        checked_value.sort();
        if !&SudokuMatrix::arr_repeat_check(&mut checked_value) {
            return false;
        }
    }
}
```

就是把9个数字放到一个数组里测重就行了，但是要注意 `0` 也是一个合法的数字，即玩家没有填数字的情况

#### 终盘生产函数

使用随机化和回朔

```rust
pub fn init(&mut self) -> &mut Self {
    let mut matrix: Vec<Vec<u8>> = vec![
        vec![0,0,0,0,0,0,0,0,0],
        vec![0,0,0,0,0,0,0,0,0],
        vec![0,0,0,0,0,0,0,0,0],
        vec![0,0,0,0,0,0,0,0,0],
        vec![0,0,0,0,0,0,0,0,0],
        vec![0,0,0,0,0,0,0,0,0],
        vec![0,0,0,0,0,0,0,0,0],
        vec![0,0,0,0,0,0,0,0,0],
        vec![0,0,0,0,0,0,0,0,0]
    ];

    let mut y = 0;
    let mut x = 0;
    let mut c_g = 0; // 回退次数计数
    loop {
        if y >= 9 {
            break;
        }
        if c_g >= 10 { // 回退次数过多时释放整个 row
            {
                let m = &mut matrix[y];
                m.into_iter()
                    .for_each(|x| {
                        *x = 0;
                    });
                y -= 1;
                c_g = 0;
            }
            {
                let m = &mut matrix[y];
                m.into_iter()
                    .for_each(|x| {
                        *x = 0;
                    });
                x = 0;
            }
        }

        matrix[y][x] = random_num(1, 10) as u8;

        let mut c = 0; // 计数器

        while !Self::matrix_check(&matrix) {
            c += 1;
            matrix[y][x] = random_num(1, 10) as u8;
            if c >= 20 {
                c_g += 1;
                matrix[y][x] = 0;
                if x == 0 && y > 0 { // matrix 换行
                    y -= 1;
                    x = 8;
                } else if x > 0 {
                    x -= 1;
                }
                c = 0;
            }
        }
        x += 1;
        if x >= 9 { // matrix 换行
            y += 1;
            x = 0;
        }
    }

    for y in 0..9 {
        for x in 0..9 {
            self.data[x + (9 * y)] = matrix[y][x];
        }
    }

    // return self to enable link like invoke
    self
}
```

这里有个要注意的地方 ———— 随机数的生成，rand crate目前不支持 `wasm32-unknown-unknown` 的编译，只能在JS端把`Math.random`包装一下传过来用着先，不然就得自己写。。挺麻烦的，而且rand在github上已经又一个bench用于支持 `wasm32-unknown-unknown` 。


## 测试

可以单元测试一起走

我这边就直接放UA上的结果了

![run result](https://user-gold-cdn.xitu.io/2018/4/25/162fc2ca5a4abdb0)