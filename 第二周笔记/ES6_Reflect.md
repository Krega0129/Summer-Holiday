###### Reflect

1. `Reflect`对象与`Proxy`对象一样，也是 ES6 为了操作对象而提供的新 API。`Reflect`对象的设计目的有这样几个。

   1.  将`Object`对象的一些明显属于语言内部的方法（比如`Object.defineProperty`），放到`Reflect`对象上。

   2. 修改某些`Object`方法的返回结果，让其变得更合理。比如，`Object.defineProperty(obj, name, desc)`在无法定义属性时，会抛出一个错误，而`Reflect.defineProperty(obj, name, desc)`则会返回`false`。

      ```javascript
      // 老写法
      try {
        Object.defineProperty(target, property, attributes);
        // success
      } catch (e) {
        // failure
      }
      
      // 新写法
      if (Reflect.defineProperty(target, property, attributes)) {
        // success
      } else {
        // failure
      }
      ```

   3. 让`Object`操作都变成函数行为。某些`Object`操作是命令式，比如`name in obj`和`delete obj[name]`，而`Reflect.has(obj, name)`和`Reflect.deleteProperty(obj, name)`让它们变成了函数行为。

      ```javascript
      // 老写法
      'assign' in Object // true
      
      // 新写法
      Reflect.has(Object, 'assign') // true
      ```

   4. `Reflect`对象的方法与`Proxy`对象的方法一一对应，只要是`Proxy`对象的方法，就能在`Reflect`对象上找到对应的方法。

2. 静态方法：`Reflect`对象一共有 13 个静态方法。

   - Reflect.apply(target, thisArg, args)
   - Reflect.construct(target, args)
   - Reflect.get(target, name, receiver)
   - Reflect.set(target, name, value, receiver)
   - Reflect.defineProperty(target, name, desc)
   - Reflect.deleteProperty(target, name)
   - Reflect.has(target, name)
   - Reflect.ownKeys(target)
   - Reflect.isExtensible(target)
   - Reflect.preventExtensions(target)
   - Reflect.getOwnPropertyDescriptor(target, name)
   - Reflect.getPrototypeOf(target)
   - Reflect.setPrototypeOf(target, prototype)

3. `Reflect.get(target, name, receiver)` ：`Reflect.get`方法查找并返回`target`对象的`name`属性，如果没有该属性，则返回`undefined`。如果`name`属性部署了读取函数（getter），则读取函数的`this`绑定`receiver`。如果第一个参数不是对象，`Reflect.get`方法会报错。

4. `Reflect.set(target, name, value, receiver)`：`Reflect.set`方法设置`target`对象的`name`属性等于`value`。如果`name`属性设置了赋值函数，则赋值函数的`this`绑定`receiver`。如果第一个参数不是对象，`Reflect.set`会报错。【注意】：如果 `Proxy`对象和 `Reflect`对象联合使用，前者拦截赋值操作，后者完成赋值的默认行为，而且传入了`receiver`，那么`Reflect.set`会触发`Proxy.defineProperty`拦截。

   ```javascript
   let p = {
     a: 'a'
   };
   
   let handler = {
     set(target, key, value, receiver) {
       console.log('set');
       Reflect.set(target, key, value, receiver)
     },
     defineProperty(target, key, attribute) {
       console.log('defineProperty');
       Reflect.defineProperty(target, key, attribute);
     }
   };
   
   let obj = new Proxy(p, handler);
   obj.a = 'A';
   // set
   // defineProperty
   ```

   上面代码中，`Proxy.set`拦截里面使用了`Reflect.set`，而且传入了`receiver`，导致触发`Proxy.defineProperty`拦截。这是因为`Proxy.set`的`receiver`参数总是指向当前的`Proxy`实例（即上例的`obj`），而`Reflect.set`一旦传入`receiver`，就会将属性赋值到`receiver`上面（即`obj`），导致触发`defineProperty`拦截。如果`Reflect.set`没有传入`receiver`，那么就不会触发`defineProperty`拦截。

5. `Reflect.has(target, name)`：`Reflect.has`方法对应`name in obj`里面的`in`运算符。如果`Reflect.has()`方法的第一个参数不是对象，会报错。

6. `Reflect.deleteProperty`：`Reflect.deleteProperty`方法等同于`delete obj[name]`，用于删除对象的属性。该方法返回一个布尔值。如果删除成功，或者被删除的属性不存在，返回`true`；删除失败，被删除的属性依然存在，返回`false`。如果`Reflect.deleteProperty()`方法的第一个参数不是对象，会报错。