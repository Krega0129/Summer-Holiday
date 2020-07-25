1. 简化对象写法：ES6 允许在大括号里面，直接写入变量和函数，作为对象的属性和方法。

   ```js
   let name = 'Mike';
   let age = function() {
   	console.log(17);
   }
   
   const person = {
   	name,	//可以把变量名作为属性值
   	age,	//可以把函数变量作为方法
   	sport(){	//声明方法可以省略:function
   		console.log('football');
   	}
   }
   console.log(person);
   ```

2. 对象方法扩展：

   1. `Object.is()` 判断两个值是否完全相等。与 `===` 不同地方在于：

      ```js
      console.log(Object.is(NaN,NaN));	//true
      console.log(NaN === NaN);	//false
      ```

   2. `Object.assign()` 对象的合并：接受两个对象作为参数，若两个对象有相同的属性/方法，则第二个参数会覆盖第一个参数的属性/方法。

   3. `Object.setPrototypeOf()` 设置原型对象，`Object.getPrototypeOf()` 获取原型对象。

   4. `super` ：指向当前对象的原型对象。`super`关键字表示原型对象时，只能用在**对象的方法**之中，用在其他地方都会报错。JavaScript 引擎内部，`super.foo`等同于`Object.getPrototypeOf(this).foo`（属性）或`Object.getPrototypeOf(this).foo.call(this)`（方法）。

   5. 属性遍历：

      1. `for...in`循环遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。
      2. `Object.keys()`返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含 Symbol 属性）的键名。
      3. `Object.getOwnPropertyNames()`返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。
      4. `Object.getOwnPropertySymbols()`返回一个数组，包含对象自身的所有 Symbol 属性的键名。
      5. `Reflect.ownKeys()`返回一个数组，包含对象自身的（不含继承的）所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。

3. ES8 对象方法扩展：

   1. `Object.entries()` ：返回一个数组，每个元素也是数组，第一个元素是键名，第二个元素是值。
   2. `Object.values()` ：获取对象所有的值。
   3. `Object.getOwnPropertyDescriptors()`：获取对象属性的描述对象。

4. ES10 对象扩展：

   1. `Object.fromEntries()` 参数为二维数组或者是Map，将参数转化为对象。相当于 `ES8`中 `Object.Entries()` 方法的逆运算。