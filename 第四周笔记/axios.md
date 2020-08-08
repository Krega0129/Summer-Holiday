### axios：前端最流行的 ajax 请求库；

2. 特点：

   1. 基于 promise 的异步 ajax 请求库；
   2. 浏览器端/node端都可以使用；
   3. 支持请求/响应拦截器；
   4. 支持请求取消；
   5. 请求/响应数据转换；
   6. 批量发送多个请求；

3. 常用语法：

   1. axios(config)：通用/最本质的发任何类型请求的方式
   2. axios(url[, config])：可以只指定 url 发 get 请求
   3. axios.request(config)：等同于 axios(config)
   4. axios.get(url[, config])：发 get 请求
   5. axios.delete(url[, config])：发 delete 请求
   6. axios.post(url[, data, config])：发 post 请求
   7. axios.put(url[,  data, config])：发 put 请求
   8. axios.default.xxx：请求的默认全局配置
   9. axios.interceptors.request.use()：添加请求拦截器
   10. axios.interceptors.response.use()：添加响应拦截器
   11. axios.create([config])：创建一个新的 axios(没有下面的功能)
   12. axios.Cancel()：用于创建取消请求的错误对象
   13. axios.CancelToken()：用于创建取消请求的 token 对象
   14. axios.isCancel()：是否是一个取消请求的错误
   15. axios.all(promises)()：用于批量执行多个异步请求
   16. axios.spread()：用来指定接收所有成功数据的回调函数或方法

3. timeout 属性：单位为毫秒，请求超过该时间则中断请求；

4. 拦截器：

   1. 请求拦截器：在请求发送前进行一些操作，例如在每个请求体里加上token，统一做了处理如果以后要改也非常容易。相同的处理可以交给拦截器，不同的再另外处理，执行顺序：后添加先执行；
   2. 响应拦截器：在接收到响应后进行一些操作，例如在服务器返回登录状态失效，需要重新登录的时候，跳转到登录页。相同的处理可以交给拦截器，不同的再另外处理，执行顺序：先添加先执行；

   ```js
   axios.defaults.baseURL = 'http://musicapi.leanapp.cn';
   
   //请求拦截器1
   axios.interceptors.request.use(
     config => {
       console.log('request1 onResolved');
       return config;
     },
     error => {
       console.log('request1 onRejceted')
       return Promise.reject(error);
     }
   )
   
   //请求拦截器2
   axios.interceptors.request.use(
     config => {
       console.log('request2 onResolved');
       return config;
     },
     error => {
       console.log('request2 onRejceted')
       return Promise.reject(error);
     }
   )
   
   //响应拦截器1
   axios.interceptors.response.use(
     response => {
       console.log('response1 onResolved');
       return response;
     },
     error => {
       console.log('response1 onRejceted')
       return Promise.reject(error);
     }
   )
   
   //响应拦截器2
   axios.interceptors.response.use(
     response => {
       console.log('response2 onResolved');
       return response;
     },
     error => {
       console.log('response2 onRejceted')
       return Promise.reject(error);
     }
   )
   
   axios.get('/playlist/hot').then(response => {
       console.log(response.data);
   })
   ```

   上面的代码中，执行顺序为：请求拦截器2 => 请求拦截器1 => 响应拦截器1 => 响应拦截器2 => 处理响应。

5. 移除拦截器：

   ```js
   //添加拦截器
   var myInterceptor = axios.interceptors.request.use(function () {/*...*/});
   
   //移除拦截器
   axios.interceptors.request.eject(myInterceptor);
   ```

6. `axios.create()` ：有时我们要请求的服务器不止一个，此时可以自定义 axios 实例：

   ```js
   //创建 axios 实例
   const instance = axios.create();
   
   //添加拦截器
   instance.interceptors.request.use(function () {/*...*/});
   ```

7. 取消请求：

   1. 方法1（使用 CancelToken.source 工厂方法创建 cancel token）：

      ```js
      var CancelToken = axios.CancelToken;
      var source = CancelToken.source();
       
      axios.get('/user/12345', {
      	cancelToken: source.token
      }).catch(function(thrown) {
      	if (axios.isCancel(thrown)) {
      		console.log('Request canceled', thrown.message);
       	} else {
        		// 处理错误
       	}
      });
       
      // 取消请求（message 参数是可选的）
      source.cancel('Operation canceled by the user.');
      ```

   2. 方法2（传递一个 executor 函数（可以写为箭头函数）到 CancelToken 的构造函数来创建 cancelToken）：

      ```js
      axios({
          url: '/playlist/hot',
          cancelToken: new axios.CancelToken(function executor(c) => {
              //c是内置的一个取消当前请求的函数，保存取消函数，在适当的时候调用
              this.cancel = c;
          })
      }).then(
          response => {
              this.cancel = null;
              console.log('请求成功',response.data);
          }, 
          error => {
              this.cancel = null;
              console.log('请求失败');
          }
      )
      ```

8. 封装：平时取消请求多用于在请求某个数据过程中，再发起另外一个请求，此时需要取消前一个请求：

   ```vue
   <template>
     <div>
       <button @click="gClick">GET</button>
       <button @click="pClick">POST</button>
       <button @click="cClick">cancel</button>
     </div>
   </template>
   
   <script>
   import axios from 'axios'
   
   var cancel = null;
   axios.defaults.baseURL = 'http://musicapi.leanapp.cn';
   
   //添加请求拦截器
   axios.interceptors.request.use(
     config => {
       //每次发起新的请求前先判断cancel是不是函数类型
       if(typeof cancel === 'function') {
         cancel('forced to cancel');
       }
       config.cancelToken = new axios.CancelToken(c => {
         cancel = c;
       })
       return config;
     }
   )
   
   //添加响应拦截器
   axios.interceptors.response.use(
     response => {
       //请求成功
       cancel = null;
       return response;
     },
     error => {
       //请求失败
       if(axios.isCancel(error)) { //如果error是cancel类型（请求被取消）
         console.log('request cancelled error', error.message);
         //中断promise链接
         return new Promise(() => {});
       }else { //请求错误
         cancel = null;
         //将错误向下传递
         // throw error;
         return Promise.reject(error);
       }
     }
   )
   
   export default {
     name: 'cpn',
     methods: {
       gClick() {
         axios({
           url: '/playlist/hot',
         }).then(
           response => {
             console.log('get successfully', response.data);
           },
           error => {  //只处理请求失败的情况，不处理取消请求的错误
             console.log('cancel failed', error.message);
           }
         )
       },
       pClick() {
         axios({
           url: '/playlist/hot',
           method: 'POST',
         }).then(
           response => {
             console.log('get successfully', response.data);
           },
           error => {
             console.log('cancel failed', error.message);
           }
         )
       },
       cClick() {
         if(typeof cancel === 'function') {
           cancel('forced to cancel');
         }else {
           console.log('no request to cancel');
         }
       }
     }
   }
   </script>
   ```

   