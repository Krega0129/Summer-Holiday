# Sass

###  一、使用变量

1. `Sass` 引入了变量，使用 `$` 符号来标识变量（老版本的 `sass` 使用 `!` 来标识变量）.

#### 1. 变量声明：

```scss
$highlight-color: #F90;
```

这时变量还没有生效，除非引用这个变量，变量可以在 `css` 规则块定义之外存在。当变量定义在 `css` 规则块内，那么该变量只能在此规则块内使用。

#### 2. 变量引用：

可以在 `css` 属性的标准值可存在的地方使用变量：

```scss
$highlight-color: #F90;
$highlight-border: 1px solid $highlight-color;
.selected {
  border: $highlight-border;
}

//编译后
.selected {
  border: 1px solid #F90;
}
```

#### 3. 变量命名：

`sass` 的变量名可以与 `css` 中的属性名和选择器名称相同，包括中划线和下划线。用中划线声明的变量可以使用下划线的方式引用，反之亦然。在 `sass` 的大 多数地方，中划线命名的内容和下划线命名的内容是互通的，除了变量，也包括对混合器和Sass函数的命名。但是在 `sass` 中纯 `css` 部分不互通，比如类名、ID或属性名。



### 二、嵌套css规则：

子元素的代码可以嵌套在父元素里面。

#### 1. 父选择器标识符&：

在使用一些伪类选择器如：`:hover`，`:first-child` 等，要使用父选择器标识符 `&` ，可以看成用父选择器替换了 `&` ：

```scss
article a {
  color: blue;
  &:hover { color: red }
}
```

同时父选择器标识符还有另外一种用法，你可以在父选择器之前添加选择器。举例来说，当用户在使用IE浏览器时，你会通过`JavaScript`在`<body>`标签上添加一个ie的类名，为这种情况编写特殊的样式如下：

```scss
#content aside {
  color: red;
  body.ie & { color: green }
}

//编译后
#content aside {color: red};
body.ie #content aside { color: green }
```

#### 2. 群组选择器的嵌套：

```scss
.container {
  h1, h2, h3 {margin-bottom: .8em}
}

//编译后
.container h1, .container h2, .container h3 { margin-bottom: .8em }

nav, aside {
  a {color: blue}
}

//编译后
nav a, aside a {color: blue}
```

需要特别注意群组选择器的规则嵌套生成的`css`。虽然`sass`让你的样式表看上去很小，但实际生成的`css`却可能非常大，这会降低网站的速度。

#### 3. 子组合选择器和同层组合选择器：>、+、和~

```scss
article {
  //选择所有跟在article后的同层article元素
  ~ article { border-top: 1px dashed #ccc }
  //只选择article下紧跟着的子元素中命中section选择器的元素。
  > section { background: #eee }
  dl > {
    dt { color: #333 }
    dd { color: #555 }
  }
  //选中nav后紧跟着的article元素
  nav + & { margin-top: 0 }
}

//编译后
article ~ article { border-top: 1px dashed #ccc }
article > footer { background: #eee }
article dl > dt { color: #333 }
article dl > dd { color: #555 }
nav + article { margin-top: 0 }
```

#### 4. 嵌套属性

嵌套属性的规则是这样的：把属性名从中划线-的地方断开，在根属性后边添加一个冒号 `:`，紧跟一个`{ }`块，把子属性部分写在这个`{ }`块中。

```scss
nav {
  border: {
  style: solid;
  width: 1px;
  color: #ccc;
  }
}

//编译后
nav {
  border-style: solid;
  border-width: 1px;
  border-color: #ccc;
}

//2. 还可以这样写：
nav {
  border: 1px solid #ccc {
  left: 0px;
  right: 0px;
  }
}

//编译后
nav {
  border: 1px solid #ccc;
  border-left: 0px;
  border-right: 0px;
}
```

### 三、导入Sass文件

`css` 有一个特别不常用的特性，即 `@import` 规则，它允许在一个 `css` 文件中导入其他 `css` 文件。然而，后果是只有执行到 `@import` 时，浏览器才会去下载其他 `css` 文件，这导致页面加载起来特别慢。`sass` 也有一个 `@import` 规则，但不同的是，`sass` 的 `@import` 规则在生成 `css` 文件时就把相关文件导入进来。这意味着所有相关的样式被归纳到了同一个 `css` 文件中，而无需发起额外的下载请求。

#### 1. 使用Sass部分文件

当通过 `@import` 把 `sass` 样式分散到多个文件时，你通常只想生成少数几个 `css` 文件。那些专门为 `@import` 命令而编写的 `sass` 文件，并不需要生成对应的独立 `css` 文件，这样的 `sass` 文件称为局部文件。对此，`sass` 有一个特殊的约定来命名这些文件。

