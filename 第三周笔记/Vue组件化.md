###### 组件化：

​		我们可以将一个完整的页面分成很多个组件，每个组件都用于实现页面的一个功能块，而每一个组件又可以进行细分，便于管理、维护，还能进行复用。

1. 组件的使用步骤：

   1. 创建组件构造器：调用 `vue.extend()` 方法创建；

      ```js
      const cpnC = Vue.extend({
          template: `
      <div>
      	<h2>标题</h2>
      	<p>内容a</p>
      	<p>内容b</p>
      </div>`
      })
      ```

      该方法接收一个对象作为参数，参数有一个template属性，属性值为要作为组件的html模板。

   2. 注册组件：

      1. 全局组件：调用 `vue.component()` 方法注册；

         ```js
         Vue.component('my-cpn', cpnC);
         ```

         该方法接收两个参数，第一个为字符串，作为组件的标签，第二个参数为组件构造器。

      2. 局部组件：在Vue实例对象中注册：

         ```js
         const app = new Vue({
             el: '#app',
             data: {},
             components: {
                 cpn: cpnC
             }
         })
         ```

         上面的代码在Vue实例app中有属性 components ，该属性值为对象，对象中的属性为注册的组件标签名，属性值为组件构造器。

      3. 语法糖注册：省去extend，直接将extend中的对象作为注册方法的第二个参数。全局和局部均如此。

         ```js
         Vue.component('my-cpn', {
             template: `
         <div>
         	<h2>标题</h2>
         	<p>内容a</p>
         	<p>内容b</p>
         </div>`
         });
         ```

         【注】组件必须在Vue实例之前注册。

   3. 使用组件：在Vue实例的作用范围内使用；

      ```html
      <my-cpn></my-cpn>
      ```

      使用注册的组件标签。

2. 全局组件和局部组件：全局组件可以在文件中所有 Vue 实例的作用范围使用，局部组件只能在对应的Vue实例作用范围使用。

3. 父组件和子组件：在组件构造器对象里面，除了template属性，还可以添加 components 属性，在里面注册组件，在此处注册的组件成为该组件的子组件。子组件只能在父组件的 template 中使用。在组件的template中使用另一个组件时，会优先在该组件构造器中搜索有无注册要使用的组件，若没有，则去上一级搜索。

4. 组件模板的分离写法：我们在vue种写html代码不方便而且可读性差，分离写法可以解决这个问题：

   1. 使用 `<script>` 标签，类型是 `text/x-template`，最后用 id 绑定：

      ```html
      <script type="text/x-template" id="cpn">
          <div>
              <h2>标题1</h2>
              <p>内容a</p>
          </div>
      </script>
      
      <script>
          Vue.component('cpn',{
              template: '#cpn'
          })
      </script>
      ```

   2. 直接template标签，用id绑定：

      ```html
      <template id="cpn2">
          <div>
              <h2>标题2</h2>
              <p>内容b</p>
          </div>
      </template>
      
      <script>
          Vue.component('cpn2',{
              template: '#cpn2'
          })
      </script>
      ```

5. 组件数据的存放：

   1. 组件不能访问 Vue 实例中的数据，每个组件有自己存放数据的地方。

   2. 组件对象也有一个 data，但是此处的 data 是一个函数，返回一个对象，对象保存着组件的数据。

      ```html
      <template id="cpn">
          <div>
              <h2>{{title}}</h2>
              <p>内容b</p>
          </div>
      </template>
      
      <script>
          Vue.component('cpn',{
              template: '#cpn',
              data(){
                  //组件中的数据
                  return {
                      title: '标题'
                  }
              }
          })
      </script>
      ```

6. Vue 实例和组件的区别：

   1. 每个自定义组件里面的模板必须**有且只有**一个根节点，否则会报错。
   2. Vue 实例中的 data 是一个对象，而组件中的 data 是一个函数，函数返回一个对象，对象里面才是组件里面的值。

