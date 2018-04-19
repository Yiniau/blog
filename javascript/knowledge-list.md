> ---
>
> title: konwledge-list
>
> data: 2018-03-13 18:42:00
>
> categories: note
>
> tags: Javascript & knowledge list
>
> ---

# Knowledge List

---

## 编程基础

- [x] **面向对象的三大特性，五大原则**
  - 三大特性
    (1)**封装**(Encapsulation)

    所谓封装，也就是把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行信息隐藏。
    封装是面向对象的特征之一，是对象和类概念的主要特性。
    简单的说，一个类就是一个封装了数据以及操作这些数据的代码的逻辑实体。
    在一个对象内部，某些代码或某些数据可以是私有的，不能被外界访问。通过这种方式，对象对内部数据提供了不同级别的保护，以防止程序中无关的部分意外的改变或错误的使用了对象的私有部分。

    (2)**继承**(Inheritance)

    继承是指这样一种能力：
    它可以使用现有类的所有功能，并在无需重新编写原来的类的情况下对这些功能进行扩展。
    通过继承创建的新类称为“子类”或“派生类”，被继承的类称为“基类”、“父类”或“超类”。继承的过程，就是从一般到特殊的过程。要实现继承，可以通过“继承”（Inheritance）和“组合”（Composition）来实现。继承概念的实现方式有二类：实现继承与接口继承。实现继承是指直接使用基类的属性和方法而无需额外编码的能力；接口继承是指仅使用属性和方法的名称、但是子类必须提供实现的能力；

    (3)**多态**(Polymorphism)

    所谓多态就是指一个类实例的相同方法在不同情形有不同表现形式。
    多态机制使具有不同内部结构的对象可以共享相同的外部接口。这意味着，虽然针对不同对象的具体操作不同，但通过一个公共的类，它们（那些操作）可以通过相同的方式予以调用。

    最常见的多态就是将子类传入父类参数中，运行时调用父类方法时通过传入的子类决定具体的内部结构或行为。

  - 五大原则
    (1)**单一职责原则**（Single-Resposibility Principle）

    •一个类应该仅有一个引起它变化的原因

    职员类例子： 比如在职员类里，将工程师、销售人员、销售经理这些情况都放在职员类里考虑，其结果将会非常混乱，在这个假设下，职员类里的每个方法都要ifelse判断是哪种情况，从类结构上来说将会十分臃肿，并且上述三种的职员类型，不论哪一种发生需求变化，都会改变职员类！这个是大家所不愿意看到的！

    (2)**开放封闭原则**（Open-Closed principle）

    •对扩展是开放的，对更改是封闭的！

    变化来临时，如果不必改动软件实体裁的源代码，就能扩充它的行为，那么这个软件实体设计就是满足开放封闭原则的。如果说我们预测到某种变化，或者某种变化发生了，我们应当创建抽象类来隔离以后发生的同类变化。

    (3)**里氏替换原则**（Liskov-Substituion Principle）

    •子类可以替换父类并且出现在父类能够出现的任何地方,贯彻GOF倡导的面向接口编程

    在这个原则中父类应尽可能使用接口或者抽象类来实现！

    子类通过实现了父类接口，能够替父类的使用地方！

    通过这个原则，我们客户端在使用父类接口的时候，通过子类实现！

    意思就是说我们依赖父类接口，在客户端声明一个父类接口，通过其子类来实现

    这个时候就要求子类必须能够替换父类所出现的任何地方，这样做的好处就是，在根据新要求扩展父类接口的新子类的时候而不影响当前客户端的使用！

    (4)**依赖倒置原则**（Dependecy-Inversion Principle）

    •传统的结构化编程中，最上层的模块通常都要依赖下面的子模块来实现，也称为高层依赖低层！

    所以DIP原则就是要逆转这种依赖关系，让高层模块不要依赖低层模块，所以称之为依赖倒置原则！

    (5)**ISP** 接口隔离原则(Interface-Segregation Principle)

    •使用多个专门的接口比使用单个接口要好的多！

    这个我有体会，在我实际编程中，为了减少接口的定义，将许多类似的方法都放在一个接口中，最后发现，维护和实现接口的时候花了太多精力，而接口所定义的操作相当于对客户端的一种承诺，这种承诺当然是越少越好，越精练越好，过多的承诺带来的就是你的大量精力和时间去维护！

- [x] **端口号的作用**
  - 实现IP地址与网络服务的一对多
  - 一部分端口特别约定为特别服务所用，如 https -> 443

