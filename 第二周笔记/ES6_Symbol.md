1. Symbol基本使用：ES6 引入了一种原始数据类型 Symbol，表示独一无二的值。

   1. 特点：

      1. Symbol 的值是唯一的，用来解决命名冲突的问题。
      2. Symbol 值不能与其他数据进行运算（+-*/，拼接等），但是，Symbol 值可以显式转为字符串（String() 和 toString() 方法），另外，Symbol 值也可以转为布尔值，但是不能转为数值。
      3. Symbol 定义的属性不能使用 `for...in`循环遍历，但是可以用`Reflect.ownKeys()`来获取对象的所有键名（包括常规键名和Symbol键名）。

   2. 声明：`Symbol`函数前不能使用`new`命令，否则会报错。这是因为生成的 Symbol 是一个原始类型的值，不是对象。基本上，它是一种类似于字符串的数据类型。`Symbol`函数可以接受一个字符串作为参数，表示对 Symbol 实例的描述，如果 Symbol 的参数是一个对象，就会调用该对象的`toString`方法，将其转为字符串，然后才生成一个 Symbol 值。注意，`Symbol`函数的参数只是表示对当前 Symbol 值的描述，因此相同参数的`Symbol`函数的返回值是不相等的。

   3. Symbol.prototype.description：[ES2019](https://github.com/tc39/proposal-Symbol-description) 提供了一个实例属性`description`，直接返回 Symbol 的描述。

      ```js
      let sym = Symbol('Good');
      console.log(sym.description);	//Good
      ```

   4. 作为属性名的Symbol：由于每一个 Symbol 值都是不相等的，这意味着 Symbol 值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性，防止某一个键被不小心改写或覆盖。

      1. Symbol 值作为对象属性名时，不能用点运算符。因为点运算符后面总是字符串，所以不会读取`mySymbol`作为标识名所指代的那个值，导致`a`的属性名实际上是一个字符串，而不是一个 Symbol 值。同理，使用 Symbol 值定义属性时，Symbol 值必须放在方括号之中。

         ```js
         const mySymbol = Symbol();
         const a = {};
         
         a.mySymbol = 'Hello!';	//此处的 mySymbol 作为字符串形式
         a[mySymbol] 	// undefined
         a['mySymbol'] 	// "Hello!"
         ```

      2. Symbol 类型还可以用于定义一组常量，保证这组常量的值都是不相等的。

      3. Symbol 值作为属性名时，该属性还是公开属性，不是私有属性。

   5. 属性名的遍历：Symbol 作为属性名，但不是私有属性，有一个`Object.getOwnPropertySymbols()`方法，可以获取指定对象的所有 Symbol 属性名。该方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。

      ```javascript
      const obj = {};
      let a = Symbol('a');
      let b = Symbol('b');
      
      obj[a] = 'Hello';
      obj[b] = 'World';
      
      const objectSymbols = Object.getOwnPropertySymbols(obj);
      
      console.log(objectSymbols)	// [Symbol(a), Symbol(b)]
      ```

      `Object.getOwnPropertySymbols()`方法与`for...in`循环、`Object.getOwnPropertyNames`方法对比:

      ```javascript
      const obj = {};
      const foo = Symbol('foo');
      
      obj[foo] = 'bar';
      
      for (let i in obj) {
        console.log(i); // 无输出
      }
      
      Object.getOwnPropertyNames(obj) // []
      Object.getOwnPropertySymbols(obj) // [Symbol(foo)]
      ```

   6. Symbol.for() 和 Symbol.keyFor()：

      1. Symbol.for()：我们希望重新使用同一个 Symbol 值，`Symbol.for()`方法可以做到这一点。它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建一个以该字符串为名称的 Symbol 值，并将其**注册到全局**。

      2. `Symbol.keyFor()`方法返回一个**已登记**（使用了Symbol.for()）的 Symbol 类型值的`key`。

         ```javascript
         let s1 = Symbol.for("foo");
         Symbol.keyFor(s1) // "foo"
         
         let s2 = Symbol("foo");
         Symbol.keyFor(s2) // undefined
         ```

2. `Symbol.prototype.description`：获取Symbol对象的描述字符串。

   ```js
   let s = new Symbol('a');
   console.log(s.description);	//a
   ```

