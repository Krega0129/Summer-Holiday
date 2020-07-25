1. rest参数：ES6 引入 rest 参数（数组），用于获取函数的实参，用来代替arguments（对象）。

   ```js
   function fn(a,b,...args){	//rest参数只能放最后
       console.log(a);     //1
       console.log(b);     //2
       console.log(args);  //[3,4,5,6]，数组
   }
   
   fn(1,2,3,4,5,6);
   ```

2. 扩展运算符：

   1. `...`扩展运算符能将`数组`逗号分隔的`参数序列`

      ```js
      const arr = [1,2,3,4,5];
      function fn(){
      	console.log(arguments);
      }
      
      fn(arr)     //一个参数，参数为数组
      fn(...arr); //5个参数，每个参数为数组的元素
      ```

   2. 应用：

      1. 合并数组（对象也同理，且后一个对象会覆盖前面对象的相同属性）：

         ```js
         const arr1 = [1,2,3];
         const arr2 = [4,5,6];
         
         //传统数组合并：
         const arr3 = arr1.concat(arr2);
         console.log(arr3);  //[1,2,3,4,5,6]
         
         //扩展运算符合并：
         const arr4 = [...arr1,...arr2];
         console.log(arr4);  //[1,2,3,4,5,6]
         ```

      2. 数组克隆（对象也同理）：

         ```js
         const arr = [5,4,3,2,1];
         const arr5 = [...arr];
         console.log(arr5);		//[5,4,3,2,1]
         ```

      3. 将伪数组转为真正的数组。

3. `Array.prototype.includes()` 方法：传入一个参数，检测数组种是否含有该元素。与 `indexOf()` 不同的是 `Array.prototype.includes()` 返回值是一个Boolean， `indexOf()` 返回的是一个数组下标或 -1 。

4. ES10 数组扩展：

   1. `flat()` ：将高维数组转成低维数组（数组降维）。参数为深度（要降的维数），默认为1。（从最外面起第二层开始降），当参数大于数组维数时，降为一维数组。
   2. `flatMap()`：若调用数组的 `map()` 方法后返回的是一个多维数组，可以直接用 `flatMap()` 方法进行降维。