- [x] **UTF-8 & GBK2312 & Unicode**
  Unicode 是 字符集，为世界上所有国家所有文字每个字符都分配了一个码位，如「知」的码位是 30693，记作 U+77E5（30693 的十六进制为 0x77E5）。

  UTF-8 和 GBK2312 都是编码规则，规定了码位转换成字节序列的规则(编码/解码 类似于 加密/解密)

  unicode在很长一段时间内无法推广，直到互联网的出现，为解决unicode如何在网络上传输的问题，于是面向传输的众多 UTF（UCS Transfer Format）标准出现了，顾名思义，UTF-8就是每次8个位传输数据，而UTF-16就是每次16个位。UTF-8就是在互联网上使用最广的一种unicode的实现方式，这是为传输而设计的编码，并使编码无国界，这样就可以显示全世界上所有文化的字符了。UTF-8最大的一个特点，就是它是一种变长的编码方式。它可以使用1~4个字节表示一个符号，根据不同的符号而变化字节长度，当字符在ASCII码的范围时，就用一个字节表示，保留了ASCII字符一个字节的编码做为它的一部分，注意的是unicode一个中文字符占2个字节，而UTF-8一个中文字符占3个字节）。从unicode到utf-8并不是直接的对应，而是要过一些算法和规则来转换。

  |Unicode符号范围         |   UTF-8编码方式    |
  |:--------------------- | ---------------: |
  |(十六进制)              |  （二进制）        |
  | 0000 0000-0000 007F | 0xxxxxxx |
  | 0000 0080-0000 07FF | 110xxxxx 10xxxxxx |
  | 0000 0800-0000 FFFF | 1110xxxx 10xxxxxx 10xxxxxx |
  | 0001 0000-0010 FFFF | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx |

  总结：
  - 中国人民通过对 ASCII 编码的中文扩充改造，产生了 GB2312 编码，可以表示6000多个常用汉字。
  - 汉字实在是太多了，包括繁体和各种字符，于是产生了 GBK 编码，它包括了 GB2312 中的编码，同时扩充了很多。
  - 中国是个多民族国家，各个民族几乎都有自己独立的语言系统，为了表示那些字符，继续把 GBK 编码扩充为 GB18030 编码。
  - 每个国家都像中国一样，把自己的语言编码，于是出现了各种各样的编码，如果你不安装相应的编码，就无法解释相应编码想表达的内容。
  - 终于，有个叫 ISO 的组织看不下去了。他们一起创造了一种编码 UNICODE ，这种编码非常大，大到可以容纳世界上任何一个文字和标志。所以只要电脑上有 UNICODE 这种编码系统，无论是全球哪种文字，只需要保存文件的时候，保存成 UNICODE 编码就可以被其他电脑正常解释。
  - UNICODE 在网络传输中，出现了两个标准 UTF-8 和 UTF-16，分别每次传输 8个位和 16个位。于是就会有人产生疑问，UTF-8 既然能保存那么多文字、符号，为什么国内还有这么多使用 GBK 等编码的人？因为 UTF-8 等编码体积比较大，占电脑空间比较多，如果面向的使用人群绝大部分都是中国人，用 GBK 等编码也可以。

  [链接](https://www.zhihu.com/question/23374078/answer/69732605)

## 前端工程

- [ ] **首屏、白屏时间如何计算**
  - 白屏时间
    白屏时间指的是浏览器开始显示内容的时间。因此我们只需要知道是浏览器开始显示内容的时间点，即页面白屏结束时间点即可获取到页面的白屏时间。

    - 计算白屏时间
      通常认为浏览器开始渲染 <body> 标签或者解析完 <head> 标签的时刻就是页面白屏结束的时间点。
      ```html
      <!DOCTYPE html>
      <html lang="en">
      <head>
        <meta charset="UTF-8">
        <title>白屏</title>
        <script type="text/javascript">
          // 不兼容performance.timing 的浏览器，如IE8
          window.pageStartTime = Date.now();
        </script>
        <!-- 页面 CSS 资源 -->
        <link rel="stylesheet" href="common.css">
        <link rel="stylesheet" href="page.css">
        <script type="text/javascript">
          // 白屏时间结束点
          window.firstPaint = Date.now();
        </script>
      </head>
      <body>
        <!-- 页面内容 -->
      </body>
      </html>
      ```

- [ ] **Ajax如何实现、readyState五中状态的含义**

- [ ] **SRI策略**

## JavaScript

- [ ] **实现 compose 函数**
  compose函数接受一连串的函数作为参数，从最后的一个函数开始往前调用，将上一个函数的返回值作为下一个函数的输入

  ```javascript
  function compose(...funcs) {
    if (funcs.length === 0) { return (args) => args }
    if (funcs.length === 1) { return funcs[0] }

    const lastFunc = funcs.pop();
    const restFuncs = funcs;
    return function composed(...args) {
      return restFuncs.reduceRight(
        (prevFuncRes, currFunc) => currFunc(prevFuncRes),
        lastFunc(...args)
      );
    }
  }

  (function test() {
    function add1(num) { return num + 1 }
    function add2(num) { return num + 2 }
    function addBettwen(num1, num2) { return num1 + num2 }

    const composed = compose(
      add1,
      add2
    );
    console.log('No. 1 test ', composed(0));

    const composed2 = compose(
      add1,
      add2,
      addBettwen,
    );
    console.log('No. 2 test ', composed2(0, 2));
  })()
  ```

- [x] **闭包**
  - 定义：闭包是函数和声明该函数的词法环境的组合。
  - 闭包如何模拟私有方法
    ```javascript
    let Counter = (function() {
      let privateCounter = 0;
      function changeBy(val) {
        privateCounter += val;
      }
      return {
        increment() {
          changeBy(1);
        },
        decrement() {
          changeBy(-1);
        },
        value() {
          return privateCounter;
        }
      }   
    })();

    console.log(Counter.value()); /* logs 0 */
    Counter.increment();
    Counter.increment();
    console.log(Counter.value()); /* logs 2 */
    Counter.decrement();
    console.log(Counter.value()); /* logs 1 */
    ```
    我们只创建了一个词法环境，为三个函数所共享：`Counter.increment`，`Counter.decrement` 和 `Counter.value`

    ```javascript
    let makeCounter = function() {
      let privateCounter = 0;
      function changeBy(val) {
        privateCounter += val;
      }
      return {
        increment() {
          changeBy(1);
        },
        decrement() {
          changeBy(-1);
        },
        value() {
          return privateCounter;
        }
      }  
    };

    var Counter1 = makeCounter();
    var Counter2 = makeCounter();
    console.log(Counter1.value()); /* logs 0 */
    Counter1.increment();
    Counter1.increment();
    console.log(Counter1.value()); /* logs 2 */
    Counter1.decrement();
    console.log(Counter1.value()); /* logs 1 */
    console.log(Counter2.value()); /* logs 0 */
    ```
    请注意两个计数器 counter1 和 counter2 是如何维护它们各自的独立性的。每个闭包都是引用自己词法作用域内的变量 privateCounter 。

    每次调用其中一个计数器时，通过改变这个变量的值，会改变这个闭包的词法环境。然而在一个闭包内对变量的修改，不会影响到另外一个闭包中的变量。
  - 缺点：资源无法释放，要注意不能在循环中创建闭包，在循环中创建的闭包会共享一个词法环境

- [x] **运算符**

  - **赋值运算符**
    | name                                     | 简写       | 含义          |
    | :--------------------------------------- | :------- | :---------- |
    | 赋值(Assignment)                           | x = y    | x = y       |
    | 加法赋值(Addition assignment)                | x += y   | x = x + y   |
    | 减法赋值(Subtraction assignment)             | x -= y   | x = x - y   |
    | 乘法赋值(Multiplication assignment)          | x *= y   | x = x * y   |
    | 除法赋值(Division assignment)                | x /= y   | x = x / y   |
    | 求余赋值(Remainder assignment)               | x %= y   | x = x % y   |
    | 求幂赋值(Exponentiation assignment)          | x **= y  | x = x ** y  |
    | 左移位赋值(Left shift assignment)             | x <<= y  | x = x << y  |
    | 右移位赋值(Right shift assignment)            | x >>= y  | x = x >> y  |
    | 无符号右移位赋值(Unsigned right shift assignment) | x >>>= y | x = x >>> y |
    | 按位与赋值(Bitwise AND assignment)            | x &= y   | x = x & y   |
    | 按位异或赋值(Bitwise XOR assignment)           | x ^= y   | x = x ^ y   |
    | 按位或赋值(Bitwise OR assignment)             | x        | = y         |

  - **解构**
    对于更复杂的赋值，解构赋值语法是一个能从数组或对象对应的数组结构或对象字面量里提取数据的 Javascript 表达式。

    ```javascript
    var foo = ["one", "two", "three"];

    // 不使用解构
    var one   = foo[0];
    var two   = foo[1];
    var three = foo[2];

    // 使用解构
    var [one, two, three] = foo;
    ```

  - **算术运算符**
    算术运算符使用数值(字面量或者变量)作为操作数并返回一个数值.标准的算术运算符就是加减乘除(+ - * /)。当操作数是浮点数时，这些运算符表现得跟它们在大多数编程语言中一样（特殊要注意的是，除零会产生Infinity）。例如：

    ```javascript
    1 / 2;  // 0.5
    1 / 2 == 1.0 / 2.0; // true
    ```

    - 求余(`%`)
      二元运算符. 返回相除之后的余数.
      ```javascript
      12 % 5 // 2
      ```

    - 自增(`++`)
      一元运算符. 将操作数的值加一. 如果放在操作数前面 (`++x`), 则返回加一后的值; 如果放在操作数后面 (`x++`), 则返回操作数原值,然后再将操作数加一.
      ```javascript
      var x=3;

      console.log(++x); //4

      console.log(x); //4

      var y=3;

      console.log(y++); //3

      console.log(y); //4
      ```

    - 自减(`--`)
      一元运算符. 将操作数的值减一. 前后缀两种用法的返回值类似自增运算符.
      ```javascript
      var x=3; console.log(--x); //输入2,x=2
      var y=3; console.log(y--);//输出3,x=2;
      ```

    - 一元负值符(`-`)
      一元运算符,返回操作数的负值.
      ```javascript
      var x=3; console.log(-x); //输入-3
      ```

    - 一元正值符(`+`)
      一元运算符, 如果操作数在之前不是number，试图将其转换为number
      ```javascript
      console.log( +'3' ); // 3

      console.log( '3' ); // '3'

      console.log(+true); // 1
      ```

    - 指数运算符(`**`)
      计算 base(底数) 的 exponent(指数)次方, 表示为baseexponent
      ```javascript
      2 ** 3 // return 8.
      10 ** -1 // return 0.1.
      ```

  - **位运算符**
    位运算符将它的操作数视为32位元的二进制串（0和1组成）而非十进制八进制或十六进制数。例如：十进制数字9用二进制表示为1001，位运算符就是在这个二进制表示上执行运算，但是返回结果是标准的JavaScript数值。

    - 位逻辑运算符
      - 按位与 **AND**
        `a & b`
        在a,b的位表示中，每一个对应的位都为1则返回1， 否则返回0.
        ```javascript
        15 & 9 // 9
          ⇕
        1111 & 1001 = 1001 // 9
        ```

      - 按位或 **OR**
        `a | b`
        在a,b的位表示中，每一个对应的位，只要有一个为1则返回1， 否则返回0.
        ```javascript
        15 | 9 // 15
          ⇕
        1111 | 1001 // 1111
        ```

      - 按位异或 **XOR**
        `a ^ b`
        在a,b的位表示中，每一个对应的位，两个不相同则返回1，相同则返回0.
        ```javascript
        15 ^ 9 // 6
          ⇕
        1111 ^ 1001 // 0110
        ```

      - 按位非 **NOT**
        `~ a`
        反转被操作数的位。
        ```javascript
        ~15 // -16
        ⇕
        ~00000000...00001111 // 11111111...11110000

        ~9 // -10
        ⇕
        ~00000000...00001001 // 11111111...11110110
        ```
        > 注意位运算符“非 (`~`)”将所有的32位取反，
        > 而值的最高位(最左边的一位)为1则表示负数(2-补码表示法)。

    - 移位运算符
      移位运算符带两个操作数：第一个是待移位的数，第二个是指定第一个数要被移多少位的数。移位的方向由运算符来控制.

      - 左移 **shift**
        `a << b`
        将a的二进制串向左移动b位,右边移入0.
        > 9<<2产生36，因为1001移位2比特向左变为100100，它是36。

      - 带符号右位移
        `a >> b`
        把a的二进制表示向右移动b位，丢弃被移出的所有位.
        > 9>>2产生2，因为1001移位2位向右变为10，其是2。同样，-9>>2产生-3，由于符号被保留。
        >
        > (译注:算术右移左边空出的位是根据最高位是0和1来进行填充的)

      - 无符号右移 (左边空出位用0填充)
        `a >>> b`
        把a的二进制表示向右移动b位，丢弃被移出的所有位，并把左边空出的位都填充为0
        > 19>>>2产生4，因为10011移位2位向右变为100，它是4。
        > 对非负数值，补零右移和带符号右移产生相同结果。

  - **逻辑运算符**
    逻辑运算符常用于布尔（逻辑）值之间; 当操作数都是布尔值时，返回值也是布尔值。 不过实际上&&和||返回的是一个特定的操作数的值，所以当它用于非布尔值的时候，返回值就可能是非布尔值。

    - 逻辑与 (`&&`)
      `expr1 && expr2`
      如果expr1能被转换为false，那么返回expr1；否则，返回expr2。因此，`&&`用于布尔值时，当操作数都为true时返回true；否则返回false.
      ```javascript
      var a1 =  true && true;     // t && t returns true
      var a2 =  true && false;    // t && f returns false
      var a3 = false && true;     // f && t returns false
      var a4 = false && (3 == 4); // f && f returns false
      var a5 = "Cat" && "Dog";    // t && t returns Dog
      var a6 = false && "Cat";    // f && t returns false
      var a7 = "Cat" && false;    // t && f returns false
      ```

    - 逻辑或 (`||`)
      `expr1 || expr2`
      如果expr1能被转换为true，那么返回expr1；否则，返回expr2。因此，`||`用于布尔值时，当任何一个操作数为true则返回true；如果操作数都是false则返回false。
      ```javascript
      var o1 =  true || true;     // t || t returns true
      var o2 = false || true;     // f || t returns true
      var o3 =  true || false;    // t || f returns true
      var o4 = false || (3 == 4); // f || f returns false
      var o5 = "Cat" || "Dog";    // t || t returns Cat
      var o6 = false || "Cat";    // f || t returns Cat
      var o7 = "Cat" || false;    // t || f returns Cat
      ```

    - 逻辑非 (`!`)
      `!expr`
      如果操作数能够转换为true则返回false；否则返回true。
      ```javascript
      var n1 = !true;  // !t returns false
      var n2 = !false; // !f returns true
      var n3 = !"Cat"; // !t returns false
      ```

    - 短路求值
      作为逻辑表达式进行求值是从左到右，它们是为可能的“短路”的出现而使用以下规则进行测试：
      ```javascript
      false && anything    // 被短路求值为false
      true || anything     // 被短路求值为true
      ```
      逻辑的规则，保证这些评估是总是正确的。请注意，上述表达式的`anything`部分都不会进行评估。

  - **字符串运算符**
    除了比较操作符，它可以在字符串值中使用，连接操作符（+）连接两个字符串值相连接，返回另一个字符串，它是两个操作数串的结合。

    ```javascript
    console.log("my " + "string"); // console logs the string "my string".
    ```

    简写操作符 += 也可以用来拼接字符串，例如：

    ```javascript
    let myString = "alpha";

    myString += "bet"; // 返回 "alphabet"
    ```

  - **条件（三元）运算符**
    三元运算符是JavaScript中唯一需要三个操作数的运算符。
    运算的结果根据给定条件在两个值中取其一。语法为：
    ```
    条件 ? 值1 : 值2
    ```
    如果条件为真，则结果取值1。否则为值2。你能够在任何允许使用标准运算符的地方使用条件运算符。

  - **一元操作符**
    一元操作符仅对应一个操作数。

    - `delete`
      delete操作符，删除一个对象或一个对象的属性或者一个数组中某一个键值。语法如下:
      ```javascript
      delete objectName;
      delete objectName.property;
      delete objectName[index];
      ```

      如果 delete 操作成功，属性或者元素会变成 undefined。如果 `delete` 可行会返回true，如果不成功返回false。
      ```javascript
      x = 42;
      var y = 43;
      myobj = new Number();
      myobj.h = 4;    // create property h
      delete x;       // returns true (can delete if declared implicitly)
      delete y;       // returns false (cannot delete if declared with var)
      delete Math.PI; // returns false (cannot delete predefined properties)
      delete myobj.h; // returns true (can delete user-defined properties)
      delete myobj;   // returns true (can delete if declared implicitly)
      ```

      **删除数组元素**
      删除数组中的元素时，数组的长度是不变的，例如删除a[3], a[4]，a[4]和a[3] 仍然存在变成了undefined。

      delete 删除数组中的一个元素，这个元素就不在数组中了。例如，trees[3]被删除，trees[3] 仍然可寻址并返回undefined。

      如果想让数组中存在一个元素但是是undefined值，使用undefined关键字而不是delete操作. 如下： trees[3]分配一个undefined,但是这个数组元素仍然存在:
      ```javascript
      let trees = new Array("redwood", "bay", "cedar", "oak", "maple");
      trees[3] = undefined;
      if (3 in trees) {
        // this gets executed（会被执行）
      }
      ```

    - `typeof`
      typeof 操作符返回一个表示 operand 类型的字符串值。operand 可为字符串、变量、关键词或对象，其类型将被返回。
      **语法**：
      ```javascript
      typeof operand
      typeof (operand) // operand 两侧的括号为可选。
      ```

      some example
      ```javascript
      typeof true;          // returns "boolean"
      typeof null;          // returns "object" ***
      typeof 62;            // returns "number"
      typeof 'Hello world'; // returns "string"
      typeof parseInt;      // returns "function"
      // 对于方法和函数，typeof 操作符将会返回如下结果：
      typeof shape.split;   // returns "function"
      ```

    - `void`
      void运算符,表明一个运算没有返回值。expression是javaScript表达式，括号中的表达式是一个可选项，当然使用该方式是一种好的形式。
      ```javascript
      void (expression)
      void expression
      ```

  - **关系操作符**
    关系操作符对操作数进行比较，根据比较结果真或假，返回相应的布尔值

    - `in`
      in操作符，如果所指定的属性确实存在于所指定的对象中，则会返回true，语法如下：
      ```javascript
      propNameOrNumber in objectName
      arrayItem in array
      ```

    - `instanceof`
      如果所判别的对象确实是所指定的类型，则返回true。其语法如下：
      ```javascript
      // objectName 是需要做判别的对象的名称,而objectType是假定的对象的类型, 例如Date或 Array.
      objectName instanceof objectType
      ```

      当你需要确认一个对象在运行时的类型时，可使用instanceof. 例如，需要 catch 异常时，你可以针对抛出异常的类型，来做不同的异常处理。

    - 运算符优先级
      运算符的优先级，用于确定一个表达式的计算顺序。**在你不能确定优先级时，可以通过使用括号显式声明运算符的优先级。**

      从最高到最低:
      |          Operator type | Individual operators                     |
      | ---------------------: | :--------------------------------------- |
      |                 member | `.` `[]`                                 |
      | call / create instance | `()` `new`                               |
      |   negation / increment | `!` `~` `-` `+` `++` `--` `typeof` `void` `delete` |
      |      multiply / divide | `*` `/` `%`                              |
      | addition / subtraction | `+` `-`                                  |
      |          bitwise shift | `<<` `>>` `>>>`                          |
      |             relational | `<` `<=` `>` `>=` `in` `instanceof`      |
      |               equality | `==` `!=` `===` `!==`                    |
      |            bitwise-and | `&`                                      |
      |            bitwise-xor | `^`                                      |
      |             bitwise-or | `|`                                      |
      |            logical-and | `&&`                                     |
      |             logical-or | `||`                                     |
      |            conditional | `?:`                                     |
      |             assignment | `=` `+=` `-=` `*=` `/=` `%=` `<<=` `>>=` `>>>=` `&=` `^=` `|=` |
      |                  comma | `,`                                      |

- [x] **`this`**

  - 全局上下文
    无论是否在严格模式下，在全局执行上下文中（在任何函数体外部）this 都指代全局对象。

  - 函数上下文
    在函数内部，this的值取决于函数被调用的方式。

    - 简单调用
      在函数内部，this的值取决于函数被调用的方式。
      ```javaScript
      // 非严格模式
      function f1(){
        return this;
      }
      //在浏览器中：
      f1() === window;   //在浏览器中，全局对象是window

      //在Node中：
      f1() === global;

      // 严格模式
      function f2(){
        "use strict"; // 这里是严格模式
        return this;
      }

      f2() === undefined; // true
      ```
      在严格模式下，如果 this 没有被执行上下文（execution context）定义，那它将保持为 undefined。

      如果要想把 this 的值从一个上下文传到另一个，就要用 call 或者apply 方法。
      ```javascript
      // 一个对象可以作为call和apply的第一个参数，并且this会被绑定到这个对象。
      var obj = {a: 'Custom'};

      // 这个属性是在global对象定义的。
      var a = 'Global';

      function whatsThis(arg) {
        return this.a;  // this的值取决于函数的调用方式
      }

      whatsThis();          // 'Global'
      whatsThis.call(obj);  // 'Custom'
      whatsThis.apply(obj); // 'Custom'
      ```
      当一个函数在其主体中使用 `this` 关键字时，可以通过使用函数继承自 `Function.prototype` 的 `call` 或 `apply` 方法将 `this` 值绑定到调用中的特定对象。

    - bind方法
      ECMAScript 5 引入了 `Function.prototype.bind`。调用`f.bind(someObject)`会创建一个与f具有相同函数体和作用域的函数，但是在这个新函数中，`this`将永久地被绑定到了`bind`的第一个参数，无论这个函数是如何被调用的。

    - 箭头函数
      在箭头函数中，this与 **封闭词法上下文** 的this保持一致。在全局代码中，它将被设置为全局对象：
      ```javascript
      let globalObject = this;
      let foo = (() => this);
      console.log(foo() === globalObject); // true
      ```

      > **note** : 如果将this传递给call、bind、或者apply，它将被忽略。
      > 不过你仍然可以为调用添加参数，不过第一个参数（thisArg）应该设置为null。

      ```javascript
      // 接着上面的代码
      // 作为对象的一个方法调用
      var obj = {foo: foo};
      console.log(obj.foo() === globalObject); // true

      // 尝试使用call来设定this
      console.log(foo.call(obj) === globalObject); // true

      // 尝试使用bind来设定this
      foo = foo.bind(obj);
      console.log(foo() === globalObject); // true
      ```
      无论如何，`foo` 的 `this` 被设置为他被创建时的上下文（在上面的例子中，就是全局对象）。这同样适用于在其他函数内创建的箭头函数：这些箭头函数的`this`被设置为封闭的词法上下文的。

      ```javascript
      // 创建一个含有bar方法的obj对象，
      // bar返回一个函数，
      // 这个函数返回this，
      // 这个返回的函数是以箭头函数创建的，
      // 所以它的this被永久绑定到了它外层函数的this。
      // bar的值可以在调用中设置，这反过来又设置了返回函数的值。
      var obj = {
        bar: function() {
          var x = (() => this);
          return x;
        }
      };

      // 作为obj对象的一个方法来调用bar，把它的this绑定到obj。
      // 将返回的函数的引用赋值给fn。
      // 函数在被调用时确定this的指向
      var fn = obj.bar();

      // 直接调用fn而不设置this，
      // 通常(即不使用箭头函数的情况)默认为全局对象
      // 若在严格模式则为undefined
      console.log(fn() === obj); // true

      // 但是注意，如果你只是引用obj的方法，
      // 而没有调用它
      var fn2 = obj.bar;
      // 那么调用箭头函数后，this指向window，因为它从 bar 继承了this。
      console.log(fn2()() == window); // true
      ```
      在上面的例子中，一个赋值给了 ``obj.bar`` 的函数（称为匿名函数 A），返回了另一个箭头函数（称为匿名函数 B）。因此，在 A 调用时，函数B的this被永久设置为`obj.bar`（函数A）的`this`。当返回的函数（函数B）被调用时，它`this`始终是最初设置的。在上面的代码示例中，函数B的`this`被设置为函数A的`this`，即obj，所以即使被调用的方式通常将其设置为 undefined 或全局对象（或者如前面示例中的其他全局执行上下文中的方法），它的 `this` 也仍然是 obj 。

    - 作为对象的方法
      当函数作为对象里的方法被调用时，它们的 this 是调用该函数的对象。

      下面的例子中，当 `o.f()` 被调用时，函数内的`this`将绑定到`o`对象。
      ```javascript
      var o = {
        prop: 37,
        f: function() {
          return this.prop;
        }
      };

      console.log(o.f()); // logs 37
      ```

      **原型链中的 this**
      对于在对象原型链上某处定义的方法，同样的概念也适用。如果该方法存在于一个对象的原型链上，那么this指向的是调用这个方法的对象，就像该方法在对象上一样。
      ```javascript
      var o = {
        f: function() {
          return this.a + this.b;
        }
      };
      var p = Object.create(o);
      p.a = 1;
      p.b = 4;

      console.log(p.f()); // 5
      ```
      在这个例子中，对象p没有属于它自己的f属性，它的f属性继承自它的原型。虽然在对 f 的查找过程中，最终是在 o 中找到 f 属性的，这并没有关系；查找过程首先从 p.f 的引用开始，所以函数中的 this 指向p。也就是说，因为f是作为p的方法调用的，所以它的this指向了p。这是 JavaScript 的原型继承中的一个有趣的特性。

    - 作为构造函数
      当一个函数用作构造函数时（使用`new`关键字），它的`this`被绑定到正在构造的新对象。
      > **note** : 虽然构造器返回的默认值是`this`所指的那个对象，但它仍可以手动返回其他的对象（如果返回值不是一个对象，则返回`this`对象）。


    - 作为一个DOM事件处理函数
      当函数被用作事件处理函数时，它的`this`指向触发事件的元素（一些浏览器在使用非addEventListener的函数动态添加监听函数时不遵守这个约定）。
      ```javascript
      // 被调用时，将关联的元素变成蓝色
      function bluify(e){
        console.log(this === e.currentTarget); // 总是 true

        // 当 currentTarget 和 target 是同一个对象时为 true
        console.log(this === e.target);        
        this.style.backgroundColor = '#A5D9F3';
      }

      // 获取文档中的所有元素的列表
      var elements = document.getElementsByTagName('*');

      // 将bluify作为元素的点击监听函数，当元素被点击时，就会变成蓝色
      for(var i=0 ; i<elements.length ; i++){
        elements[i].addEventListener('click', bluify, false);
      }
      ```

    - 作为一个内联事件处理函数
      当代码被内联on-event 处理函数调用时，它的this指向监听器所在的DOM元素：
      ```html
      <button onclick="alert(this.tagName.toLowerCase());">
        Show this
      </button>
      ```
      上面的 alert 会显示button。注意只有外层代码中的this是这样设置的：

      ```html
      <button onclick="alert((function(){return this})());">
        Show inner this
      </button>
      ```
      在这种情况下，没有设置内部函数的this，所以它指向 global/window 对象（即非严格模式下调用的函数未设置this时指向的默认对象）。

- [x] **怎么样才算实现继承**
    修改一次就能影响多个对象。（变得更高效）

- [x] **JS - 继承**

  - 基于原型链的继承
    ```javascript
    function Parent () {
        this.name = 'kevin';
    }

    Parent.prototype.getName = function () {
        console.log(this.name);
    }

    function Child () {

    }

    Child.prototype = new Parent();
    Child.prototype.constructor = Child; // not necessary but useful

    var child1 = new Child();

    console.log(child1.getName()) // kevin
    ```

    - 缺点
      1. 引用类型的属性被所有实例共享，举个例子：
        ```javascript
        function Parent () {
          this.names = ['kevin', 'daisy'];
        }

        function Child () {

        }

        Child.prototype = new Parent();

        var child1 = new Child();

        child1.names.push('yayu');

        console.log(child1.names); // ["kevin", "daisy", "yayu"]

        var child2 = new Child();

        console.log(child2.names); // ["kevin", "daisy", "yayu"]
        ```
      2. 在创建 Child 的实例时，不能向Parent传参

  - 借用构造函数(经典继承)
    ```javascript
    function Parent () {
        this.names = ['kevin', 'daisy'];
    }

    function Child () {
        Parent.call(this);
    }

    var child1 = new Child();

    child1.names.push('yayu');

    console.log(child1.names); // ["kevin", "daisy", "yayu"]

    var child2 = new Child();

    console.log(child2.names); // ["kevin", "daisy"]
    ```
    优点：
    1. 避免了引用类型的属性被所有实例共享
    2. 可以在 `Child` 中向 `Parent` 传参
      ```javascript
      function Parent (name) {
        this.name = name;
      }

      function Child (name) {
        Parent.call(this, name);
      }

      var child1 = new Child('kevin');

      console.log(child1.name); // kevin

      var child2 = new Child('daisy');

      console.log(child2.name); // daisy
      ```
    缺点：
    方法都在构造函数中定义，每次创建实例都会创建一遍方法。

  - 组合继承
    原型链继承和经典继承双剑合璧。
    ```javascript
    function Parent (name) {
      this.name = name;
      this.colors = ['red', 'blue', 'green'];
    }

    Parent.prototype.getName = function () {
      console.log(this.name)
    }

    function Child (name, age) {
      Parent.call(this, name);
      this.age = age;
    }

    Child.prototype = new Parent();
    Child.prototype.constructor = Child;

    var child1 = new Child('kevin', '18');

    child1.colors.push('black');

    console.log(child1.name); // kevin
    console.log(child1.age); // 18
    console.log(child1.colors); // ["red", "blue", "green", "black"]

    var child2 = new Child('daisy', '20');

    console.log(child2.name); // daisy
    console.log(child2.age); // 20
    console.log(child2.colors); // ["red", "blue", "green"]
    ```
    解决了原型链继承和构造函数继承的缺点

  - 原型式继承
    ```javascript
    function createObj(o) {
      function F(){}
      F.prototype = o;
      return new F();
    }
    ```
    就是 ES5 Object.create 的模拟实现，将传入的对象作为创建的对象的原型。

    缺点：
    包含引用类型的属性值始终都会共享相应的值，这点跟原型链继承一样。
    ```javascript
    var person = {
      name: 'kevin',
      friends: ['daisy', 'kelly'],
    }

    var person1 = createObj(person);
    var person2 = createObj(person);

    person1.name = 'person1';
    console.log(person2.name); // kevin

    person1.firends.push('taylor');
    console.log(person2.friends); // ["daisy", "kelly", "taylor"]
    ```
    > **note**: 修改person1.name的值，person2.name的值并未发生改变，并不是因为person1和person2有独立的 name 值，而是因为person1.name = 'person1'，给person1添加了 name 值，并非修改了原型上的 name 值。

  - 寄生式继承
    创建一个仅用于封装继承过程的函数，该函数在内部以某种形式来做增强对象，最后返回对象。
    ```javascript
    function createObj (o) {
      var clone = object.create(o);
      clone.sayName = function () {
        console.log('hi');
      }
      return clone;
    }
    ```
    缺点：跟借用构造函数模式一样，每次创建对象都会创建一遍方法。

  - 寄生组合式继承
    组合继承的代码：
    ```javascript
    function Parent (name) {
      this.name = name;
      this.colors = ['red', 'blue', 'green'];
    }

    Parent.prototype.getName = function () {
      console.log(this.name);
    }

    function Child (name, age) {
      Parent.call(this, name);
      this.age = age;
    }

    Child.prototype = new Parent();
    Child.prototype.constructor = Child;

    var child1 = new Child('kevin', '18');

    console.log(child1);
    ```
    组合继承最大的缺点是会调用两次父构造函数。

    一次是设置子类型实例的原型的时候：
    ```javascript
    Child.prototype = new Parent();
    ```

    一次在创建子类型实例的时候：
    ```javascript
    var child1 = new Child('kevin', '18');
    ```
    `new Child()` -> `Parent.call(this)`
    在这个例子中，如果我们打印 child1 对象，我们会发现 Child.prototype 和 child1 都有一个属性为colors，属性值为['red', 'blue', 'green']。

    那么我们该如何精益求精，避免这一次重复调用呢？

    如果我们不使用 `Child.prototype = new Parent()` ，而是间接的让 `Child.prototype` 访问到 `Parent.prototype` 呢？

    看看如何实现：

    ```Javascript
    function Parent (name) {
      this.name = name;
      this.colors = ['red', 'blue', 'green'];
    }

    Parent.prototype.getName = function () {
      console.log(this.name)
    }

    function Child (name, age) {
      Parent.call(this, name);
      this.age = age;
    }

    // 关键的三步
    var F = function () {};

    F.prototype = Parent.prototype;

    Child.prototype = new F();

    var child1 = new Child('kevin', '18');

    console.log(child1);
    ​```

    最后我们封装一下这个继承方法：
    ​```javascript
    function object(o) {
        function F() {}
        F.prototype = o;
        return new F();
    }

    function prototype(child, parent) {
        var prototype = object(parent.prototype);
        prototype.constructor = child;
        child.prototype = prototype;
    }

    // 当我们使用的时候：
    prototype(Child, Parent);

    // 简短版本
    function myPrototype(child, parent) {
      var prototype = Object.create(parent.prototype);
      prototype.constructor = child;
      child.prototype = prototype;
    }
    ​```

- [x] **VO & AO & Scope chain & this**
    [variable object](https://github.com/mqyqingfeng/Blog/issues/5)
    [scope chain](https://github.com/mqyqingfeng/Blog/issues/6)

- [x] **实现 setIntervial**
  需要注意参数的传递
  ```javascript
  function interval(func, wt){
    var args = Array.prototype.slice.call(arguments, 2);
    var interv = function (args) {
      setTimeout(interv, wt, args);
      try {
        func.apply(null, args);
      }
      catch (e) { throw e.toString(); }
    };

    setTimeout(interv, wt, args);
  };
  ```

- [x] `Event Loop`

  提炼：[Tasks, microtasks, queues and schedules](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)

  - **definitions**
    为了协调事件，用户交互，脚本，渲染，网络等，用户代理必须使用本节所述的`event loop`。有两种`event loop`：浏览上下文(browsing contexts)和工作者循环(workers)。
    每个UA都必须至少有一个浏览上下文事件循环，并且每个同源浏览上下文至多只能有一个`event loop`。
    一个 browsing context event loop 总是至少有一个 browsing context，如果这样的一个 event loop 的 browsing context 消失，那么event loop也会消失，一个browsing context总是会有一个 event loop 来协调活动。
    web worker 的 event loop 更加简单，每个 worker 都有一个 event loop 并且 worker 处理模型会管理 event loop 的生命周期

    一个 event loop 有一个或多个 task queues，任务队列是一个有序的任务列表(an ordered list of **tasks**)，它负责的方面有：

    - events

      在特定的EventTarget对象上分派(dispatch)Event对象通常由专门的任务完成。

      > 并非所有事件都使用task queues分派，许多事件都会在其他任务中分派。

    - Parsing

      获取一个或多个字节并处理产生的结果的HTML解析器通常是一个task

    - Callbacks

      调用回调函数通常由专门的task完成

    - Using a resource

      当算法获取资源时，如果以非阻塞方式进行提取，那么一旦某个或全部资源可用，对资源的处理由任务执行。

    - Reacting to DOM manipulation(对DOM操作作出反应)

      某些元素具有响应DOM操作而触发的任务，e.g. 当该元素插入到文档中时。

    task 是指向某个事件循环的：处理与Document相关的task的事件循环或者是和worker有关的循环。

    当UA在将一个task入队时，它必须将给定的task加入到有关的一个task queue中。

    ​

    每一个task都被定义为来自特别的任务源。所有来自一个特定任务源的并且指向特定事件循环的任务(例如：由Document的计时器生成的回调，文档上的鼠标移动而触发的事件，排队等待文档解析器的解析任务)必须始终添加到相同的任务队列中，但来自不同任务源的任务可能被放置在不同的任务队列中。

    > **e.g.**
    >
    > 一个用户代理可以有一个用于鼠标和键盘事件的任务队列（用户交互任务源），另一个用于其他任务。然后，用户代理可以在大部分时间内使键盘和鼠标事件优先于其他任务，保持界面响应但不会不顾及其他任务队列，并且从不处理来自任何一个无序任务源的事件。

    每个事件循环都有一个 *当前运行的任务(currently running task)* 。最初，这是空的。它用于处理重入。每个事件循环还有一个执行微任务检查点标志，该标志最初必须是假的。它用于防止执行微任务检查点算法的重入调用。每个事件循环还有一个 *执行微任务检查点(performing a microtask checkpoint)* 标志，该标志最初必须是false。它用于防止执行微任务检查点算法的重入调用。

  - **Processing model**

    一个事件循环在其存在时会不停执行以下动作：

    - 让 oldestTask 成为事件循环任务队列中最老的一个task(如果有的话)，在浏览上下文事件循环中，忽略关联文档不完全活动的任务。

    - 设置事件循环的当前 *正在运行的任务(currently running task)* 为 oldestTask

    - 运行 oldestTask

    - 设置事件循环的 currently running task = null

    - 将任务队列中的 oldestTask 移除

    - Microtasks：执行一个微任务检查点(perform a microtask checkpoint)。

    -  **Updating the rendering**: 如果事件循环是 *浏览上下文事件循环* (不同于 worker 上下文)，则运行如下步骤：

      1. 令 *now* 值为 performance.now() *(now = performance.now())*

      2. 令 *docs* 值为与所讨论的事件循环相关联的Document对象列表，除了必须满足以下条件外，还可以任意排序：

         1. 任何通过文档A嵌套的文档B必须列在列表中的A之后。
         2. 如果存在两个文档A和B，其浏览上下文都是嵌套浏览上下文，并且它们的浏览上下文容器都是同一文档C中的元素，则列表中的A和B的顺序必须匹配它们各自浏览的相对树顺序上下文容器在C.

         在迭代文档的以下步骤中，必须按照在列表中找到的顺序处理每个文档。

      3. 如果有顶级浏览上下文B，UA认为此时不会从其渲染更新中受益，则从文档中移除其浏览上下文的顶级浏览上下文位于B中的所有Document对象。

         > 顶层浏览上下文是否会因更新渲染而受益取决于各种因素，例如更新频率。例如，如果浏览器试图达到60Hz的刷新率，那么这些步骤只需要每60秒（约16.7ms）。如果浏览器发现顶级浏览上下文无法维持此速率，那么它可能会下降到该组文档的更可持续的30Hz，而不是偶尔丢失帧。 （这个规范并没有规定何时更新渲染的任何特定模型。）同样，如果顶级浏览上下文在后台，用户代理可能会决定将该页面放慢到4Hz甚至更低。
         >
         > 浏览器可能会跳过更新渲染的另一个例子是确保某些任务紧接着一个执行，只有微任务检查点交错（并且没有例如 *动画帧回调(animation frame callback)* 交错）。例如，用户代理可能希望将计时器回调合并在一起，而不需要中间渲染更新。

      4. 如果UA认为嵌套的浏览上下文B不会从此时更新渲染中受益，则从文档中移除其浏览上下文位于B中的所有Document对象。

         > 与顶级浏览上下文一样，各种因素都会影响浏览器是否有利于更新嵌套浏览上下文的呈现。例如，UA可能希望花费较少的资源来呈现第三方内容，尤其是当用户当前不可见或资源受限时。在这种情况下，浏览器可能决定不经常更新此类内容的呈现或从不更新。

      5. 对于 *docs* 中的每个完全活动的文档，运行该文档的  *调整大小(resize)* 步骤， *now* 作为时间戳传入。

      6. 对于 *docs* 中的每个完全活动的文档，运行该文档的  滚动*(scroll)* 步骤， *now* 作为时间戳传入。

      7. 对于 *docs* 中的每个完全活动的文档，运行该文档的 *评估媒体查询和报告更改(evaluate media queries and report changes)* 步骤， *now* 作为时间戳传入。

      8. 对于 *docs* 中的每个完全活动的文档，运行该文档的 *动画更新(animation update)* *发射事件(send event)*  步骤， *now* 作为时间戳传入。

      9. 对于 *docs* 中的每个完全活动的文档，运行该文档的 *全屏*(fullscreen) 步骤， *now* 作为时间戳传入。

      10. 对于 *docs* 中的每个完全活动的文档，运行该文档的 *动画帧回调(animation frame callback)* 步骤， *now* 作为时间戳传入。

      11. 对于文档中的每个完全活动的Document，更新该Document的呈现或用户界面及其浏览上下文以反映当前状态。

      12. 对于 *docs* 中的每个完全活动的文档，运行该文档的 *更新交点观察(update intersection observation)* 步骤， *now* 作为时间戳传入。

    - 如果是 worker 事件循环(即运行在WorkerGlobalScope上)，但事件循环的任务队列中没有任务，并且WorkerGlobalScope对象的 `closing` 标志为 `true`，然后销毁事件循环，中止这些步骤，恢复运行下面Web工作部分中描述的辅助工步骤。

    每个事件循环都有一个微任务*(microtask)*队列。微任务是最初在微任务队列中排队而不是任务队列的任务。有两种微任务： *独立回调微任务(solitary callback microtasks)* 和复合微任务。

    > 此规范仅具有独立回调微任务。使用复合微任务的规范必须格外小心，以包装回调来处理事件循环

    当算法要求微任务需要入队时，它必须附加到相关事件循环的微任务队列;这种微任务的任务源是 *微任务任务源(microtask task source)* 。

    > 如果在初始化执行过程中 [转换事件循环 spins the event loop](https://html.spec.whatwg.org/multipage/webappapis.html#spin-the-event-loop) ，则将微任务移动到常规任务队列中是可能的。

    当用户代理执行微任务检查点时，如果执行微任务检查点标记为false，则用户代理必须运行以下步骤：

    - 将执行microtask检查点标志设置为true。

    - 当事件循环的microtask队列不为空时：

      1. 让最老的微任务(oldestMicrotask)成为事件循环微任务队列中最早的微任务。

      2. 将事件循环的当前正在运行的任务设置为oldestMicrotask。

      3. Run oldestMicrotask.

         > 这可能涉及调用脚本回调，并最终会在运行脚本步骤之后调用清理，然后再次执行微任务检查点算法，这就是为什么我们使用执行微任务检查点标志来避免重入的原因

      4. 将事件循环当前运行的任务设置为空。

      5. 从microtask队列中删除 oldestMicrotask。

    - 对于其负责任的事件循环为此事件循环的每个环境设置对象，通知有关该环境设置对象上被拒绝的承诺([notify about rejected promises](https://html.spec.whatwg.org/multipage/webappapis.html#notify-about-rejected-promises))

    - 清理indexed数据库事务。

    - 将执行microtask检查点标志设置为false。

    如果在复合微任务运行时，用户代理需要执行复合微任务子任务以运行一系列步骤，则用户代理必须执行以下步骤：

    - 让 *父元素(parent)* 成为事件循环当前正在运行的任务（当前正在运行的复合微任务）。
    - 让 子任务(subtask) 成为运行给定的一系列步骤的新任务。这种微任务的任务源是微任务任务源。这是一个复合微任务子任务(compound microtask subtask)。
    - 将事件循环的当前正在运行的任务设置为子任务。
    - Run subtask.
    - 将事件循环的当前运行任务设置回parent。

    未完。。。

    ​

- [ ] **分页器原理**

- [x] **实现`bind`函数**
  先实现 `返回函数` 的功能
  ```javascript
  Function.prototype.myBind = function (contexg) {
    var self = this;
    return function () {
      return self.apply(context);
    }
  }
  ```

  实现 `传参`
  ```javascript
  Function.prototype.myBind = function (context) {
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);
    return function () {
      // 这个时候的arguments是指bind返回的函数传入的参数
      var bindArgs = Array.prototype.slice.call(arguments);
      return self.apply(context, args.concat(bindArgs));
    }
  }

  // es6
  Function.prototype.myBind = function (context, ...args) {
    let self = this;
    return function (...bindArgs) {
      // 这个时候的bindArgs是指传入bind返回的函数的参数
      return self.apply(context, [...args, ...bindArgs]);
    }
  }
  ```

  兼容构造函数！
  new 操作符会修改this指向，导致bind之后的函数中的this指向创建的对象
  example:
  ```javaScript
  var value = 2;

  var foo = {
      value: 1
  };

  function bar(name, age) {
      this.habit = 'shopping';
      console.log(this.value);
      console.log(name);
      console.log(age);
  }

  bar.prototype.friend = 'kevin';

  var bindFoo = bar.bind(foo, 'daisy');

  var obj = new bindFoo('18');
  // undefined
  // daisy
  // 18
  console.log(obj.habit);
  console.log(obj.friend);
  // shopping
  // kevin
  ```
  实现：
  ```javascript
  Function.prototype.myBind = function (context) {
    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);

    var fBound = function () {
      var bindArgs = Array.prototype.slice.call(arguments);
      return self.apply(this instanceof fBound ? this : context, args.concat(bindArgs));
    };

    fBound.prototype = Object.create(this.prototype);
    return fBound;
  }

  // es6
  Function.prototype.myBind = function (context, ...args) {
    let self = this;

    let fBound = function (...bindArgs) {
      return self.apply(
        this instanceof fBound ? this : context, [...args, ...bindArgs]
      );
    }
    fBound.prototype = Object.create(this.prototype);

    return fBound;
  }
  ```

- [x] **prototype和__proto__的关系是什么**
  \_\_proto\_\_（隐式原型）与prototype（显式原型）
  每一个函数在创建之后都会拥有一个名为prototype的属性，这个属性指向函数的原型对象。
  **Note**：通过Function.prototype.bind方法构造出来的函数是个例外，它没有prototype属性。同学的答案让我知道这一点）

  显式原型的作用：用来实现基于原型的继承与属性的共享。

  JavaScript中任意对象都有一个内置属性`[[prototype]]`，在ES5之前没有标准的方法访问这个内置属性，但是大多数浏览器都支持通过\_\_proto\_\_来访问。ES5中有了对于这个内置属性标准的Get方法 `Object.getPrototypeOf()`
  **Note**: `Object.prototype` 这个对象是个例外，它的`__proto__`值为null

  隐式原型的作用：构成原型链，同样用于实现基于原型的继承。举个例子，当我们访问obj这个对象中的x属性时，如果在obj中找不到，那么就会沿着`__proto__`依次查找。

  ![](https://pic1.zhimg.com/80/e83bca5f1d1e6bf359d1f75727968c11_hd.jpg)
  总结：
  1. 对象有属性`__proto__`,指向该对象的构造函数的原型对象。
  2. 方法除了有属性`__proto__`,还有属性`prototype`，`prototype`指向该方法的原型对象。

- [x] **instanceof 原理**
  工作原理
  设 L instanceof R
  通过判断
  `L.__proto__.__proto__ ..... === R.prototype ？`
  最终返回true or false

## Data Structures and Algorithms

- [ ] 快速排序

- [ ] 冒泡排序

- [ ] 动态规划

## HTML & CSS

- [x] **Doctype是什么**
    Document Type Declaration
    <!DOCTYPE> 告知 浏览器 当前的 HTML (或 XML) 文档是哪一个版本. Doctype 是一条 声明, 而不是一个 标签; 也可以把它叫做 "文档类型声明", 或 简称为 "DTD".

  目前浏览器的排版引擎有三种模式：怪异模式（Quirks mode）、接近标准模式（Almost standards mode）、以及标准模式（Standards mode）。在怪异模式下，排版会模拟 Navigator 4 与 Internet Explorer 5 的非标准行为。为了支持在网络标准被广泛采用前，就已经建好的网站，这么做是必要的。在标准模式下，行为即（但愿如此）由 HTML 与 CSS 的规范描述的行为。在接近标准模式下，只有少数的怪异行为被实现。

  - 浏览器如何决定用哪个模式？
    任何放在 DOCTYPE 前面的东西，比如批注或 XML 声明，会令 Internet Explorer 9 或更早期的浏览器触发怪异模式。
    ```
    <!DOCTYPE html>
    ```
    使用标准模式

    在 HTML5中，DOCTYPE 唯一的作用是启用标准模式。更早期的 HTML 标准会附加其他意义，但没有任何浏览器会将 DOCTYPE 用于怪异模式和标准模式之间互换以外的用途。

- [x] **HTML与XHTML的区别**
  简单来说，XHTML比HTML更严格, XHTML遇到一个错误浏览器就会停止解析
  strict html 4.01 标准
  `<html>` 必须是root元素
  `<head>` 和 `<body>` 是 `<html>` 中一定有且只有的元素
  `<head>` 必须有 `<title>`，`<meta>` 和 `<style>` 可选, 他们只能在 `<head>` 里
  `<body>` 里只能有 block元素
  block元素不能放在 inline元素里
  block元素不能放在 `<p>` 里
  `<ul>`和`<ol>`中只能有`<li>`元素,但`<li>`里可以放其他，包括block元素
  `<blockquote>`中只能放block元素

  xhtml 1.0 标准

  html元素需要有xml相关属性
  元素名必须是小写字母
  元素属性用"包围,不能为空值
  在内容里不能有`&`, 需要转义，包括其他特殊字符`<>`
  空元素以 `/>` 结尾

  - 详解
    可扩展超文本标记语言XHTML（eXtensible HyperText Markup Language），是HTML 4.01的第一个修订版本，是「3种HTML4文件根据XML1.0标准重组」而成的。也就是说是，XHTML是HTML 4.01和XML1.0的杂交。
    由于XHTML1.0是基于HTML4.01的，并没有引入任何新标签或属性（XHTML可以看作是HTML的一个子集），表现方式与超文本标记语言HTML类似，只是语法上更加严格，几乎所有的网页浏览器在正确解析HTML的同时，可兼容XHTML。

    TML是一种基于标准通用标记语言（SGML）的应用，而XHTML则基于可扩展标记语言（XML），HTML和XHTML其实是平行发展的两个标准。本质上说，XHTML是一个过渡技术，结合了部分XML的强大功能及大多数HTML的简单特性。建立XHTML的目的就是实现HTML向XML的过渡。

    由于原本XHTML只是在内容结构上改进原有的HTML系统，XHTML2.0也仅仅在XHTML1.1的基础上更加注重页面规范和可用性，缺乏交互性。在这个Web App大行其道的年代，XHTML2有些OUT了，于是就催生了HTML5。

    HTML5目标是取代1999年所制定的HTML4.01和XHTML1.0标准，旨在提高网页性能，增加页面交互。HTML5吸取了XHTML2一些建议，包括一些用来改善文档结构的功能，比如，新的HTML标签header、footer、dialog、aside、figure等的使用，将使内容创作者更加语义地创建文档，之前的开发者在这些场合是一律使用div的。

    2011年，Google工程师兼HTML5标准编辑的Ian Hickson称，HTML5将是最后一个带版本号的HTML语言。他表示，HTML语言将成为一个活的标准。

- [x] **块级元素和行内元素，怎么让块级元素不换行**
  - 使用 display： inline-block
  - 使用 float: left | right

- [x] **Display有哪些属性，inline-block是什么**

  - CSS 1
    - none
      关闭一个元素的显示（对布局没有影响）；其所有后代元素都也被会被关闭显示。文档渲染时，该元素如同不存在。
      > 如果想要保留空间需要使用visibility
      > `visibility: hidden` 隐藏元素，并将其所占空间用空白占位

    - inline
      该元素生成一个或多个行内元素盒。一个行内元素只占据它对应标签的边框所包含的空间。
      一般情况下，行内元素只能包含数据和其他行内元素。
      默认情况下，行内元素不会以新行开始，而块级元素会新起一行。
      **不能设置 `width` & `height`**

    - block
      该元素生成一个块元素盒。
      块级元素占据其父元素（容器）的整个空间，因此创建了一个“块”。
      通常浏览器会在块级元素前后另起一个新行。
      **块级元素只能出现在 <body> 元素内。**
      一般块级元素可以包含行内元素和其他块级元素。这种结构上的包含继承区别可以使块级元素创建比行内元素更”大型“的结构。
      **可以设置 `width` & `height`**

    - list-item
      该元素生成一个容纳内容和单独的列表行内元素盒的块状盒。

  - CSS 2.1

    - inline-block
      该元素生成一个块状盒，该块状盒随着周围内容流动，如同它是一个单独的行内盒子（表现更像是一个被替换的元素）

  - CSS 3

    - flex
      该元素的行为类似于块元素，并根据flexbox模型展示其内容。

    - inline-flex
      该元素的行为与行内元素相似，并根据flexbox模型布局其内容。

- [x] **flex 布局**
  Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。
  任何一个容器都可以指定为 Flex 布局。

  ```css
  .box{
    display: flex;
  }
  ```

  行内元素也可以使用 Flex 布局。

  ```css
  .box{
    display: inline-flex;
  }
  ```

  **note**
  设为 Flex 布局以后，子元素的float、clear和vertical-align属性将失效。

  - 基本概念
    ![](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071004.png)
    容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；交叉轴的开始位置叫做cross start，结束位置叫做cross end.

    项目默认沿主轴排列。单个项目占据的主轴空间叫做main size，占据的交叉轴空间叫做cross size。

  - 容器属性

    - `flex-direction`
      决定主轴的方向（即项目的排列方向）
      row（默认值）：主轴为水平方向，起点在左端。
      row-reverse：主轴为水平方向，起点在右端。
      column：主轴为垂直方向，起点在上沿。
      column-reverse：主轴为垂直方向，起点在下沿。

    - `flex-wrap`
      nowrap（默认）：不换行。
      wrap：换行，第一行在上方。
      wrap-reverse：换行，第一行在下方。

    - `flex-flow`
      flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。
      ```
      flex-flow: <flex-direction> || <flex-wrap>;
      ```

    - `justify-content`
      `justify-content`属性定义了项目在主轴上的对齐方式。

      - flex-start（默认值）：左对齐
      - flex-end：右对齐
      - center： 居中
      - space-between：两端对齐，项目之间的间隔都相等。
      - space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

    - `align-items`
      `align-items`属性定义项目在交叉轴上如何对齐。

      - flex-start：交叉轴的起点对齐。
      - flex-end：交叉轴的终点对齐。
      - center：交叉轴的中点对齐。
      - baseline: 项目的第一行文字的基线对齐。
      - stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

    - `align-content`
      `align-content`属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

      - flex-start：与交叉轴的起点对齐。
      - flex-end：与交叉轴的终点对齐。
      - center：与交叉轴的中点对齐。
      - space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
      - space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
      - stretch（默认值）：轴线占满整个交叉轴。

  - 项目属性

    - `order`
      `order`属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
      ```
      order: <integer>;
      ```
      ![](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071013.png)

    - `flex-grow`
      `flex-grow`属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
      ```
      flex-grow: <number>; /* default 0 */
      ```
      ![](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071014.png)
      如果所有项目的`flex-grow`属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

    - `flex-shrink`
      `flex-shrink`属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
      ```
      flex-shrink: <number>; /* default 1 */
      ```
      ![](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071015.jpg)
      如果所有项目的`flex-shrink`属性都为1，当空间不足时，都将等比例缩小。如果一个项目的`flex-shrink`属性为0，其他项目都为1，则空间不足时，前者不缩小。

      负值对该属性无效。

    - `flex-basis`
      `flex-basis`属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。
      ```
      flex-basis: <length> | auto; /* default auto */
      ```
      它可以设为跟`width`或`height`属性一样的值（比如350px），则项目将占据固定空间。

    - `flex`
      `flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。
      ```
      flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
      ```
      该属性有两个快捷值：`auto` (`1 1 auto`) 和 `none` (`0 0 auto`)。

      建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。

    - `align-self`
      `align-self`属性允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。
      ```
      align-self: auto | flex-start | flex-end | center | baseline | stretch;
      ```
      ![](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071016.png)
      该属性可能取6个值，除了auto，其他都与align-items属性完全一致。

- [x] **`<script>` 标签详解**
  script标签用来在网页中执行JavaScript，它可以直接包含JavaScript代码，也可以直接通过src指向一个同域或者不同域的外链。

  - script标签默认会阻塞页面解析，并按照它们出现的顺序执行
    script.src 如果指向外链，下载是并行进行的，但是执行是同步的

  - script标签的 `async` 和 `defer` 属性
    - `async` 仅适用于外链，规定脚本异步执行
      - 下载不会阻塞页面解析
      - 不会按照出现的顺序执行，先下载完成哪个就先执行哪个
      - 执行的时候，有可能页面还没解析完成

    - `defer` 仅适用于外链，规定脚本延迟执行
      - 不会阻塞页面解析
      - 在html解析完成后, DOMContentLoaded之前执行
      - 会按照出现的顺序执行

    - type为module的script标签
      相比传统script，`<script type="module"></script>`将被当作一个JavaScript模块对待，被称为`module script`，且不受`charset`和`defer`属性影响。
      ```javascript
      <script type="module" src="app.js"></script>
      <script nomodule src="classic-app-bundle.js"></script>
      ```
      1. 支持module script的浏览器，不会执行拥有nomodule属性的script
      2. 不支持module script的浏览器，会忽略未知的type="module"的script，同时也会忽略传统script中不认识的nomodule属性，进而执行传统的bundle.js代码
      3. module script以及其依赖所有文件（源文件中通过import声明导入的文件）都会被下载，一旦整个依赖的模块树都被导入，页面文档也完成解析，app.js将会被执行

      但是如果module script里有`async`属性，比如`<script type="module" src="util.js" async></script>`，module script及其所有依赖都会异步下载，待整个依赖的模块树都被导入时会立即执行，而此时页面有可能还没有完成解析渲染。

      传统script和module script如何被下载执行可以用下图来概括：
      ![](https://camo.githubusercontent.com/949ada5c1c7e5da192528c23cfb22698394c6d88/68747470733a2f2f68746d6c2e737065632e7768617477672e6f72672f696d616765732f6173796e6364656665722e737667)

  - script标签的`integrity`属性
    ```javascript
    <script crossorigin="anonymous" integrity="sha256-PJJrxrJLzT6CCz1jDfQXTRWOO9zmemDQbmLtSlFQluc=" src="https://assets-cdn.github.com/assets/frameworks-3c926bc6b24bcd3e820b3d630df4174d158e3bdce67a60d06e62ed4a515096e7.js"></script>
    ```

    integrity属性是资源完整性规范的一部分，它允许你为script提供一个hash，用来进行验签，检验加载的JavaScript文件是否完整。

    作用：
    - 减少由【托管在CDN的资源被篡改】而引入的XSS 风险
    - 减少通信过程资源被篡改而引入的XSS风险（同时使用https会更保险）
    - 可以通过一些技术手段，不执行有脏数据的CDN资源，同时去源站下载对应资源
    > note: 注意：启用 SRI 策略后，浏览器会对资源进行 CORS 校验，这就要求被请求的资源必须同域，或者配置了 Access-Control-Allow-Origin 响应头

  - script标签的`crossorigin`属性
    `crossorigin`的属性值可以是`anonymous`、`use-credentials`，如果没有属性值或者非法属性值，会被浏览器默认做`anonymous`。`crossorigin`的作用有三个：
    - crossorigin会让浏览器启用CORS访问检查，检查http相应头的Access-Control-Allow-Origin
    - 对于传统script需要跨域获取的js资源，控制暴露出其报错的详细信息
    - 对于module script，控制用于跨域请求的[凭据模式](https://fetch.spec.whatwg.org/#concept-request-credentials-mode)

    我们在收集错误日志的时候，通常会在`window`上注册一个方法来监测所有代码抛出的异常：

    ```javascript
    window.addEventListener('error', function(msg, url, lineno, colno, error) {
      var string = msg.toLowerCase();
      var substring = "script error";
      if (string.indexOf(substring) > -1){
        alert('Script Error: See Browser Console for Detail');
      } else {
        var message = {
          Message: msg,
          URL:  url,
          Line: lineNo,
          Column: columnNo,
          'Error object': JSON.stringify(error)
        };
        // send error log to server
        record(message);
      }
      return false;
    })
    ```
    但是对于跨域js来说，只会给出很少的报错信息：'error: script error'，通过使用`crossorigin`属性可以使跨域js暴露出跟同域js同样的报错信息。但是，资源服务器必须返回一个`Access-Control-Allow-Origin`的header，否则资源无法访问。

  - 动态导入script(Dynamically importing scripts)
    ```javascript
    function loadError (error) {
      throw new URIError(`The script ${error.target.src}  is not accessible.`);
    }

    function importScript (src, onLoad) {
      var script = document.createElement('script');
      script.onerror = loadError;
      // 此函数动态加载js资源，但是要注意的是，默认append到文档中的script会异步执行
      // (可以理解为默认拥有async属性，如果需要加载的js按顺序执行，需要设置async为false)
      script.async = false;
      if (onLoad) { script.onload = onLoad; }
      document.header.appendChild(script);
      script.src = src;
    }
    ```

  - script标签的onerror

    1. JavaScript运行时的错误（抛出的语法错误和异常）发生时，实现了`ErrorEvent`接口的`error`事件在`window`上触发，并且调用`window.onerror`(或者`window.addEventListener('error, cb)`)的回调函数

    2. 当资源（如 `<img>` 或 `<script>`）无法加载，或者启用SRI策略资源不完整时，使用`Event`接口的`error`事件在会在该资源元素处触发，元素上的`onerror`回调函数被调用

  - script标签与innerHTML
    ```javascript
    document.head.innerHTML += `<script>alert('不会执行')</script>`
    ```

- [x] **`div` & `span` & `label` 的区别**

  - `div` & `span`
    div：指定渲染HTML的容器

    span：指定内嵌文本容器

    通俗地讲就是如果里面还有其他标签的时候就用div，如果里面只有文本就应该用span

    div是一个块级元素，用来为HTML文档内大块的内容提供结构和背景

    span是行内元素，在行内定义一个区域（也就是一行内可以被<span>划分好几个区域）

    div标签中可以镶嵌span标签，（div可以看做是一个大容器，span是一个小容器，大容器当然可以放下一个小容器啦）

  - `span` & `label`
    由于有了以上关于span的说法,那么我们就可以对文本进行span的容器操作了,可以定义css样式什么的,但是label好像也可以?但是两者区别有时什么呢?

    label标签主要用于绑定一个表单元素, 当点击label标签的时候, 被绑定的表单元素就会获得输入焦点

    span则是对普通的文本的一种容器。
    > **note**: 要将 LABEL 绑定到其它的控件，请将 LABEL 元素的 FOR 属性设置为与该控件的 ID 相同。
    > 将 LABEL 绑定到控件的 NAME 属性毫无用处。但是，要提交表单，你必须为 LABEL 元素所绑定到的控件指定 NAME。
    > 如果用户单击 LABEL，则会先触发 LABEL 上的 onclick 事件，然后触发由 htmlFor 属性所指定的控件上的 onclick 事件。按下 LABEL 设定的快捷键将设置焦点但并不触发 onclick 事件。

- [x] **元素排列**
  - Flex flow
    - 多元素垂直居中排列
      ```
      parent {
        display: flex;
        flex-direction: column;
        justify-content: space-evenly; // only chrome 60 | firefox 52
        justify-content: space-around;
        align-items: center;
      }
      ```
    - 多元素水平居中排列
      ```
      parent {
        display: flex;
        flex-direction: row;
        justify-content: space-evenly; // only chrome 60 | firefox 52
        justify-content: space-around;
        align-items: center;
      }
      ```
    - 单元素水平垂直居中
      ```
      target {
        display: flex;
        flex-direction: column;
        aligin-items: center;
        justify-content: space-around | space-evenly;
      }
      ```

  - 单个元素垂直居中
    - 不知道自己的高度 & 不知道父元素的高度
      ```
      parentElement {
        position:relative;
      }

      childElement {
        position: absolute;
        top: 50%; // 向上偏移父元素的 1/2 height
        transform: translateY(-50%); // 将自己向上提升自己一半的高度
      }
      ```
    - 已知父元素的高度 & 不知道自己的高度 & 已知没有兄弟元素
      ```
      parent {
        height: 400px;
      }

      child {
        position: relative;
        top: 50%;
        transform: translateY(-50%);
      }
      ```

  - 单个元素水平居中
  - 单个元素垂直水平居中
  - 垂直居中排列
  - 水平居中排列
  - 多列垂直排列
  - 多行水平排列
  - 兼容IE 6的水平垂直居中

- [x] **CSS3 选择器**
  ![css3 选择器](https://segmentfault.com/img/bVTd2d?w=780&h=728)

- [x] **CSS `transition` & `animation` & `transform`**

  - `transition` 过渡
    `transition`： CSS属性，花费时间，效果曲线(默认ease)，延迟时间(默认0)
    ```
    transition: width, .5s, ease, .2s;
    ```

  - `animation` 动画
    语法：
    ```
    animation: duration | timing-function | delay | iteration-count | direction | fill-mode | play-state | name

    animation: duration | timing-function | delay | name

    animation: duration | name
    ```

    需要先编写动画，`@keyframes`
    ```
    @keyframes animationName {
      0% | from { atributes: values }
      100% | to { atributes: values }
    }
    ```
    > **note**: 最好加上 -webkit- 前缀，`@-webkit-keyframes` & `-webkit-animation-x`

  - `transform` 变形
    `transform`:指定作用在元素上的变形。取值为空格分隔的一系列变形的列表，他们会像被组合操作请求一样被分别执行。
    `transform-origin`：指定原点的位置。默认值为元素的中心，可以被移动。很多变形需要用到这个属性，比如旋转，缩放和倾斜，他们都需要一个指定的点作为参数。 。默认(x,y,z)：(50%,50%,0)
    > **note**: 只对 `block` 元素生效

    - `transform`
      - 旋转
        ```
        transform:  rotate(angle);
        ```
        从原点(由 `transform-origin` 属性指定)开始安装顺时针方向旋转元素一个特定的角度。此操作对象的矩阵是  `[cos(angle) sin(angle) -sin(angle) cos(angle) 0 0]` 。
      - 缩放
        ```
        transform:  scale(sx[, sy]);     /* one or two unitless <number>s, e.g.  scale(2.1,4) */
        ```
      - X方向缩放
        ```
        transform:  scaleX(sx);
        ```
      - Y方向缩放
        ```
        transform:  scaleY(sy);
        ```

      - 倾斜
        ```
        transform:  skew(ax[, ay])       /* one or two <angle>s, e.g.  skew(30deg,-10deg) */
        ```
        元素在X轴和Y轴方向以指定的角度倾斜。如果ay未提供，在Y轴上没有倾斜。

      - X方向倾斜
        ```
        transform:  skewX(angle)         /* an <angle>, e.g.  skewX(-30deg) */
        ```
        绕X轴以指定的角度倾斜

      - Y方向倾斜
        ```
        transform:  skewY(angle)         /* an <angle>, e.g.  skewY(4deg) */
        ```
        绕Y轴以指定的角度倾斜

      - 平移
        ```
        transform:  translate(tx[, ty])  /* one or two <length> values */
        ```
        用向量[tx, ty]完成2D平移。如果ty没有指定，它的值默认为0。

      - X方向平移
        ```
        transform:  translateX(tx)       /* see <length> for possible values */
        ```
        在X轴平移指定距离

      - Y方向平移
        ```
        transform:  translateY(ty)       /* see <length> for possible values */
        ```
        在Y轴平移指定距离

- [x] **外边距折叠 (margin collapsing)**
  块级元素的上外边距和下外边距有时会合并（或折叠）为一个外边距，其大小取其中的最大者，这种行为称为外边距折叠（margin collapsing），有时也翻译为外边距合并。注意浮动元素和绝对定位元素的外边距不会折叠。

  下面列出了会发生外边距折叠的三种基本情况：

  - 相邻元素之间
    毗邻的两个元素之间的外边距会折叠（除非后一个元素需要清除之前的浮动）。

  - 父元素与其第一个或最后一个子元素之间
    如果在父元素与其第一个子元素之间不存在边框、内边距、行内内容，也没有创建块格式化上下文、或者清除浮动将两者的 margin-top 分开；或者在父元素与其最后一个子元素之间不存在边框、内边距、行内内容、height、min-height、max-height将两者的 margin-bottom 分开，那么这两对外边距之间会产生折叠。此时子元素的外边距会“溢出”到父元素的外面。

  - 空的块级元素
    如果一个块级元素中不包含任何内容，并且在其 margin-top 与 margin-bottom 之间没有边框、内边距、行内内容、height、min-height 将两者分开，则该元素的上下外边距会折叠。

- [x] **BFC 原理**

  - BFC 是什么
    块格式化上下文（Block Formatting Context，BFC） 是Web页面的可视化CSS渲染的一部分，是布局过程中生成块级盒子的区域，也是浮动元素与其他元素的交互限定区域。

  - 怎么样会创建 BFC
    - 根元素或包含根元素的元素
    - 浮动元素（元素的 float 不是 none）
    - 绝对定位元素（元素的 position 为 absolute 或 fixed）
    - 行内块元素（元素的 display 为 inline-block）
    - 表格单元格（元素的 display为 table-cell，HTML表格单元格默认为该值）
    - 表格标题（元素的 display 为 table-caption，HTML表格标题默认为该值）
    - 匿名表格单元格元素（元素的 display为 table、table-row、 table-row-group、table-header-group、table-footer-group（分别是HTML table、row、tbody、thead、tfoot的默认属性）或 inline-table）
    - overflow 值不为 visible 的块元素
    - display 值为 flow-root 的元素
    - contain 值为 layout、content或 strict 的元素
    - 弹性元素（display为 flex 或 inline-flex元素的直接子元素）
    - 网格元素（display为 grid 或 inline-grid 元素的直接子元素）
    - 多列容器（元素的 column-count 或 column-width 不为 auto，包括 column-count 为 1）
    - column-span 为 all 的元素始终会创建一个新的BFC，即使该元素没有包裹在一个多列容器中

    创建了块格式化上下文的元素中的所有内容都会被包含到该BFC中。

    块格式化上下文对浮动定位(参见) [float](https://developer.mozilla.org/zh-CN/docs/Web/CSS/float)）与清除浮动(参见) [clear](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clear)）都很重要。浮动定位和清除浮动时只会应用于同一个BFC内的元素。浮动不会影响其它BFC中元素的布局，而清除浮动只能清除同一BFC中在它前面的元素的浮动。外边距折叠（Margin collapsing）也只会发生在属于同一BFC的块级元素之间。

  - 应用
    可以用于防止margin collapasing
    用于清除浮动（浮动定位和清除浮动时只会应用于同一个BFC内的元素。浮动不会影响其它BFC中元素的布局，而清除浮动只能清除同一BFC中在它前面的元素的浮动。）

## http

- [X] **get和post的区别**
  GET和POST本质上就是TCP链接，并无差别。但是由于HTTP的规定和浏览器/服务器的限制，导致他们在应用过程中体现出一些不同

  GET和POST还有一个重大区别，简单的说：
  GET产生一个TCP数据包；POST产生两个TCP数据包。
  不同的UA还可能有不同的实现。

  [原文](http://mp.weixin.qq.com/s?__biz=MzI3NzIzMzg3Mw==&mid=100000054&idx=1&sn=71f6c214f3833d9ca20b9f7dcd9d33e4#rd)

- [x] **REST & RESTful**
    [参考](https://www.zhihu.com/question/28557115);
    *Resource Representational State Transfer*
    一句话概括：[@lvony](https://www.zhihu.com/people/6ef2e77274cb0719253a577665cf690e)
    URL定位资源，用HTTP动词（GET,POST,DELETE,DETC）描述操作。

  - server api 设计的最佳实践
    1. URL root:
      https://example.org/api/...
      https://api.example.com/...

    2. api 带上 versioning
      https://example.org/api/vx.x.x/...
      https://api.example.com/vx.x.x/...

    3. URI使用名词而不是动词，且推荐用复数。
      BAD
        - /getProducts/listOrders/retrieveClientByOrder?orderId=1

      GOOD
        - GET /products : will return the list of all products
        - POST /products : will add a product to the collection
        - GET /products/4 : will retrieve product
        - PATCH | PUT /products/4 : will update product

- [x] **HTTP2 相比 HTTP1.1**
    [参考链接](http://www.alloyteam.com/2016/07/httphttp2-0spdyhttps-reading-this-is-enough/)
    [参考链接2](http://www.alloyteam.com/2015/03/http2-0-di-qi-miao-ri-chang/)
  - 多路复用（MultiPlexing）
    即连接共享，即每一个request都是是用作连接共享机制的。一个request对应一个id，这样一个连接上可以有多个request，每个连接的request可以随机的混杂在一起，接收方可以根据request的 id将request再归属到各自不同的服务端请求里面。多路复用原理图：
    ![](http://tenny.qiniudn.com/duolufuyong.png)

  - 新的二进制格式（Binary Format）(二进制分帧)
    HTTP1.x的解析是基于文本。基于文本协议的格式解析存在天然缺陷，文本的表现形式有多样性，要做到健壮性考虑的场景必然很多，二进制则不同，只认0和1的组合。基于这种考虑HTTP2.0的协议解析决定采用二进制格式，实现方便且健壮。

  - header压缩
    如上文中所言，对前面提到过HTTP1.x的header带有大量信息，而且每次都要重复发送，HTTP2.0使用encoder来减少需要传输的header大小，通讯双方各自cache一份header fields表，既避免了重复header的传输，又减小了需要传输的大小。

  - 服务端推送（server push）
    同SPDY一样，HTTP2.0也具有server push功能。目前，有大多数网站已经启用HTTP2.0，例如YouTuBe，淘宝网等网站，利用chrome控制台可以查看是否启用H2：
    ![](http://tenny.qiniudn.com/h2.png)
    更多关于HTTP2的问题可以参考：HTTP2奇妙日常，以及HTTP2.0的官方网站。
    关于HTTP2和HTTP1.x的区别大致可以看下图：
    ![](http://tenny.qiniudn.com/diff332.png)