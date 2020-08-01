###### 路由及Vuex

1. URL的 hash 和 HTML5 的 history。

   1. URL的 hash

      1. URL的 hash 也就是锚点(#) ，本质上是改变 window.location 的href 属性。
      2. 我们可以通过直接赋值 `location.hash` 来改变href，但是页面不发生刷新。

   2. HTML5 的 history 模式：

      1. pushState(data, title, ?url)：第一个参数是对象，第二个传入title，第三个是url。

         ```js
         history.pushState({}, '', 'home')
         ```

         相当于向栈中压入一个url。

      2. history.back()：相当于后退一步，即栈中弹出一个url。

      3. history.forward()：前进一步，即往栈中压入一个url。

      4. history.go()：

         1. 当参数为 -1 ，相当于 history.back()
         2. 当参数为0，刷新页面
         3. 当参数为1，相当于history.forward()

2. vue-router：

   1. 配置：

      ```js
      import Vue from 'vue'
      import VueRouter from 'vue-router'
      
      //通过Vue.use(插件)，安装插件
      Vue.use(VueRouter)
      
      //配置路由映射：组件和路由映射配置
      const routes = [
        
      ]
      //创建VueRouter对象
      const router = new VueRouter({
        //history模式，若不加，则默认为hash模式（/#/）
        mode: 'history',
        routes
      })
      
      //将router对象导出，传入到Vue实例中
      export default router
      ```

   2. 使用：

      1. 创建路由组件
      2. 配置路由映射：组件和路由映射配置
      3. 使用路由：通过 `<router-link>` 和 `<router-view>`。

   3. 重定向：redirect

      ```js
      const routes = [
        {
          //重定向，path中可以不写，页可以写 '/',为根目录
          path: '',
          redirect: '/center'
        }
      ]
      ```

      当我们刚进入网页（或设置其他值）时，会重定向至指定组件并渲染出来。

   4. `<router-link>` 属性：

      1. to：指定跳转的路径，如 `to="/home"`。

      2. tag：`<router-link>` 默认渲染成 `<a>` 标签，而 `tag` 标签后面的标签可以指定渲染成什么标签。如 `tag="button"`。

      3. replace：replace不会留下 history 记录，不会用后退键返回上一页。后面不需要加属性值。

      4. activeClass（或 active-class）：当 `<router-link>` 对应的路由匹配成功时，会自动给当前元素设置一个 router-link-active 的 class，设置 activeClass 可以修改默认的名称。可以用于高亮显示等。也可以直接在 router 文件夹下的 index.js 中的创建的 VueRouter 对象里添加 `linkActiveClass: 'active'`。

         ```js
         const router = new VueRouter({
           mode: 'history',
           routes,
           linkActiveClass: 'active'
         })
         ```

   5. `<router-view>`：路由容器，相当于插槽一样使用。

   6. 编程式导航： `$router` 指向 router 对象，我们可以使用对象里的 push 方法将路径压入栈中。若不想保留history历史，可以使用该对象的 replace 方法。

      ```js
      methods: {
        homeClick(){
          this.$router.push('/home')
        },
        aboutClick(){
          this.$router.replace('/about')
        }
      }
      ```

   7. 动态路由：在某些情况下，一个页面的 path 路径可能是不确定的，比如我听不同的歌曲，路径是 `/id=...`，这种 path 和 component 的关系，称之为动态路由。

      ```js
      const routes = [
        {
          path: '/user/:userId',
          component: User
        }
      ]
      ```

      userId 只是充当一个变量，可以传入想要的路径。

      【注】：`$route` 指向的是**当前活跃**的路由。

   8. 路由的懒加载：

      1. 当打包构建应用时，js包会变得非常大，影响页面加载。如果我们把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，就能更高效。

      2. 路由懒加载主要的作用是将路由对应的组件打包成一个个js代码块，只有路由被访问到的时候才加载对应的组件。

      3. 方式：

         1. 结合 Vue 的异步组件和 Webpack 的代码分析（了解）：

            ```js
            const Home = resolve => { require.ensure(['../components/Home.vue'], () => { resolve(require('../components/Home.vue')) })};
            ```

         2. AMD 写法：

            ```js
            const About = resolve => require(['../components/About.vue'], resolve);
            ```

         3. ES6 中，我们可以有更简单的写法来组织 Vue 异步组件和 Webpack 的代码分割：

            ```js
            const Home = () => import('../component/Home.vue');
            ```

   9. 嵌套路由：一级路由的基础上，在 routes 里面对应的对象中加入 children 属性，该属性值为一个数组，与routes 格式基本一致，就是路径格式不一样，children 里面的路径 前面不需要加 `/` 。当加上二级路由后，我们需要在父组件的模板中的对应位置加上一个 路由容器 `<router-view></router-view>` ，

      1. 创建对应子组件，并且在路由映射中配置对应的子路由。
      2. 在组件内部使用 `<route-view>`。

   10. 传递参数：传递参数主要有两种类型：params 和 query。

       1. params 类型（动态路由）：

          1. 配置路由格式：`/router/:id`
          2. 传递的方式：在 path 后面跟上对应的值
          3. 传递后形成的路径：`/router/123`，`/router/abc`

          ```vue
          <!-- 用 <router-link>传递 -->
          <router-link :to="'/user/' + userId" tag="button">用户</router-link>
          
          <!-- 用 <button>传递 -->
          <button @click="userClick">用户</button>
          <!-- 在methods里面 -->
          <script>
          	export default {
                  methods:{
                      userClick() {
          				this.$router.push('/user/' + this.userId)
                      }
                  }
              }
          </script>
          ```

       2. query 类型：

          1. 配置路由格式：`/router`，也就是普通配置
          2. 传递的方式：对象中使用 query 的 key 作为传递方式
          3. 传递后形成的路径：`/router?id=123`，`/router?id=abc`

          ```vue
          <!-- 用 <router-link>传递 -->
          <router-link :to="{path: '/profile', query: {name: 'Mike', age: 18}}">
              
          <!-- 用 <button>传递 -->
          <button @click="profileClick">档案</button>
          <!-- 在methods里面 -->
          <script>
          	export default {
                  methods:{
                      profileClick() {
          				this.$router.push({
                              path: '/profile',
                              query: {
                                  name: 'Mike',
                                  age: 18
                              }
                          })
                      }
                  }
              }
          </script>
          ```

       params 和 query 传递的参数可以用 `(this.)$route.params/query` 获取。

   11. 全局导航守卫：监听路由的跳转，在跳转时执行相应代码。如跳转时修改标题：

       ```js
       //在router的index.js上添加,其中next是内部定义好的函数,要调用next才能继续执行
       router.beforeEach((to, from, next) => {
         //从from跳转到to
         document.title = to.matched[0].meta.title;
         next();
       })
       ```

   12. 导航守卫：

       1. vue-router 提供的导航守卫主要来监听路由的进入和离开的

       2. vue-router 提供了 beforeEach 和 afterEach 的钩子函数，他们会在路由即将改变和改变后触发。

       3. 参数：

          1. **`to: Route`**: 即将要进入的目标 [路由对象](https://router.vuejs.org/zh/api/#路由对象)
          2. **`from: Route`**: 当前导航正要离开的路由
          3. **`next: Function`**: 一定要调用该方法来 **resolve** 这个钩子。执行效果依赖 `next` 方法的调用参数。
             - **`next()`**: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 **confirmed** (确认的)。
             - **`next(false)`**: 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 `from` 路由对应的地址。
             - **`next('/')` 或者 `next({ path: '/' })`**: 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 `next` 传递任意位置对象，且允许设置诸如 `replace: true`、`name: 'home'` 之类的选项以及任何用在 [`router-link` 的 `to` prop](https://router.vuejs.org/zh/api/#to) 或 [`router.push`](https://router.vuejs.org/zh/api/#router-push) 中的选项。
             - **`next(error)`**: (2.4.0+) 如果传入 `next` 的参数是一个 `Error` 实例，则导航会被终止且该错误会被传递给 [`router.onError()`](https://router.vuejs.org/zh/api/#router-onerror) 注册过的回调。

       4. 全局解析守卫：你可以用 `router.beforeResolve` 注册一个全局守卫。这和 `router.beforeEach` 类似，区别是在导航被确认之前，**同时在所有组件内守卫和异步路由组件被解析之后**，解析守卫就被调用。

       5. 全局后置钩子：你也可以注册全局后置钩子，然而和守卫不同的是，这些钩子不会接受 `next` 函数也不会改变导航本身：

          ```js
          router.afterEach((to, from) => {
            // ...
          })
          ```

       6. 路由独享的守卫：你可以在路由配置上直接定义 `beforeEnter` 守卫，这些守卫与全局前置守卫的方法参数是一样的。

          ```js
          const router = new VueRouter({
            routes: [
              {
                path: '/foo',
                component: Foo,
                beforeEnter: (to, from, next) => {
                  // ...
                }
              }
            ]
          })
          ```

       7. 组件内的守卫：最后，你可以在路由组件内直接定义以下路由导航守卫：

          1. `beforeRouteEnter`
          2. `beforeRouteUpdate` (2.2 新增)
          3. `beforeRouteLeave`

          ```js
          const Foo = {
            template: `...`,
            beforeRouteEnter (to, from, next) {
              // 在渲染该组件的对应路由被 confirm 前调用
              // 不！能！获取组件实例 `this`
              // 因为当守卫执行前，组件实例还没被创建
            },
            beforeRouteUpdate (to, from, next) {
              // 在当前路由改变，但是该组件被复用时调用
              // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
              // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
              // 可以访问组件实例 `this`
            },
            beforeRouteLeave (to, from, next) {
              // 导航离开该组件的对应路由时调用
              // 可以访问组件实例 `this`
            }
          }
          ```

          `beforeRouteEnter` 守卫 **不能** 访问 `this`，因为守卫在导航确认前被调用，因此即将登场的新组件还没被创建。

          不过，你可以通过传一个回调给 `next`来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。

          ```js
          beforeRouteEnter (to, from, next) {
            next(vm => {
              // 通过 `vm` 访问组件实例
            })
          }
          ```

          注意 `beforeRouteEnter` 是支持给 `next` 传递回调的唯一守卫。对于 `beforeRouteUpdate` 和 `beforeRouteLeave` 来说，`this` 已经可用了，所以**不支持**传递回调，因为没有必要了。

          ```js
          beforeRouteUpdate (to, from, next) {
            // just use `this`
            this.name = to.params.name
            next()
          }
          ```

          这个离开守卫通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 `next(false)` 来取消。

          ```js
          beforeRouteLeave (to, from, next) {
            const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
            if (answer) {
              next()
            } else {
              next(false)
            }
          }
          ```

       8. 完整的解析流程：

          1. 导航被触发。
          2. 在失活的组件里调用 `beforeRouteLeave` 守卫。
          3. 调用全局的 `beforeEach` 守卫。
          4. 在重用的组件里调用 `beforeRouteUpdate` 守卫 (2.2+)。
          5. 在路由配置里调用 `beforeEnter`。
          6. 解析异步路由组件。
          7. 在被激活的组件里调用 `beforeRouteEnter`。
          8. 调用全局的 `beforeResolve` 守卫 (2.5+)。
          9. 导航被确认。
          10. 调用全局的 `afterEach` 钩子。
          11. 触发 DOM 更新。
          12. 用创建好的实例调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数。

   13. keep-alive：kep-alive 是 Vue 内置的一个组件，可以使被包含的组件保留状态，或避免重新渲染。

       1. `router-view` 也是一个组件，如果被包在 keep-alive 里面，所有路径匹配到的试图组件都会被缓存。
       2. 在 keep-alive 下，可以使用 `activated` 和 `deactivated` 周期函数。
       3. 属性：
          1. include：字符串或正则表达式，只有匹配的组件才会缓存；
          2. exclude：字符串或正则表达式，任何匹配的组件都不会被缓存；

3. Vuex：状态管理模式。采用 集中式存储管理 应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式变化，并且是响应式的。

   1. 单界面的状态管理：![](https://vuex.vuejs.org/flow.png)

      ​	

      1. state：状态（姑且可以当作data的属性）
      2. View：视图层，可以针对 State 的变化，显示不同的信息。
      3. Actions：这里的 Actions 主要是用户的各种操作：点击、输入等，可能会导致状态的改变。

   2. 多界面状态管理：将共享的状态抽取出来，交给 Vuex 同一管理，之后，每个视图按照规定好的规则进行访问和修改。![](https://vuex.vuejs.org/vuex.png)

      1. 我们可以通过 Actions 或 Mutations 对 state 中的数据进行修改。Mutations 可以跟踪我们对 State 的修改，但只能跟踪同步操作，Actions 主要是在我们处理处理异步操作的时候起作用。

      2. 使用步骤：

         1. 提取出一个公共的 Store 对象，用于保存在多个组件中共享的状态。
         2. 将 Store 对象放置在 new Vue 对象中，这样可以保证在所有的组件中都可以使用到。
         3. 在其他组件中使用 store 对象中保存的状态即可：
            1. 通过 `this.$store.state.属性` 的方式来访问状态；
            2. 通过 `this.$store.commit('mutations中的方法')` 来修改状态；
         4. 【注】我们通过提交 mutations 的方式，而非直接改变 store.state.属性，因为这样 Vuex 可以更明确地跟踪状态的变化。

      3. 核心概念：

         1. State： 存储状态相关的信息

         2. Getters：相当于计算属性，默认传入参数 State，可以传入 Getters 作为第二个参数。

         3. Mutations：Vuex 的 store 状态更新的唯一方式： 提交 Mutations，Mutations 包括字符串的事件类型和一个回调函数，该函数的第一个参数为state。当我们提交时想传入其他参数，则需要：

            `this.$store.commit('事件类型', 参数)`，若传入多个参数，则传入一个对象。另一种提交风格：

            ```js
            this.store.commit({
            	type: '事件属性',
            	参数...
            })
            
            //此时mutation里面的写法中，第二个参数为提交的对象：
            属性名 (state, payload){
                payload.属性
            }
            ```

            Mutation相应规则：Vuex 的 store 中的 state 是响应式的，当 state 中的数据发生改变时，Vue 组件会自动更新，但是我们需要注意：

            1. 提前在 store 中 **初始化** 对应的属性
            2. 当我们给 state 添加属性时，不要直接用 state.新属性 进行增加，我们可以：
               1. 使用 `Vue.set(obj, 'newProp', value)` ；
               2. 用新对象给旧对象重新赋值
            3. 当我们删除属性时，用 `Vue.delete(obj, 'prop')`；

         4. Actions：Actions 类似于 Mutations，但 Actions 能处理异步操作。我们需要先把异步操作 dispatch 到 Action，再由 Action commit 到 Mutations：

            ```js
            //dispatch 到 Actions
            fu() {
                this.$store.dispatch('事件属性')
            }
            
            //commit 到 Mutations
            fn1(context) {
                setTimeout(() => {
                    context.commit('事件属性')
                }, 1000)
            }
            ```

            context 代表上下文，此处代表store，如果在模块里面，则代表当前模块。

         5. Modules：Vuex 使用单一状态树，意味着 Vuex 需要管理很多东西，看起来十分复杂臃肿，Vuex 支持我们把 store 分割成模块（module），每个模块里面拥有自己的 state, Mutations, Actions 和 Getters 等。

            1. 参数：
               1. state：当前模块的 state；
               2. getters：当前模块的 getters；
               3. rootState：根模块（store）的 state；