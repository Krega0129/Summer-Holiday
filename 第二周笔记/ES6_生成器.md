1. 生成器：ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同（可以看成是一个可以暂停的特殊函数，其中yield就是暂停的标志）。
   1. 特征：

      1. 在function和函数名之间需要加上一个`*`；

      2. 函数体内部使用`yield`表达式，定义不同的内部状态。

         ```js
         function* gen(){
             yield 'hello';
             yield 'world';
             return 'ending';
         }
         ```

         上面的函数它内部有两个`yield`表达式（`hello`和`world`），即该函数有三个状态：hello，world 和 return 语句（结束执行）。调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个遍历（迭代）器对象，代表 Generator 函数的内部指针。必须调用遍历器对象的`next`方法，使得指针移向下一个状态。也就是说，每次调用`next`方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个`yield`表达式（或`return`语句）为止。换言之，Generator 函数是分段执行的，`yield`表达式是暂停执行的标记，而`next`方法可以恢复执行。（**简单来说就是Generator函数中的yield和return把函数分成了若干部分，每调用一次next方法，就执行一个部分**）

   2. yield表达式：由于 Generator 函数返回的遍历器对象，只有调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数。`yield`表达式就是暂停标志。

      1. 遍历器对象的`next`方法的运行逻辑：

         1. 遇到`yield`表达式，就暂停执行后面的操作，并将紧跟在`yield`后面的那个表达式的值，作为返回的对象的`value`属性值。
         2. 下一次调用`next`方法时，再继续往下执行，直到遇到下一个`yield`表达式。
         3. 如果没有再遇到新的`yield`表达式，就一直运行到函数结束，直到`return`语句为止，并将`return`语句后面的表达式的值，作为返回的对象的`value`属性值。
         4. 如果该函数没有`return`语句，则返回的对象的`value`属性值为`undefined`。

      2. `yield`后面的表达式不会立即求值，只会在`next`方法将指针移到这一句时，才会求值。

      3. `yield`表达式只能用在 Generator 函数里面，如果用在另一个表达式之中，必须放在圆括号里面。

         ```js
         function* demo() {
           console.log('Hello' + yield); // SyntaxError
           console.log('Hello' + yield 123); // SyntaxError
         
           console.log('Hello' + (yield)); // OK
           console.log('Hello' + (yield 123)); // OK
         }
         ```

      4. `yield`表达式**用作函数参数** 或 **放在赋值表达式的右边**，可以不加括号。

         ```js
         function* demo() {
           foo(yield 'a', yield 'b'); // OK
           let input = yield; // OK
         }
         ```

   3. 与Iterator接口的关系：任意一个对象的`Symbol.iterator`方法，等于该对象的遍历器生成函数，调用该函数会返回该对象的一个遍历器对象，由于 Generator 函数就是遍历器生成函数，因此可以把Generator 赋值给对象的`Symbol.iterator`属性，从而使得该对象具有 Iterator 接口。Generator 函数执行后，返回一个遍历器对象。该对象本身也具有`Symbol.iterator`属性，执行后返回自身。

      ```js
      //生成器函数
      function* gen(){
        // some code
      }
      //g为遍历器对象
      var g = gen();
      
      g[Symbol.iterator]() === g
      // true
      ```

   4. next 方法的参数：

      1. `yield`表达式本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会被当作上一个`yield`表达式的返回值。
      2. 由于`next`方法的参数表示**上一个**`yield`表达式的返回值，所以在第一次使用`next`方法时，传递参数是无效的。从语义上讲，第一个`next`方法用来启动遍历器对象，所以不用带有参数。