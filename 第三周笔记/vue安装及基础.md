###### vue.js安装

1. 直接CDN引入：（反应较慢）

   ```html
   <!-- 开发环境版本，包含了有帮助的命令行警告 -->
   <script src="https://cdn.jsdelivr.net/npm/vue/dist/vur.js"></script>
   <!-- 生产环境版本，优化了尺寸和速度 -->
   <script src="https://cdn.jsdelivr.net/npm/vue"></script>
   ```

2. 下载和引入：

   1. 开发环境：https://vuejs.org/js/vue.js
   2. 生产环境：https://vuejs.org/js/vue.min.js

3. MPN安装：



###### 声明式开发

1. Mustache语法：

   ```html
   <!-- 引入vue.js文件 -->
   <script src='./js/vue.js'></script>
   <!-- 用双层大括号{{}}包围变量（Mustache语法） -->
   <div id='app'>		<!-- 被挂载到vue实例上 -->
       {{message}}		<!-- 双括号里面写变量 输出Hello Vuejs -->
   	{{firstName + ' ' + lastName}} <!-- 双括号里面写简单的表达式 输出Michael Jackson -->
       {{firstName}} {{lastName}}		<!-- 同样输出Michael Jackson -->
   </div>		
   <div>{{message}}</div>		<!-- 没有被挂载到vue实例上 输出{{message}} -->
   
   <script>
   	const app = new Vue({	//创建vue对象
           el : '#app',	//挂载要管理的元素
           data : {		//定义数据
               message : 'Hello Vuejs',
               firstName: 'Michael',
               lastName: 'Jackson'
           }
       });
   </script>
   ```

2. Vue列表

   ```html
   <div id="app">
       <ul>
           <!-- v-for='item in arr'遍历arr数组 -->
           <li v-for='item in arr'>{{item}}</li>
       </ul>
   </div>
   
   <script>
       const app = new Vue({
           el : '#app',
           data : {
               message : 'Vue列表',
               arr : [1,2,3,4]
           }
       })
   </script>
   ```

   响应式开发：当我们数据发生变化时，页面的内容也会跟着改变，因此我们可以给数组push一个元素，界面会直接多出一个，不需要我们进行DOM操作。

3. `v-on: 事件名 = ` 事件监听，如监听点击事件：`v-on : click = fn()`，可简写成`@click=`（语法糖）。

4. Vue对象属性：

   1. el ：string类型或HTMLElement，决定Vue实例会管理哪一个DOM。
   2. data：Object或Function类型（组件中data必须是一个函数），Vue实例对应的数据对象。
   3. methods：{[key : string] : Function}，定义Vue的一些方法，可以在指令内外使用。
   4. ……