7. 父子组件的通信：

   1. 在上面可以直到子组件不能访问父组件或者 Vue 实例的数据，但是我们可以让父组件将数据传递给子组件。

   2. 父子组件通信方式：

      1. 父组件通过 props（属性）向子组件传递数据；

         ```html
         <!-- 父组件 -->
         <div id="app">
             <!-- 将数据传给子组件，此处不支持驼峰写法 -->
             <cpn :cmovies="movies" :cmessage="message"></cpn>
         </div>
         
         <!-- 子组件 -->
         <template id="cpn">
             <div>
                 <ul>
                     <li v-for="item in cmovies">{{item}}</li>
                 </ul>
                 <h2>{{cmessage}}</h2>
             </div>
         </template>
         
         <script>
             const cpn = {
                 template: '#cpn',
                 //通过props接收父组件传递的数据
                 props: ['cmovies', 'cmessage']
             }
         
             const app = new Vue({
                 el: '#app',
                 data: {
                     message: 'hello',
                     movies: ['aaa', 'bbb', 'ccc']
                 },
                 components: {
                     cpn		//字面量增强写法
                 }
             })
         </script>
         ```

         上面的 props 是数组类型，还可以写成对象类型（推荐），对象写法可以对传过来的数据进行类型验证：

         ```js
         const cpn = {
             template: '#cpn',
             props: {
                 //1. 类型限制，传入数组表示支持多种类型的值
                 cmovies: Array,
                 cmessage: [String, Array],
                 
                 //2. 提供一些默认值，不传值时显示默认值，若required为true，则该值必须要传值。
                 cmessage: {
                     type: String,
                     default: 'aaaaa',
                     required: true
                 }，
                 cmovies: {
                 	type: Array,
                     default(){	//若类型是数组或对象，默认值必须是一个函数
             			return []
         			}
             	}
             }
         }
         ```

      2. 子组件通过事件向父组件发送消息；

         ```html
         <!-- 父组件模板 -->
         <div id="app">
             <!-- 此处的函数可以不传入参数，传入的话用 $event 作为参数 -->
             <cpn @item-click="cpnClick"></cpn>
         </div>
         
         <!-- 子组件模板 -->
         <template id="cpn">
             <div>
                 <button v-for="item in categories" @click="btnClick(item)">
                     {{item.name}}
                 </button>
             </div>
         </template>
         
         <script>
             //子组件
             const cpn = {
                 template: '#cpn',
                 data() {
                     return {
                         categories: [
                             {id: 'aaa', name: '热门推荐'},
                             {id: 'bbb', name: '手机数码'},
                             {id: 'ccc', name: '家用家电'},
                             {id: 'ddd', name: '电脑办公'}
                         ]
                     }
                 },
                 methods: {
                     btnClick(item){
                         //发射事件：自定义事件
                         this.$emit('item-click', item);
                     }
                 }
             }
         
             //父组件
             const app = new Vue({
                 el: '#app',
                 components: {
                     cpn
                 },
                 methods: {
                     cpnClick(item){
                         console.log('cpnclick', item);
                     }
                 }
             })
         </script>
         ```

         `v-on` 不仅可以监听 DOM 事件，也可以用于组件之间的自定义事件。自定义事件流程：

         1. 在子组件中，通过 `$emit()` 来触发事件。
         2. 在父组件中通过 `v-on` 来监听子组件事件。

   3. `watch` ：监听属性改变；`watch` 可以充当组件对象的另一个属性，属性值为一个对象，对象里面装着方法，方法名就是我们要监听的属性名，该方法可以接受两个参数：第一个为 newValue ，第二个为oldValue。

8. 父子组件的访问方式：有时候我们需要父组件之间访问子组件，子组件直接访问父组件或根组件。

   1. 父组件访问子组件：使用 `$children` 或 `$refs` 

      ```html
      <div id="app">
          <h2>我是父组件</h2>
          <cpn></cpn>
          <cpn ref="aaa"></cpn>
          <cpn></cpn>
          <!-- 监听点击事件 -->
          <button @click="btnClick">按钮</button>
      </div>
      
      <template id="cpn">
          <div>
              <h2>我是子组件</h2>
          </div>
      </template>
      
      <script>
          const app = new Vue({
              el: '#app',
              data: {
      
              },
              //注册子组件
              components: {
                  cpn: {
                      template: '#cpn',
                      methods: {
                          showMessage(){
                              console.log('showMessage');
                          }
                      }
                  }
              },
              methods: {
                  btnClick() {
                      //$children方式
                      console.log(this.$children);	//得到一个数组，每个元素为子组件
                      this.$children[0].showMessage();  //引用第一个子组件的showMessage
                      
                      //$refs 方式
                      console.log(this.$refs);	//得到一个对象，每个属性为一个子组件对象
                      this.$refs.aaa.showMessage();  //引用ref为aaa的子组件的showMessage
                  }
              }
          })
      </script>
      ```

      通常使用 `$refs` 方式访问，因为 `$children` 需要使用索引值，若改变次序（如在前面插入一个子组件），需要更换下标值。

   2. 子组件访问父组件，使用 `$parent` ；访问根属性，使用 `$root`，原理跟上面代码相似，但正常开发时不建议使用，因为组件应该是可以复用的，若访问父组件的内容，耦合性太高，不利于组件的复用。

