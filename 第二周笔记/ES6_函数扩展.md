1. 箭头函数：ES6 允许使用箭头函数。
   1. 声明箭头函数：

      ```js
      let fn = (a,b) => {
      	return a+b;
      }
      let result = fn(2,3);
      console.log(result);	//5
      ```

   2. 特性：

      1. this 是静态的，this始终指向函数声明时所在作用于下的 this的值，不受call，apply，bind的影响。

      2. 不能作为构造函数实例化对象。

      3. 不能使用 arguments 对象。

      4. 箭头函数的简写：

         1. 当形参有且只有一个的时候可以省略小括号

         2. 当函数体只有一条语句的时候可以省略花括号，return 也要省略

            ```js
            let add = n => n + 1;
            console.log(add(2));
            ```

            

   3. 应用：

      1. 定时器中的回调函数使用箭头函数，函数中的this的值是静态的，指向声明箭头函数的作用域的this。(详情见vscode中'ES6_箭头函数应用1.html')

      2. 返回数组的偶数元素：

         ```js
         const arr = [1,25,30,24,17,100];
         
         //传统方法：
         const result = arr.filter(function(item){
         	return item % 2 === 0;
         });
         console.log(result);
         
         //箭头函数：
         const result = arr.filter(item => item % 2 === 0);
         console.log(result);
         ```

   4. 总结：

      1. 箭头函数适用于与 this 无关的回调，如定时器、数组的方法回调。
      2. 箭头函数不适用于与 this 有关的回调，如事件回调、对象的方法。

2. 函数参数的默认值设置：ES6 允许给函数参数设置初始值。

   1. 形参初始值，具有默认值的参数，一般放在后面。

      ```js
      function add(a,b,c=10){
      	return a + b + c;
      }
      
      let result = add(2,3);
      console.log(result);	//15
      ```

   2. 与解构赋值结合：

      ```js
      function connect({name='John',age,sex}){
          console.log(name);	//John
          console.log(age);	//18
          console.log(sex);	//男
      }
      
      connect({
          age:18,
          sex:'男'
      })
      ```

3. async函数：ES2017 标准引入了 async 函数，使得异步操作变得更加方便。
   1. 声明：

      ```js
      async function fn(){
      	//code
      }
      ```

      async函数返回值是一个 Promise 对象，若return 一个失败的 Promise，则返回值为失败的，否则为成功的。

   2. await：正常情况下，`await`命令后面是一个 Promise 对象，返回该**对象的结果**。如果不是 Promise 对象，就直接返回对应的值。若 await 后的 promise 对象为失败，则需要用 try...catch 捕获。见 ES8_await.html。

   3. async 和 await 结合发送 Ajax 请求，见 ES8_async-await发送Ajax请求.html。

