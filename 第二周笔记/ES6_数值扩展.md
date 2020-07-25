###### 数值扩展：

1. Number.EPSILON 是 JavaScript 表示的最小精度，误差如果小于这个值，就可以认为已经没有意义了，即不存在误差了。主要用于浮点数计算。

   ```javascript
   function withinErrorMargin (left, right) {
     return Math.abs(left - right) < Number.EPSILON;
   }
   
   0.1 + 0.2 === 0.3 // false
   withinErrorMargin(0.1 + 0.2, 0.3) // true
   
   1.1 + 1.3 === 2.4 // false
   withinErrorMargin(1.1 + 1.3, 2.4) // true
   ```

2. 二进制和八进制：

   ```js
   let b = 0b1010;		//0b开头为二进制数
   let o = 0o777;		//0o开头为八进制数
   let d = 100;		//十进制数
   let x = 0xff;		//0x开头为十六进制数
   ```

3. `Number.isFinite()`用来检查一个数值是否为有限的（finite），是则返回true，否则（包括非数值）返回false。

4. `Number.isNaN()`用来检查一个值是否为`NaN`。

5. `parseInt()`和`parseFloat()`将字符串转为整数或者浮点数。

6. `Number.isInteger()`用来判断一个数值是否为整数。

   【注】JavaScript 内部，整数和浮点数采用的是同样的储存方法，所以 25 和 25.0 被视为同一个值。由于 JavaScript 采用 IEEE 754 标准，数值存储为64位双精度格式，**数值精度最多可以达到 53 个二进制位**（1 个隐藏位与 52 个有效位）。如果数值的精度超过这个限度，第54位及后面的位就会被丢弃，这种情况下，`Number.isInteger`可能会误判。

7. `Math.trunc()`将数字的小数点后面抹掉，若参数是非数值，`Math.trunc`内部使用`Number`方法将其先转为数值。

   【注】与 `parseInt()` 的区别：若参数是非数值，`Math.trunc`内部使用`Number`方法将其先转为数值。**当数字小于 `10e-6` ，自动转成科学计数法**，此时会出现不同：

   ```js
   console.log(parseInt(0.00000060));		//6
   console.log(Math.trunc(0.00000060));	//0
   ```

8. `Math.sign`方法用来判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。它会返回五种值：

   |  数值  | 返回值 |
   | :----: | :----: |
   |  正数  |   1    |
   |   0    |   0    |
   |   -0   |   -0   |
   |  负数  |   -1   |
   | 其他值 |  NaN   |

9. `**` 幂运算：与 `Math.pow()` 效果一样。

   ```js
   console.log(2 ** 10);	//1024
   console.log(Math.pow(2,10));	//1024
   ```

10. BigInt：大整型，由于Number有最大安全数值，超过这个数值无法显示，因此引入大整型来进行大数值运算。【注】BigInt类型的值只能和BigInt类型的值进行运算。

    ```js
    //声明
    let n = 123n;	//在数值后面加上n
    
    let m = 123;
    console.log(BigInt(m));		//123n，BigInt()的参数必须是整型，不然报错
    ```

11. 可选链操作符 `.?` ：之前我们要读取函数参数里的一个属性时，必须先一层一层判断是否传入和有无这个属性，用可选链操作符可以一步到位，若存在则获取成功，若不错在则为undefined；

    ```js
    console.log(obj.?arr);	//存在：输出obj.arr
    						//不存在：undefined
    ```