此约定即，`sass` 局部文件的文件名以下划线开头。这样，`sass` 就不会在编译时单独编译这个文件输出 `css`，而只把这个文件用作导入。当你 `@import` 一个局部文件时，还可以不写文件的全名，即省略文件名开头的下划线。举例来说，你想导入 `themes/_night-sky.scss` 这个局部文件里的变量，你只需在样式表中写 `@import "themes/night-sky";`。

局部文件可以被多个不同的文件引用。当一些样式需要在多个页面甚至多个项目中使用时，这非常有用。在这种情况下，有时需要在你的样式表中对导入的样式稍作修改，`sass` 有一个功能刚好可以解决这个问题，即默认变量值。

#### 2. 默认值变量

假如你写了一个可被他人通过 `@import` 导入的 `sass` 库文件，你可能希望导入者可以定制修改 `sass` 库文件中的某些值。使用 `sass` 的 `!default` 标签可以实现这个目的。它很像 `css` 属性中 `!important` 标签的对立面，不同的是 `!default` 用于变量，含义是：如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值。

```scss
$fancybox-width: 400px !default;
.fancybox {
width: $fancybox-width;
}
```

在上例中，如果用户在导入你的 `sass` 局部文件之前声明了一个 `$fancybox-width` 变量，那么你的局部文件中对 `$fancybox-width` 赋值 `400px` 的操作就无效。如果用户没有做这样的声明，则 `$fancybox-width` 将默认为 `400px`。

#### 3. 镶嵌导入

跟原生的 `css` 不同，`sass` 允许 `@import` 命令写在 `css` 规则内。这种导入方式下，生成对应的 `css` 文件时，局部文件会被直接插入到 `css` 规则内导入它的地方。举例说明，有一个名为 `_blue-theme.scss` 的局部文件，内容如下：

```scss
aside {
  background: blue;
  color: white;
}
```

然后把它导入到一个CSS规则内，如下所示：

```scss
.blue-theme {@import "blue-theme"}

//生成的结果跟你直接在.blue-theme选择器内写_blue-theme.scss文件的内容完全一样。
.blue-theme {
  aside {
    background: blue;
    color: #fff;
  }
}
```

被导入的局部文件中定义的所有变量和混合器，也会在这个规则范围内生效。这些变量和混合器不会全局有效，这样我们就可以通过嵌套导入只对站点中某一特定区域运用某种颜色主题或其他通过变量配置的样式。

#### 4. 原生的css导入

由于 `sass` 兼容原生的 `css`，所以它也支持原生的 `CSS@import`。尽管通常在 `sass` 中使用 `@import` 时，`sass` 会尝试找到对应的 `sass` 文件并导入进来，但在下列三种情况下会生成原生的 `CSS@import`，尽管这会造成浏览器解析 `css` 时的额外下载：

- 被导入文件的名字以`.css`结尾；
- 被导入文件的名字是一个URL地址（比如http://www.sass.hk/css/css.css），由此可用谷歌字体API提供的相应服务；
- 被导入文件的名字是`CSS`的url()值。

这就是说，你不能用 `sass` 的 `@import` 直接导入一个原始的 `css` 文件，因为 `sass` 会认为你想用 `css` 原生的 `@import`。但是，因为 `sass` 的语法完全兼容 `css`，所以你可以把原始的 `css` 文件改名为 `.scss` 后缀，即可直接导入了。

### 四、静默注释

`sass` 另外提供了一种**不同于** `css` 标准注释格式 `/* ... */` 的注释语法，即静默注释，其内容不会出现在生成的 `css` 文件中。静默注释的语法跟 `JavaScript``Java` 等类 `C` 的语言中单行注释的语法相同，它们以 `//` 开头，注释内容直到行末。

```scss
body {
  color: #333; // 这种注释内容不会出现在生成的css文件中
  padding: 0; /* 这种注释内容会出现在生成的css文件中 */
}
```

实际上，`css` 的标准注释格式 `/* ... */` 内的注释内容亦可在生成的 `css` 文件中抹去。当注释出现在原生 `css` 不允许的地方，如在 `css` 属性或选择器中， `sass` 将不知如何将其生成到对应 `css` 文件中的相应位置，于是这些注释被抹掉。

```scss
body {
  color /* 这块注释内容不会出现在生成的css中 */: #333;
  padding: 1; /* 这块注释内容也不会出现在生成的css中 */ 0;
}
```

### 五、混合器

