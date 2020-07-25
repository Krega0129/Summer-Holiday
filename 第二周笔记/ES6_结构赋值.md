1. 结构赋值：ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构。

   1. 数组的解构：

      ```js
      const NUM = [1,2,3,4,5];
      let [a,b,c] = NUM;  //相当于给abc三个变量赋值，每个变量对应NUM数组里面的元素，NUM中多出来没事
      console.log(a); //1
      console.log(b); //2
      console.log(c); //3
      ```

      本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。

      ```js
      let [foo, [[bar], baz]] = [1, [[2], 3]];
      console.log(foo); // 1
      console.log(bar); // 2
      console.log(baz); // 3
      
      let [ , , third] = ["foo", "bar", "baz"];
      console.log(third); // "baz"
      
      let [x, , y] = [1, 2, 3];
      console.log(x); // 1
      console.log(y); // 3
      
      let [head, ...tail] = [1, 2, 3, 4];
      console.log(head); // 1
      console.log(tail); // [2, 3, 4]
      
      let [x, y, ...z] = ['a'];
      console.log(x); // "a"
      console.log(y); // undefined
      console.log(z); // []
      ```

   2. 对象的解构：

      ```js
      const person = {
          name : 'Mike',
          age : 18,
          sport : function() {
              console.log('football');
          }
      }
      
      //注：此处{}里面的变量名字要与person里的属性名一致，不然会解构失败，值为undefined
      let {name,age,sport} = person;  
      console.log(name);  //Mike，引用时不用在前面加上对象
      console.log(age);   //18
      console.log(sport); //function{console.log('football');}
      sport();    //football，此处不用对象来调用，对于要对此调用的方法比较方便
      ```
