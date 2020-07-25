1. class类：ES6 提供了更接近传统语言的写法，引入了 Class（类）这个概念，作为对象的模板。通过`class`关键字，可以定义类。它的绝大部分功能，ES5 都可以做到，新的`class`写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。
   1. 声明：类相当于构造函数的而另一种写法，方法之间不需要逗号分隔。

      ```js
      class Phone{
          //构造方法
          constructor(brand,price){
              this.brand = brand;
              this.price = price;
          }
          //方法与方法之间不需要逗号隔开
          call(){
          	console.log('打电话');
          }
      }
      ```

   2. 静态成员：

      1. 静态方法：类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上`static`关键字，就表示该方法**不会被实例继承**，而是直接通过类来调用，这就称为“静态方法”。

      2. 静态属性：静态属性指的是 Class 本身的属性，即`Class.propName`，而不是定义在实例对象（`this`）上的属性。

         ```js
         class Paper{
             constructor(brand, price){
                 this.brand = brand;
                 this.price = price;
             }
             static producer = 'China';  //静态属性
             static call(){      //静态方法
                 console.log('打电话');
             }
         }
         
         let paper = new Paper();
         console.log(paper.producer);       //undefined
         console.log(Paper.producer);       //China
         console.log(paper.call());	//Uncaught TypeError: paper.call is not a function
         console.log(Paper.call());  //打电话
         ```

   3. 类继承：

      ```js
      class Phone{
      //构造方法
          constructor(brand,price){
              this.brand = brand;
              this.price = price;
          }
      
          //父类得成员属性
          call(){
              console.log('打电话');
          }
      }
      
      class SmartPhone extends Phone{
          constructor(brand,price,color,size){
              super(brand,price); //相当于ES5中得 Phone.call(this,brand,price);
              this.color = color;
              this.size = size;
          }
      
          photo(){
          	console.log('拍照');
          }
      
          playGames(){
          	console.log('打游戏');
          }
      }
      ```

   4. getter和setter：与 ES5 一样，在“类”的内部可以使用`get`和`set`关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为。

      ```js
      class Phone{
          //class内可以没有构造方法
          get price(){
          	console.log('读取价格');
      	}
      
          set price(val){
          	console.log('修改价格为' + val);
          }
      }
      
      let p = new Phone();
      p.price;    //读取价格
      p.price = 2999; //修改价格为2999
      ```

   5. 注意点：

      1. 类和模块的内部，默认就是严格模式。

      2. 类不存在变量提升（hoist）。

      3. 由于本质上，ES6 的类只是 ES5 的构造函数的一层包装，所以函数的许多特性都被`Class`继承，包括`name`属性。

         ```js
         class Point {}
         Point.name // "Point"
         ```

         `name`属性总是返回紧跟在`class`关键字后面的类名。

      4. 如果某个方法之前加上星号（`*`），就表示该方法是一个 Generator 函数。

      5. 类的方法内部如果含有`this`，它默认指向类的实例。

   6. 私有属性：在类里面的属性名前面加上 `#` 即为私有属性，在class外部不能被访问。只能在class内部使用（可以用于方法里面让外部访问）。