5. Vue的生命周期函数（钩子）：

   ![](https://cn.vuejs.org/images/lifecycle.png)

   每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做**生命周期钩子**的函数，这给了用户在不同阶段添加自己的代码的机会。**生命周期钩子的 `this` 上下文指向调用它的 Vue 实例**。

   不要在选项 property 或回调上使用[箭头函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，因为箭头函数并没有 `this`，`this` 会作为变量一直向上级词法作用域查找，直至找到为止，经常导致 `Uncaught TypeError: Cannot read property of undefined` 或 `Uncaught TypeError: this.myMethod is not a function` 之类的错误。

6. 插值操作：

   1. `v-once`：

      1. 该指令后不需跟任何表达式；
      2. 该指令表示的元素和组件只渲染一次，不会随着数据的改变而改变。

   2. `v-html`：

      1. 指令后往往跟上一个String类型；
      2. 将String解析成HTML并进行渲染；

   3. `v-text`：

      1. 指令后往往跟上一个String类型；
      2. 与 Mustache 语法相似，将文本数据显示在页面上，但是会覆盖之前的值；

   4. `v-pre`：使 Mustache 语法不会被解析，原封不动地显示出来；

   5. `v-cloak`：当vue还没解析完成前，隐藏内容；当解析完成后，再显示出来，此时自动删除该属性。

   6. `v-bind`：动态绑定属性；

      ```html
      <div id="app">
          <!-- 此处将字符串 {{imgUrl}}赋值给src，不能显示图片 -->
          <img src="{{imgUrl}}" alt="">
          <!-- 此处将Vue实例中的imgUrl绑定给src，可以显示图片 -->
          <img v-bind:src="imgUrl" alt="">
          <!-- 语法糖写法，直接在需要绑定的属性前面加上 : -->
          <img :src="imgUrl" alt="">
      </div>
      
      <script>
          const app = new Vue({
              el: '#app',
              data: {
                  message: 'hello',
                  imgUrl: 'https://images-cn-8.ssl-images-amazon.com/images/G/28/pangshuo/GS/GW/hero/2020/Desktop_Hero_cewk12._CB421024157_.jpg'
              }
          })
      </script>
      ```

      ###### v-bind 动态绑定class（对象语法）

      ```html
      <h2 :class="{attr1: isAttr1, attr2: isAttr2}"></h2>
      ```

      上面的class属性值为一个对象，该对象每个键的值均为一个Boolean，true则该值生效，false则不生效。通过 vue 改变 isAttr1 和 isAttr2 的值，可以实现动态绑定class。当class对象里面的属性太多，为了方便和美观，可以写成一个methods，将对象作为返回值返回。【注】：每个Boolean前需要加上 `this`。

      ```vue
      methods: {
      	getClasses(){
      		return {attr1: this.isAttr1, attr2: this.isAttr2}
      	}
      }
      ```

      ###### v-bind 动态绑定class（数组语法）

      ```html
      <h2 :class="[attr1, attr2]"></h2>
      ```

      上面代码中的 attr1 和 attr2 充当变量，不需要加上引号，可以通过vue改变这些变量的值来实现动态绑定class。同理，与对象语法一样，我们可以写成一个methods，将数组当作返回值返回，每个数组元素也要加上`this`。

      ###### v-bind 动态绑定style（对象语法）

      ```html
      <h2 :style="{fontSize: '20px', background: 'red'}">{{message}}</h2>
      ```

      上面代码中 style 接收了一个对象，属性名为css属性（驼峰式），属性值要写为字符串形式，若不加上引号则解析为变量，用变量可以在vue中动态改变属性值，从而改变style。也可以写成methods。

      ###### v-bind 动态绑定style（数组语法）

      ```html
      <h2 id="a" :style="[style1, style2]">{{message}}</h2>
      
      <script>
      	const app = new Vue({
              el: '#a',
              data: {
                  message: 'Hello',
                  style1: {fontSize: '20px'},
                  style2: {backgound: 'red'}
              }
          })
      </script>
      ```

      传入一个数组，每个元素是一个变量，本质上是一个对象，对象里面是键值对：css属性名和属性值。

7. 计算属性 `computed`：当我们需要对一些数据进行一些转化后再显示，或者将多个值结合起来再显示时，我们需要用到计算属性。

   ```html
   <div id="app">
       <h2>{{fullName}}</h2>
       <!-- 输出Michael Jackson -->
   </div>
   
   <script>
       const app = new Vue({
           el: '#app',
           data: {
               firstName: 'Michael',
               lastName: 'Jackson'
           },
           computed: {     //计算属性，里面写方法
               fullName(){
                   return this.firstName + ' ' + this.lastName;
               }
           }
       })
   </script>
   ```

   还能进行一些复杂操作：求总价格等

   ```html
   <div id="app">
       <h2>总价格：{{fullPrice}}</h2>
   </div>
   
   <script>
       const app = new Vue({
           el: '#app',
           data: {
               books: [
                   {id: 01, name: 'a', price: 105},
                   {id: 02, name: 'b', price: 123},
                   {id: 03, name: 'c', price: 153},
                   {id: 04, name: 'd', price: 85}
               ]
           },
           computed: {
               fullPrice(){
                   let result = 0;
                   for (let i of this.books){
                       result += i.price;
                   }
                   return result;
               }
           }
       })
   </script>
   ```

   与methods的不同点：计算属性会进行缓存，如果多次使用，计算属性只会调用一次。而methods则会调用多次。上面代码中的computed的fullName实际上是简写形式，它的完整形式应该包含getter和setter。

   ```vue
   computed: {
   	fullName:{
   		set(newValue){
   			const names = newValue.split(' ');
   			this.firstName = names[0];
   			this.lastName = names[1];
   		},
   		get(){
   			return this.firstName + ' ' + this.lastName;
   		}
   	}
   }
   ```

   若我们想要该属性为只读，我们就可以不写set，因此可以简写成上面的代码。



###### 事件监听

1. `v-on`：语法糖为@：

   ```html
   <button v-on:click='fun'>按钮</button>
   <!-- 等价于 -->
   <button @click='fun'>按钮</button>
   ```

   1. 参数：当我们不传入参数的时候，可以不写括号，但是浏览器会自动产生一个时间对象传入。当我们要传入参数是，我们需要加上参数，如果我们要当手动传入事件对象，用 `$event` 充当实参传入。

      ```html
      <button v-on:click='fun(a,$event)'>按钮</button>
      ```

   2. 修饰符：

      1. `.stop`：调用 `event.stopPropagation()`，防止事件冒泡。

      2. `.prevent`：调用 `event.preventDefault()`，阻止默认行为。

      3. `.{keyCode | keyAlias}`：当事件是从键盘上特定按键触发时才触发回调。`.`后面加的是键码或者简写。

         ```html
         <input @keyup.13="fn">
         <input @keyup.enter="fn">
         ```

      4. `.native`：监听组件根元素的原生事件。

      5. `.once`：只触发一次回调。



###### 条件判断

1. `v-if`：当 `v-if` 的后面的条件为false时，对应的元素及其子元素不会**渲染**（直接不存在此元素）。
2. `v-else-if`：跟在`v-if`或者`v-else-if`后，当 `v-if` 以及前面的 `v-else-if` 为 false 时，`v-else` 的对应的元素及其子元素会渲染，否则不会渲染。
3. `v-else`：跟在`v-if`或者`v-else-if`后，当 `v-if` 以及前面的 `v-else-if` 为 false 时，`v-else` 的对应的元素及其子元素会渲染，否则不会渲染。
4. `v-show`：当 `v-if` 的后面的条件为 false 时，对应的元素及其子元素不会**显示**。
5. `v-if` 与 `v-show` 不同的地方在于，`v-if`为 false 时，元素直接不存在，相当于 true 和 false 转换的时候是对元素的增删操作；但是，当 `v-show` 为 false 时，只是给元素添加了一个 `display: none` 样式，没有对元素的增删操作。因此，若要频繁地改变元素地显示或消失，用 `v-show` 能提高性能。如果只有一次切换时，通常使用`v-if`。



###### 循环遍历

1. `v-for` 遍历数组：

   1. 遍历过程中，没有使用索引值。

      ```html
      <ul>
          <li v-for="item in arr">{{item}}</li>
      </ul>
      ```

   2. 遍历过程中，获取索引值。

      ```html
      <ul>
          <li v-for="(item, index) in arr">{{index}}-{{item}}</li>
      </ul>
      ```

2. `v-for` 遍历对象：

   1. 只获取一个值，该值为 value；

      ```html
      <ul>
          <li v-for="value in obj">{{value}}</li>
      </ul>
      ```

   2. 获取两个值，第一个值为 value，第二个值为key；

      ```html
      <ul>
          <li v-for="(value, key) in obj">{{key}}：{{value}}</li>
      </ul>
      ```

   3. 获取三个值，第三个值为索引index；

      ```html
      <ul>
          <li v-for="(value, key, index) in obj">{{index}}-{{key}}：{{value}}</li>
      </ul>
      ```

3. `v-for` 绑定 key 可以提高代码的复用率和性能，但是必须保正每个key值唯一且不会改变。

4. 响应式的数组方法：使用这些方法改变数组后，会显示出来。

   1. `.push` 
   2. `.pop`
   3. `.shift`
   4. `.unshift`
   5. `.splice`
   6. `.sort`
   7. `.reverse`

   【注】通过索引值修改数组元素不是响应式的，可以用`.splice` 来实现，或者使用 `vue.set()` 方法：

   `vue.set(要修改的对象, 索引值, 修改后的值)`。



###### v-model

1. vue 中使用 `v-model` 指令来实现表单元素和数据的双向绑定：`v-model` 后跟着一个变量(可加引号)，变量的内容充当 input 中的 value，message 的值被绑定到 input 中，因此修改 message 的值， input 的 value 会改变，同时，input 的 value 也绑定到 message 上，修改 input 的 value，message 也会改变。

2. 也可以将 `v-model` 用于 `textarea` 元素。

3. 原理：

   1. `v-bind` 绑定一个 value 属性；
   2. `v-on` 指令给当前元素绑定 input 事件（当输入内容时触发）；

   ```html
   <input type="text" v-model="message">
   <!-- 等同于 -->
   <input type="text" :value="message" @input="message = $event.target.value">
   ```

4. `v-model` 绑定 checkbox。

   1. checkbox 中若 `v-model` 绑定的是一个变量，该变量有值（除0，undefined，null等）或为true，则为选中，值为false，0，undefined等则为不选中。且变量的值为 true 或 false。

      ```html
      <input type="checkbox" value='a' id="agree" v-model="isAgree">同意协议
      ```

   2. checkbox 中若 `v-model` 绑定的是一个数组，则选中的value值会添加到该数组中。

5. `v-model` 绑定 select。

   1. 单选：`v-model` 绑定的是一个值，当我们选中其中一个 option 时，就会将它对应的value赋值到该值中：

      ```html
      <select name="a" v-model="fruit">	<!-- 此处绑定了 fruit 变量 -->
          <option value="apple">apple</option>
          <option value="banana">banana</option>
          <option value="grape">grape</option>
          <option value="pear">pear</option>
      </select>
      <h2>The fruit you have chose is: {{fruit}}</h2>
      ```

   2. 多选：`v-model` 绑定的是一个数组，当我们选中多个值时，就会将选中的value添加到该数组中：

      ```html
      <select name="a" v-model="fruits" multiple>		<!-- 此处绑定了fruits数组 -->
          <option value="apple">apple</option>
          <option value="banana">banana</option>
          <option value="grape">grape</option>
          <option value="pear">pear</option>
      </select>
      <h2>The fruit you have chose is: {{fruits}}</h2>
      ```

6. 值绑定：动态添加value值，比如我们在服务器上获取到一些数据，把这些数据动态添加到input中的value上。

   ```html
   <div id="app">
       <label v-for="item in originHobbies" :for="item">
           <input type="checkbox" :value="item" :id="item" v-model="hobbies">{{item}}
       </label>
       <h2>您的爱好是：{{hobbies}}</h2>
   </div>
   
   <script>
       const app = new Vue({
           el: '#app',
           data: {
               isAgree: false,
               hobbies: [],
               originHobbies: ['唱', '跳', 'Rap', '篮球', '足球', '台球']
           }
       })
   </script>
   ```

7. `v-model` 修饰符：

   1. lazy：默认情况下， `v-model` 默认在input事件中同步输入框的数据，lazy修饰符可以让数据早失去焦点或者按下回车后再更新。
   2. number：默认情况下，再输入框中无论输入字母还是数字，都会当成字符串处理，number修饰符可以让在输入框输入的内容自动转成数字类型。
   3. trim：可以过滤输入内容左右两边的空格。