9. 事件总线：在Vue中可以使用 `EventBus` 来作为沟通桥梁，就像是所有组件共用相同的事件中心，可以向该中心注册发送事件或接收事件，若两个组件不是父子组件，或者关系太远，我们需要事件总线（本质上是一个空的 Vue 实例）来传输数据：

   ```html
   <div id="app">
       <cpn1></cpn1>
       <cpn2></cpn2>
   </div>
   
   <template id="cpn1">
       <div>
           我是商家
           <input type="text" ref="mytext">
           <button @click="handleClick">发送</button>
       </div>
   </template>
   
   <template id="cpn2">
       <div>
           我是用户，{{user}}
       </div>
   </template>
   
   <script>
       const bus = new Vue()   //事件总线
   
       Vue.component('cpn1', {
           template: '#cpn1',
           methods: {
               handleClick() {
                   //发送事件，由于ref属性添加给DOM元素，则$refs指向一个对象，每个属性是原生DOM对象
                   bus.$emit('sendMessage', this.$refs.mytext.value);
               }
           }
       })
   
       Vue.component('cpn2', {
           template: '#cpn2',
           data() {
               return {
                   user: '未收到'
               }
           },
           //生命周期函数，一旦创建好节点就执行
           mounted() {
               //监听 sendMessage 事件
               bus.$on('sendMessage', (data) => {
                   this.user = '已收到' + data
               })
           }
       })
   
       const app = new Vue({
           el: '#app'
       })
   </script>
   ```

10. slot 组件插槽（已过时）：组件的插槽让我们封装的组件更加具有扩展性，可以让我们决定组件内部要展示的内容。封装组件的时候，我们需要把相同的地方封装到组件中，把不同的地方暴露为插槽。

    ```html
    <div id="app">	
        <!-- 在cpn标签里面写的代码即代替插槽，使组件有更高的扩展性 -->
        <cpn></cpn>	
        <cpn><span>新增</span></cpn>
        <cpn><i>斜体</i></cpn>
    </div>
    
    <template id="cpn">
        <div>
            <h2>标题</h2>
            <p>内容内容内容内容内容</p>
            <!-- 插槽中为默认值（可选），若使用组件时不传入，则使用默认值 -->
            <slot><button>按钮</button></slot>
        </div>
    </template>
    
    <script>
        const app = new Vue({
            el: '#app',
            components: {
                cpn: {
                    template: '#cpn',
                }
            }
        })
    </script>
    ```

    具名插槽的使用：

    ```html
    <div id="app">
        <cpn></cpn>
        <!-- 左边 中间 右边 -->
        
        <!-- 修改name属性为center的插槽-->
        <cpn><span slot="center">标题</span></cpn>
        <!-- 左边 标题 右边 -->
        
        <!-- 不加name属性不能修改具名插槽，只能修改不具名插槽 -->
        <cpn><span>新的</span></cpn>
        <!-- 左边 中间 右边 新的 -->
    </div>
    
    <template id="cpn">
        <div>
            <!-- 给插槽添加name属性 -->
            <slot name="left"><span>左边</span></slot>
            <slot name="center"><span>中间</span></slot>
            <slot name="right"><span>右边</span></slot>
            <slot></slot>
        </div>
    </template>
    
    <script>
        const app = new Vue({
            el: '#app',
            components: {
                cpn: {
                    template: '#cpn',
                }
            }
        })
    </script>
    ```