跟 js 中的函数作用类似，均是为了代码复用。混合器使用 `@mixin` 标识符定义。下边的这段 `sass` 代码，定义了一个非常简单的混合器，目的是添加跨浏览器的圆角边框。

```scss
@mixin rounded-corners {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```

然后就可以在你的样式表中通过`@include`来使用这个混合器，放在你希望的任何地方。`@include`调用会把混合器中的所有样式提取出来放在`@include`被调用的地方。如果像下边这样写：

```scss
notice {
  background-color: green;
  border: 2px solid #00aa00;
  @include rounded-corners;
}

//sass最终生成：
.notice {
  background-color: green;
  border: 2px solid #00aa00;
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```

但是大量的重用可能会导致生成的样式表过大，导致加载缓慢。

#### 1. 何时使用混合器

能否为这个混合器想出一个好的名字。如果你能找到一个很好的短名字来描述这些属性修饰的样式，那么往往能够构造一个合适的混合器。

#### 2. 混合器中的css规则

混合器中不仅可以包含属性，也可以包含 `css` 规则，包含选择器和选择器中的属性，如下代码:

```scss
@mixin no-bullets {
  list-style: none;
  li {
    list-style-image: none;
    list-style-type: none;
    margin-left: 0px;
  }
}
```

当一个包含 `css` 规则的混合器通过 `@include` 包含在一个父规则中时，在混合器中的规则最终会生成父规则中的嵌套规则：

```scss
ul.plain {
  color: #444;
  @include no-bullets;
}

//编译后
ul.plain {
  color: #444;
  list-style: none;
}
ul.plain li {
  list-style-image: none;
  list-style-type: none;
  margin-left: 0px;
}
```

混合器中的规则甚至可以使用 `sass` 的父选择器标识符 `&`。使用起来跟不用混合器时一样，`sass` 解开嵌套规则时，用父规则中的选择器替代 `&`。

如果一个混合器只包含 `css` 规则，不包含属性，那么这个混合器就可以在文档的顶部调用，写在所有的 `css` 规则之外。

#### 3. 给混合器传参

可以通过在 `@include` 混合器时给混合器传参，来定制混合器生成的精确样式。当 `@include` 混合器时，参数其实就是可以赋值给 `css` 属性值的变量。

```scss
@mixin link-colors($normal, $hover, $visited) {
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
```

当混合器被 `@include` 时，你可以把它当作一个 `css` 函数来传参。如果你像下边这样写：

```scss
a {
  @include link-colors(blue, red, green);
}

//Sass最终生成的是：
a { color: blue; }
a:hover { color: red; }
a:visited { color: green; }
```

当你 `@include` 混合器时，有时候可能会很难区分每个参数是什么意思，参数之间是一个什么样的顺序。为了解决这个问题，`sass` 允许通过语法`$name: value` 的形式指定每个参数的值。这种形式的传参，参数顺序就不必再在乎了，只需要保证没有漏掉参数即可：

```scss
a {
    @include link-colors(
      $normal: blue,
      $visited: green,
      $hover: red
  );
}
```

#### 4. 默认参数值

为了在 `@include` 混合器时不必传入所有的参数，我们可以给参数指定一个默认值。参数默认值使用 `$name: default-value` 的声明形式，默认值可以是任何有效的 `css` 属性值，甚至是其他参数的引用，如下代码：

```scss
@mixin link-colors(
    $normal,
    $hover: $normal,
    $visited: $normal
  )
{
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
```

如果像下边这样调用：`@include link-colors(red)` 时， `$hover`和`$visited`也会被自动赋值为`red`。

### 六、使用选择器继承来精简css

使用 `sass` 的时候，最后一个减少重复的主要特性就是选择器继承。选择器继承是说一个选择器可以继承为另一个选择器定义的所有样式。这个通过 `@extend` 语法实现，如下代码:

```scss
//通过选择器继承继承样式
.error {
  border: 1px solid red;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
```

在上边的代码中，`.seriousError` 将会继承样式表中任何位置处为 `.error` 定义的所有样式。

`.seriousError`不仅会继承`.error`自身的所有样式，任何跟`.error`有关的组合选择器样式也会被`.seriousError`以组合选择器的形式继承，如下代码:

```scss
//.seriousError从.error继承样式
.error a{  //应用到.seriousError a
  color: red;
  font-weight: 100;
}
h1.error { //应用到hl.seriousError
  font-size: 1.2rem;
}
```

#### 1. 何时使用继承

因为继承是基于类的（有时是基于其他类型的选择器），所以继承应该是建立在语义化的关系上。当一个元素拥有的类（比如说 `.seriousError` ）表明它属于另一个类（比如说 `.error` ），这时使用继承再合适不过了。

