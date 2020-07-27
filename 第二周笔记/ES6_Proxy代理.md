###### Proxy：代理

1. Proxy 用于修改某些操作的默认行为，可以理解成在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。

   ```js
   let proxy = new Proxy(target, handler)
   ```

   参数1：要拦截的目标对象；

   参数2：也是一个对象，用来定制拦截行为。

   ###### get:

   ```js
   let user = {
       name : 'Mike',
       age : 18
   };
   
   let pUser = new Proxy(user, {
       get(target, attr){
           return 1;	//若没有设置返回值，则下面输出pUser的属性值是undefined。
       }
   });
   
   console.log(pUser.name);	//1，
   console.log(pUser.age);		//1
   ```

   上面的代码中，配置对象有一个`get`方法，用来拦截对目标对象属性的访问请求。`get`方法的两个参数分别是目标对象和所要访问的属性。当我们访问属性的时候都会触发这个get函数，所以访问任何属性都是返回1。**注**：若 get 方法里面没有设置返回值，则访问 pUser 里面的属性输出为undefined。若要访问代理对象的值，则需要返回 `target[attr]`。

   ​		有时，我们会在对象上面设置内部属性，属性名的第一个字符使用下划线开头，表示这些属性不应该被外部使用。结合`get`和`set`方法，就可以做到防止这些内部属性被外部读写。

   ```javascript
   const handler = {
     get (target, key) {
       invariant(key, 'get');
       return target[key];
     },
     set (target, key, value) {
       invariant(key, 'set');
       target[key] = value;
       return true;
     }
   };
   function invariant (key, action) {
     if (key[0] === '_') {		//若以_开头，则抛出错误。
       throw new Error(`Invalid attempt to ${action} private "${key}" property`);
     }
   }
   ```

   ###### set(): 

   ​		`set`方法用来拦截某个属性的赋值操作，可以接受四个参数，依次为目标对象、属性名、属性值和 Proxy 实例本身，其中最后一个参数可选。

   ```js
   let validator = {
     set: function(obj, prop, value) {
       if (prop === 'age') {
         if (!Number.isInteger(value)) {
           throw new TypeError('The age is not an integer');
         }
         if (value > 200) {
           throw new RangeError('The age seems invalid');
         }
       }
   
       // 对于满足条件的 age 属性以及其他属性，直接保存
       obj[prop] = value;
     }
   };
   let person = new Proxy({}, validator);
   
   person.age = 100;
   
   person.age // 100
   person.age = 'young' // 报错
   person.age = 300 // 报错
   ```

   上面代码中，由于设置了存值函数`set`，任何不符合要求的`age`属性赋值，都会抛出一个错误，这是数据验证的一种实现方法。利用`set`方法，还可以数据绑定，即每当对象发生变化时，会自动更新 DOM。

   ​		`set`方法的第四个参数`receiver`，指的是原始的操作行为所在的那个对象，一般情况下是`proxy`实例本身。【注】：

   1. 如果目标对象自身的某个属性，不可写且不可配置，那么`set`方法将不起作用。
   2. 严格模式下，`set`代理返回`false`或者`undefined`，都会报错。

2. 【注】要使得`Proxy`起作用，必须针对`Proxy`实例进行操作，而不是针对目标对象进行操作。

3. Proxy支持的拦截操作：

   1. **get(target, propKey, receiver)**：拦截对象属性的读取，比如`proxy.foo`和`proxy['foo']`。
   2. **set(target, propKey, value, receiver)**：拦截对象属性的设置，比如`proxy.foo = v`或`proxy['foo'] = v`，返回一个布尔值。
   3. **has(target, propKey)**：拦截`propKey in proxy`的操作，返回一个布尔值。
   4. **deleteProperty(target, propKey)**：拦截`delete proxy[propKey]`的操作，返回一个布尔值。
   5. **ownKeys(target)**：拦截`Object.getOwnPropertyNames(proxy)`、`Object.getOwnPropertySymbols(proxy)`、`Object.keys(proxy)`、`for...in`循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而`Object.keys()`的返回结果仅包括目标对象自身的可遍历属性。
   6. **getOwnPropertyDescriptor(target, propKey)**：拦截`Object.getOwnPropertyDescriptor(proxy, propKey)`，返回属性的描述对象。
   7. **defineProperty(target, propKey, propDesc)**：拦截`Object.defineProperty(proxy, propKey, propDesc）`、`Object.defineProperties(proxy, propDescs)`，返回一个布尔值。
   8. **preventExtensions(target)**：拦截`Object.preventExtensions(proxy)`，返回一个布尔值。
   9. **getPrototypeOf(target)**：拦截`Object.getPrototypeOf(proxy)`，返回一个对象。
   10. **isExtensible(target)**：拦截`Object.isExtensible(proxy)`，返回一个布尔值。
   11. **setPrototypeOf(target, proto)**：拦截`Object.setPrototypeOf(proxy, proto)`，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
   12. **apply(target, object, args)**：拦截 Proxy 实例作为函数调用的操作，比如`proxy(...args)`、`proxy.call(object, ...args)`、`proxy.apply(...)`。
   13. **construct(target, args)**：拦截 Proxy 实例作为构造函数调用的操作，比如`new proxy(...args)`。

###### has()

`has`方法用来拦截`HasProperty`操作，即判断对象是否具有某个属性时，这个方法会生效。典型的操作就是`in`运算符。`has`方法可以接受两个参数，分别是目标对象、需查询的属性名。

```javascript
var handler = {
  has (target, key) {
    if (key[0] === '_') {
      return false;
    }
    return key in target;
  }
};
var target = { _prop: 'foo', prop: 'foo' };
var proxy = new Proxy(target, handler);
'_prop' in proxy // false
```

如果原对象的属性名的第一个字符是下划线，`proxy.has`就会返回`false`，从而不会被`in`运算符发现，从而隐藏某些属性。

