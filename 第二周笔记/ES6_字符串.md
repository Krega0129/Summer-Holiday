1. 模板字符串：ES6 引入新的声明字符串的方式  ``(反引号)

   1. 声明：

      ```js
      let str = `I am a string`;
      console.log(str + ',' + typeof str); //I am a string,string
      ```

   2. 内容中可以直接出现换行符：输出格式也会换行。

   3. 变量拼接：用`${}`拼接

      ```js
      let sport = 'football';
      let str = `I like play ${sport}!`;
      console.log(str);	//I like play football!
      ```

2. 字符串的 Unicode 表示法：

   1. ES6 加强了对 Unicode 的支持，允许采用`\uxxxx`形式表示一个字符，其中`xxxx`表示字符的 Unicode 码点（限于码点在`\u0000`~`\uFFFF`之间的字符）。
   2. 如果直接在`\u`后面跟上超过`0xFFFF`的数值（比如`\u20BB7`），JavaScript 会理解成`\u20BB+7`。由于`\u20BB`是一个不可打印字符，所以只会显示一个空格，后面跟着一个`7`。ES6 对这一点做出了改进，只要将码点放入大括号，就能正确解读该字符。

3. 字符串扩展：

   1. `trim()` ：清除字符串两侧空白。
   2. `trimStart()` ：清除字符串左侧空白。
   3. `trimEnd()` ：清除字符串右侧空白。