#### 2. 继承的高级用法

最常用的一种高级用法是继承一个 `html` 元素的样式。尽管默认的浏览器样式不会被继承，因为它们不属于样式表中的样式，但是你对 `html` 元素添加的所有样式都会被继承。

接下来的这段代码定义了一个名为 `disabled` 的类，样式修饰使它看上去像一个灰掉的超链接。通过继承 a 这一**超链接元素**来实现：

```scss
.disabled {
  color: gray;
  @extend a;
}
```

假如一条样式规则继承了一个复杂的选择器，那么它只会继承这个复杂选择器命中的元素所应用的样式。举例来说， 如果 `.seriousError @extend .important.error` ， 那么 `.important.error` 和 `h1.important.error`  的样式都会被 `.seriousError` 继承， 但是 `.important` 或者 `.error` 下的样式则不会被继承。这种情况下你很可能希望 `.seriousError` 能够分别继承 `.important` 或者 `.error` 下的样式。

如果一个选择器序列（`#main .seriousError`）`@extend` 另一个选择器（`.error`），那么只有完全匹配 `#main .seriousError` 这个选择器的元素才会继承 `.error` 的样式，就像单个类名继承那样。拥有 `class="seriousError"` 的 `#main` 元素之外的元素不会受到影响。

像 `#main .error` 这种选择器序列是**不能被继承**的。这是因为从 `#main .error` 中继承的样式一般情况下会跟直接从 `.error` 中继承的样式基本一致，细微的区别往往使人迷惑。

#### 3. 传承的工作细节

跟变量和混合器不同，继承不是仅仅用 `css` 样式替换@extend处的代码那么简单。`@extend` 背后最基本的想法是，如果 `.seriousError @extend .error`， 那么样式表中的任何一处 `.error` 都用 `.error .seriousError` 这一选择器组进行替换。这就意味着相关样式会如预期那样应用到 `.error` 和 `.seriousError`。当 `.error` 出现在复杂的选择器中，比如说 `h1.error``.error a` 或者 `#main .sidebar input.error[type="text"]`，那情况就变得复杂多了，但是不用担心，`sass` 已经为你考虑到了这些。

关于 `@extend` 有两个要点你应该知道：

1. 跟混合器相比，继承生成的 `css` 代码相对更少。因为继承仅仅是重复选择器，而不会重复属性，所以使用继承往往比混合器生成的 `css` 体积更小。
2. 继承遵从 `css` 层叠的规则。当两个不同的 `css` 规则应用到同一个 `html` 元素上时，并且这两个不同的 `css` 规则对同一属性的修饰存在不同的值，`css` 层叠规则会决定应用哪个样式。相当直观：通常权重更高的选择器胜出，如果权重相同，定义在后边的规则胜出。

#### 4. 使用继承的最佳实践

继承只会在生成 `css` 时复制选择器，而不会复制大段的 `css` 属性。但是如果你不小心，可能会让生成的 `css` 中包含大量的选择器复制。

避免这种情况出现的最好方法就是不要在 `css` 规则中使用后代选择器（比如 `.foo .bar` ）去继承 `css` 规则。如果你这么做，同时被继承的 `css` 规则有通过后代选择器修饰的样式，生成 `css` 中的选择器的数量很快就会失控：

```scss
.foo .bar { @extend .baz; }
.bip .baz { a: b; }
```

在上边的例子中，`sass` 必须保证应用到 `.baz` 的样式同时也要应用到 `.foo .bar`（位于 `class="foo"` 的元素内的 `class="bar"` 的元素）。例子中有一条应用到 `.bip .baz`（位于 `class="bip"` 的元素内的 `class="baz"` 的元素）的 `css` 规则。当这条规则应用到 `.foo .bar` 时，可能存在三种情况，如下代码:

```scss
<!-- 继承可能迅速变复杂 -->
<!-- Case 1 -->
<div class="foo">
  <div class="bip">
    <div class="bar">...</div>
  </div>
</div>
<!-- Case 2 -->
<div class="bip">
  <div class="foo">
    <div class="bar">...</div>
  </div>
</div>
<!-- Case 3 -->
<div class="foo bip">
  <div class="bar">...</div>
</div>
```

为了应付这些情况，`sass` 必须生成三种选择器组合（仅仅是.bip .foo .bar不能覆盖所有情况）。如果任何一条规则里边的后代选择器再长一点，`sass` 需要考虑的情况就会更多。实际上 `sass` 并不总是会生成所有可能的选择器组合，即使是这样，选择器的个数依然可能会变得相当大，所以如果允许，尽可能避免这种用法。