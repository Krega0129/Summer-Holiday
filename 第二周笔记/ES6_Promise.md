1. Promise：Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。

   1. `Promise`构造函数接受一个函数作为参数，该函数的两个参数分别是`resolve`和`reject`（可选）。`resolve`函数的作用是，将`Promise`对象的状态从“未完成”变为“成功”，在异步操作成功时调用，并将异步操作的结果作为参数传递出去；`reject`函数的作用是，将`Promise`对象的状态从“未完成”变为“失败”，在异步操作失败时调用，并将异步操作报出的错误作为参数传递出去。

   2. `Promise`实例生成以后，可以用`then`方法分别指定`resolved`状态和`rejected`状态的回调函数。`then`方法可以接受两个回调函数作为参数。第一个回调函数是`Promise`对象的状态变为`resolved`时调用，第二个回调函数是`Promise`对象的状态变为`rejected`时调用。其中，第二个函数是可选的，不一定要提供。这两个函数都接受`Promise`对象传出的值作为参数。见vscode中 `ES6_Promise.html`。

   3. 应用于Ajax函数封装：见vscode中 `ES6_Promise封装Ajax函数.html`。

   4. **Promise.prototype.then**：调用 then 方法的返回结果为 Promise 对象，对象状态由回调函数执行结果决定：

      1. 若回调函数中返回的结果是 **非**Promise 类型的属性，状态为成功，返回值为对象的成功的值。
      2. 若回调函数中返回的结果是 Promise 对象，内部返回的 Promise 状态决定了 then 方法返回的 Promise 对象的状态（返回的对象成功，返回值对象也为成功，且成功的值相同）
      3. 由于 then 方法可以返回一个 Promise 对象，所以 then 可以进行链式调用。

   5. **Promise.prototype.catch**：`Promise.prototype.catch()`方法是`.then(null, rejection)`或`.then(undefined, rejection)`的别名，用于指定发生错误时的回调函数。`then()`方法指定的回调函数，如果运行中抛出错误，也会被`catch()`方法捕获。`Promise.prototype.catch()`也可以进行链式调用，后一个catch捕获前一个catch的错误。

   6. **Promise.prototype.finally**：`finally()`方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。`finally`方法的回调函数不接受任何参数，这表明，`finally`方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果。`finally()`方法的实现：

      ```javascript
      Promise.prototype.finally = function (callback) {
        let P = this.constructor;
        return this.then(
          value  => P.resolve(callback()).then(() => value),
          reason => P.resolve(callback()).then(() => { throw reason })
        );
      };
      ```

      从上面的实现还可以看到，`finally`方法总是会返回原来的值。

      ```javascript
      // resolve 的值是 undefined
      Promise.resolve(2).then(() => {}, () => {})
      
      // resolve 的值是 2
      Promise.resolve(2).finally(() => {})
      
      // reject 的值是 undefined
      Promise.reject(3).then(() => {}, () => {})
      
      // reject 的值是 3
      Promise.reject(3).finally(() => {})
      ```

   7. **Promise.prototype.all**：`Promise.all()`方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。如果参数不是Promise实例，就会先调用`Promise.resolve`方法，将参数转为 Promise 实例。`Promise.all()`方法的参数必须具有 Iterator 接口，且返回的每个成员都是 Promise 实例。

      ```javascript
      const p = Promise.all([p1, p2, p3]);
      ```

      `p`的状态由`p1`、`p2`、`p3`决定，分成两种情况。

      1. 只有`p1`、`p2`、`p3`的状态都变成`fulfilled`，`p`的状态才会变成`fulfilled`，此时`p1`、`p2`、`p3`的返回值组成一个数组，传递给`p`的回调函数。
      2. 只要`p1`、`p2`、`p3`之中有一个被`rejected`，`p`的状态就变成`rejected`，此时第一个被`reject`的实例的返回值，会传递给`p`的回调函数。

   8. **Promise.prototype.race**：`Promise.race()`方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例，`Promise.race()`方法的参数与`Promise.all()`方法一样。

      ```javascript
      const p = Promise.race([p1, p2, p3]);
      ```

      只要`p1`、`p2`、`p3`之中有一个实例率先改变状态，`p`的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给`p`的回调函数。

   9. **Promise.resolve**：将现有对象转为 Promise 对象。等价于：

      ```javascript
      Promise.resolve('foo')
      // 等价于
      new Promise(resolve => resolve('foo'))
      ```

      `Promise.resolve`方法的参数分成四种情况：

      1. 如果参数是 Promise 实例，那么`Promise.resolve`将不做任何修改、原封不动地返回这个实例。
      2. 参数是一个`thenable`对象，`Promise.resolve`方法会将这个对象转为 Promise 对象，然后就立即执行`thenable`对象的`then`方法。
      3. 如果参数是一个原始值，或者是一个不具有`then`方法的对象，则`Promise.resolve`方法返回一个新的 Promise 对象，状态为`resolved`。
      4. `Promise.resolve()`方法允许调用时不带参数，直接返回一个`resolved`状态的 Promise 对象。

   10. **Promise.reject**：`Promise.reject(reason)`方法也会返回一个新的 Promise 实例，该实例的状态为`rejected`。注意，`Promise.reject()`方法的参数，会原封不动地作为`reject`的理由，变成后续方法的参数。这一点与`Promise.resolve`方法不一致。

       ```javascript
       const thenable = {
         then(resolve, reject) {
           reject('出错了');
         }
       };
       
       Promise.reject(thenable)
       .catch(e => {
         console.log(e === thenable);	// true
       })
       ```

2. `Promise.allSettled()` ：状态始终是成功的，参数为一个数组，每个元素为一个Promise对象，调用后的值也为一个数组，每个元素是一个对象，属性是参数中promise对象的状态和值。与`Promise.all()` 类似，但是`Promise.all()` 的状态由参数的状态决定，若都为成功，则成功，值为一个数组，每个元素是参数中promise对象的值。若其中有一个失败，则为失败，值为失败的那个参数的值。