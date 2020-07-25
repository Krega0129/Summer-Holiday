1. Module（模块化）：模块化是指将一个大的程序文件，拆分成许多小的文件，然后将小的文件组合起来。
   1. 模块化的优势：

      1. 防止命名冲突；
      2. 代码复用；
      3. 高维护性；

   2. export 和 import。

      1. export：`export`命令用于规定模块的对外接口；

         1. 3种方式：

            ```js
            //分别暴露
            export let a = 2;
            export function fn(){}
            
            //统一暴露
            let b = 2;
            function fn1(){};
            export {b, fn1};
            
            //默认暴露
            export default {
            	name:'Mike'
            }
            ```

         2. 可以用as进行重命名。

         3. 一个模块只能有一个默认输出。

         4. 本质上，`export default`就是输出一个叫做`default`的变量或方法，然后系统允许你为它取任意名字，`export default`命令用在非匿名函数前，也是可以的。需要在 import 命名，不需要加{}。

            ```js
            //默认暴露
            export default function fn() { // 输出
              // ...
            }
            import fn from 'crc32'; // 输入，此处无需加{}，且名字可以与输出的函数名不一样
            
            // 分别暴露
            export function fn1() { // 输出
              // ...
            };
            import {fn1} from 'crc32'; // 输入
            ```

      2. import：`import`命令用于输入其他模块提供的功能。

         1. `import`命令具有提升效果，会提升到整个模块的头部，首先执行。

         2. 可以用as给变量重命名。

         3. `import`命令输入的变量都是只读的，因为它的本质是输入接口，不允许在加载模块的脚本里面改写接口。

         4. 由于`import`是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。

            ```js
            // 报错，用到了表达式
            import { 'f' + 'oo' } from 'my_module';
            
            // 报错，用到了变量
            let module = 'my_module';
            import { foo } from module;
            
            // 报错，用到了if结构
            if (x === 1) {
              import { foo } from 'module1';
            } else {
              import { foo } from 'module2';
            }
            ```

         5. 如果多次重复执行同一句`import`语句，那么只会执行一次，而不会执行多次。

         6. 用 `*` 进行整体加载。

   3. `import()` 函数：动态import，可以进行按需加载。返回一个promise对象。