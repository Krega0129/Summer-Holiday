### webpack

1. *webpack* 是一个静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个*依赖关系图(dependency graph)*，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 *bundle*。

2. 五个核心概念：

   1. 入口：**入口起点(entry point)** 指示 webpack 应该使用哪个模块，来作为构建其内部依赖图的开始。可以通过在 [webpack 配置](https://www.webpackjs.com/configuration)中配置 `entry` 属性，来指定一个入口起点（或多个入口起点）。默认值为 `./src`。
   2. 出口：**output** 属性告诉 webpack 在哪里输出它所创建的 *bundles*，以及如何命名这些文件，默认值为 `./dist`。
   3. Loader：Loader 让 Webpack 能够去处理那些非 JavaScript 文件（Webpack 自身只能理解 JavaScript）
   4. Plugins：插件（plugins）可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量等
   5. Mode：模式（Mode）指示 Webpack 使用相应模式的配置。
      1. development：能让代码本地调试运行的环境；
      2. production：能让代码优化上线运行的环境；

3. 打包样式资源：

   1. webpack.config.js 文件：

      ```js
      /* 
        webpack.config.js  webpack的配置文件
          作用：提示 webpack 干那些活（当你运行 webpack 指令时，会加载里面的配置）
      
          所有构建工具都是基于nodejs平台运行的，模块化默认采用commonjs
      */
      
      //resolve用来拼接绝对路径的方法
      const { resolve } = require('path');
      
      module.exports = {
        //webpack配置
        //入口起点
        entry: './src/index.js',
        //输出路径
        //__dirname：nodejs的变量，代表当前文件（webpack.config.js）的绝对路径
        output: {
          filename: 'build.js',
          path: resolve(__dirname, 'build')
        },
        //loader的配置
        module: {
          rules: [
            //详细loader配置
            //不同的文件必须配置不同的loader处理
            {
              //匹配哪些文件（正则）
              test: /\.css$/,
              //使用哪些loader进行处理
              use: [
                //use 数组中loader执行顺序：从下到上（从右到左）
                //创建style标签，将js中的样式资源插入进去，添加到head中生效
                'style-loader',
                //将css文件变成commonjs模块加载到js中，里面内容是样式字符串
                'css-loader'
              ]
            },
            {
              test: /\.less$/,
              use: [
                'style-loader',
                'css-loader',
                //将less文件编译成css文件
                //需要下载less-loader和less
                'less-loader'
              ]
            }
          ]
        },
        //plugins配置
        plugins: [
          //详细plugins配置
        ],
        //模式
        mode: 'development'   //开发模式
        // mode: 'production'   //生产模式
      }
      ```

   2. index.js 文件：

      ```js
      //引入css资源
      import './index.css';
      import './index.less'
      ```

4. 打包html资源：

   1. webpack.config.js 文件：

      ```js
      /* 
        loader: 1. 下载  2. 使用（配置loader）
        plugins：1. 下载  2. 引入  3. 使用
      */
      
      const { resolve } = require('path');
      
      //引入插件
      const HtmlWebpackPlugin = require('html-webpack-plugin');
      
      module.exports = {
        entry: './src/index.js',
        output: {
          filename: 'build.js',
          path: resolve(__dirname, 'build')
        },
        module: {
          rules: [
            //loader的配置
          ]
        },
        plugins: [
          //plugins的配置
          //下载 html-webpack-plugin 插件，指令：npm i html-webpack-plugin -D
          //功能：默认会创建一个空的HTML，自动引入打包输出的所有资源（js/css）
          //需求：需要有结构的HTML文件，此时需要加入参数
          new HtmlWebpackPlugin({
            //复制 ./src/index.html 文件，并自动引入打包输出的所有资源（js/css）
            template: './src/index.html'
          })
        ],
        mode: 'development'
      }
      ```

5. 打包图片资源：

   1. webpack.config.js中的配置代码：

      ```js
      const { resolve } = require('path');
      const HtmlWebpackPlugin = require('html-webpack-plugin');
      
      module.exports ={
        entry: './src/index.js',
        output: {
          filename: 'build.js',
          path: resolve(__dirname, 'build')
        },
        module: {
          rules: [
            {
              test: /\.less$/,
              //要用多个loader时用数组
              use: [
                'style-loader',
                'css-loader',
                'less-loader'
              ]
            },
            {
              //处理图片
              //问题，默认处理不了html文件中的img图片，需要用 html-loader
              test: /\.(png|jpg|gif)$/,
              //使用一个 loader
              //下载： url-loader, file-loader
              loader: 'url-loader',
              options: {
                //图片大小小于8kb，就会被base64处理
                //优点：减少请求数量（减轻服务器压力）
                //缺点：图片体积会增大（文件请求速度会更慢）
                limit: 8 * 1024,
                //问题：因为url-loader默认使用es6模块化解析，而html-loader引入图片时commonjs
                //解析时会出问题：img中src为[object Module] (但测试时没有发现错误)
                //解决：关闭url-loader的es6模块化，使用commonjs
                esModule: false,
                //图片的名字为自动生成的唯一哈希值，若觉得过长，可以进行重命名
                //[hash:10]取图片的hash的前10位
                //[ext]取文件原来的扩展名
                name: '[hash:10].[ext]'
              }
            },
            {
              test: /\.html$/,
              //处理html文件的img图片（负则引入img，从而能被url-loader进行处理）
              loader: 'html-loader'
            }
          ]
        },
        plugins: [
          new HtmlWebpackPlugin({
            template: './src/index.html'
          })
        ],
        mode: 'development'
      }
      ```

6. 打包其他资源（字体等，可以在 [iconfont](https://www.iconfont.cn/) 下载素材）

   1. webpack.config.js中的配置代码：

      ```js
      const {resolve} = require('path');
      const HtmlWebpackPlugin = require('html-webpack-plugin');
      
      module.exports = {
        entry: './src/index.js',
        output: {
          filename: 'build.js',
          path: resolve(__dirname, 'build')
        },
        module: {
          rules: [
            {
              test: /\.css$/,
              use: [
                'style-loader',
                'css-loader'
              ]
            },
            //打包其他资源
            {
              //排除css/js/html文件
              exclude: /\.(css|js|html)$/,
              loader: 'file-loader'
            }
          ]
        },
        plugins: [
          new HtmlWebpackPlugin({
            template: './src/index.html'
          })
        ],
        mode: 'development'
      }
      ```

7. 开发服务器devServer：用来自动化（自动编译、自动打开浏览器、自动刷新浏览器）

   1. webpack.config.js中的配置代码：

      ```js
      const {resolve} = require('path');
      const HtmlWebpackPlugin = require('html-webpack-plugin');
      
      module.exports = {
        entry: './src/index.js',
        output: {
          filename: 'build.js',
          path: resolve(__dirname, 'build')
        },
        module: {
          rules: [
            //loader配置
          ]
        },
        plugins: [
          //插件配置
        ],
        mode: 'development',
        
        //开发服务器devServer：用来自动化（自动编译、自动打开浏览器、自动刷新浏览器）
        //特点：只会在内存中编译打包，不会有任何输出
        //启动devserver指令为：npx webpack-dev-server，要先下载 webpack-dev-server
        devServer: {
          //路径
          contentBase: resolve(__dirname, 'build'),
          //启动gzip压缩
          compress: true,
          //端口号
          port: 3000,
          //自动打开浏览器
          open: true
        }
      }
      ```

8. 开发环境基本配置：

   ```js
   const {resolve} = require('path');
   const HtmlWebpackPlugin = require('html-webpack-plugin');
   
   module.exports = {
     entry: './src/js/index.js',
     output: {
       filename: 'js/build.js',
       path: resolve(__dirname, 'build')
     },
     module: {
       rules: [
         {
           //处理less资源
           test: /\.less$/,
           use: ['style-loader', 'css-loader', 'less-loader']
         },
         {
           //处理css资源
           test: /\.css$/,
           use: ['style-loader', 'css-loader']
         },
         {
           //处理图片资源
           test: /\.(jpg|png|gif)$/,
           loader: 'url-loader',
           options: {
             limit: 2 * 1024,
             name: '[hash:10].[ext]',
             //关闭es6模块化
             esModule: false,
             //把图片输出到img文件夹里
             outputPath: 'img'
           }
         },
         {
           //处理html中img资源
           test: /\.html$/,
           loader: 'html-loader'
         },
         {
           //处理其他资源
           exclude: /\.(css|js|html|less|jpg|png|gif)$/,
           loader: 'file-loader',
           options: {
             name: '[hash:10].[ext]',
             //把其他资源输出到media文件夹里
             outputPath: 'media'
           }
         },
       ]
     },
     plugins: [
       new HtmlWebpackPlugin({
         template: './src/index.html'
       })
     ],
     mode: 'development',
     devServer: {
       contentBase: resolve(__dirname, 'build'),
       compress: true,
       port: 3000,
       open: true
     }
   }
   ```

9. 提取css成单独文件：

   1. webpack.config.js中的配置代码：

      ```js
      const {resolve} = require('path');
      const HtmlWebpackPlugin = require('html-webpack-plugin');
      const MiniCssExtractPlugin = require('mini-css-extract-plugin');
      
      module.exports = {
        entry: './src/js/index.js',
        output: {
          filename: 'js/build.js',
          path: resolve(__dirname, 'build')
        },
        module: {
          rules: [
            {
              test: /\.css/,
              use: [
                //创建style标签，将样式引入（此处不需要）
                //'style-loader',
                //这个loader取代style-loader。作用：提取js中的css成单独文件
                //需要安装插件：mini-css-extract-plugin
                MiniCssExtractPlugin.loader,
                //将css文件整合到js文件中
                'css-loader'
              ]
            }
          ]
        },
        plugins: [
          new HtmlWebpackPlugin({
            template: './src/index.html'
          }),
          new MiniCssExtractPlugin({
            filename: 'css/build.css'
          })
        ]
      }
      ```

10. css兼容性处理：

    1. webpack.config.js中的配置代码：

       ```js
       const {resolve} = require('path');
       const HtmlWebpackPlugin = require('html-webpack-plugin');
       const MiniCssExtractPlugin = require('mini-css-extract-plugin');
       
       //设置nodejs的环境变量：
       process.env.NODE_ENV = 'development';
       
       module.exports = {
         entry: './src/js/index.js',
         output: {
           filename: 'js/build.js',
           path: resolve(__dirname, 'build')
         },
         module: {
           rules: [
             {
               test: /\.css/,
               use: [
                 MiniCssExtractPlugin.loader,
                 'css-loader',
                 /* 
                   css兼容性处理：postcss(库) --> （在webpack中使用需要）postcss-loader 
                   			--> 需要插件：postcss-preset-env
                     安装：postcss-loader 和 postcss-preset-env
                     帮postcss找到package.json中browserslist(自己添加)里面的配置
                     通过配置加载指定的css兼容性样式:
                     "browserslist": {
                       //开发环境
                       "development": [
                         "last 1 chrome version",
                         "last 1 firefox version",
                         "last 1 safari version"
                       ],
                       //生产环境：默认看生产环境，看开发环境的话需要设置node的环境变量：
                       //process.env.NODE_ENV = 'development'
                       "production": [
                         ">0.2%",
                         "not dead",
                         "not op_mini all"
                       ]
                     }
                 */
                 //1. 使用loader的默认配置
                 //postcss-loader
                 //2. 我们需要修改loader的配置
                 {
                   loader: 'postcss-loader',
                   options: {
                     ident: 'postcss',
                     plugins: () => [
                       //postcss的插件
                       require('postcss-preset-env')()
                     ]
                   }
                 }
               ]
             }
           ]
         },
         plugins: [
           new HtmlWebpackPlugin({
             template: './src/index.html'
           }),
           new MiniCssExtractPlugin({
             filename: 'css/build.css'
           })
         ],
         mode: 'development'
       }
       ```

11. js语法检查eslint

    1. webpack.config.js中的配置代码：

       ```js
       const { resolve } = require('path');
       const HtmlWebpackPlugin = require('html-webpack-plugin');
       
       module.exports = {
         entry: './src/js/index.js',
         output: {
           filename: 'js/build.js',
           path: resolve(__dirname, 'build'),
         },
         module: {
           rules: [
             /*
               语法检查：eslint-loader  eslint
                 【注】只检查自己写的源代码，第三方的库是不用检查的
                 设置检查规则：
                   在 package.json中eslintConfig中设置：
                   	"eslintConfig": {
                          "extends": "airbnb-base"
                        }
                     推荐使用airbnb：需要下载三个：
                     eslint-config-airbnb-base eslint-plugin-import eslint
             */
             {
               test: /\.js$/,
               // 排除第三方的库
               exclude: /node_modules/,
               loader: 'eslint-loader',
               options: {
                 //自动修复eslint的错误
                 fix: true
               },
             },
           ],
         },
         plugins: [
           new HtmlWebpackPlugin({
             template: './src/index.html',
           }),
         ],
         mode: 'development',
       };
       ```

    2. 使下一行eslint失效：

       ```js
       // eslint-disable-next-line
       code...		//此行eslint失效
       ```

12. js兼容性处理

    1. webpack.config.js中的配置代码：

       ```js
       const { resolve } = require('path');
       const HtmlWebpackPlugin = require('html-webpack-plugin');
       
       module.exports = {
         entry: './src/js/index.js',
         output: {
           filename: 'js/build.js',
           path: resolve(__dirname, 'build'),
         },
         module: {
           rules: [
             /* 
               js兼容性处理: 下载 babel-loader @babel/core
                 1. 基于js兼容性处理, -->下载 @babel/preset-env
                   问题: 只能转换基本语法, 如promise等高级语法不能转换
                 2. 全部的兼容性处理 -->下载 @babel/polyfill, 在对应的js文件中import引入
                   问题: 我只要解决部分兼容性问题, 但是将所有兼容性代码引入, 体积太大
                 3. 需要做兼容性处理的就做: 按需加载 -->下载 core-js
             */
             {
               test: /\.js$/,
               exclude: /node_modules/,
               loader: 'babel-loader',
               options: {
                 // 1. 预设: 指示babel做怎样的兼容性处理
                 // presets: ['@babel/preset-env']
       
                 //3. core-js按需加载,此时需要不能使用第二种方法:
                 //    在对应的js文件中import引入@babel/polyfill
                 presets: [
                   [
                     '@babel/preset-env',
                     {
                       // 按需加载
                       useBuiltIns: 'usage',
                       // 指定core-js版本
                       corejs: {
                         version: 3
                       },
                       // 指定兼容性做的哪个版本浏览器
                       targets: {
                         chrome: '60',
                         firefox: '60',
                         ie: '9',
                         safari: '10',
                         edge: '17'
                       }
                     }
                   ]
                 ]
               }
             }
           ],
         },
         plugins: [
           new HtmlWebpackPlugin({
             template: './src/index.html',
           }),
         ],
         mode: 'development'
       };
       ```

13. js和html压缩：

    1. webpack.config.js中的配置代码：

       ```js
       const { resolve } = require('path');
       const HtmlWebpackPlugin = require('html-webpack-plugin');
       
       module.exports = {
         entry: './src/js/index.js',
         output: {
           filename: 'js/build.js',
           path: resolve(__dirname, 'build'),
         },
         plugins: [
           new HtmlWebpackPlugin({
             template: './src/index.html',
             // 压缩html
             minify: {
               // 移除空格
               collapseWhitespace: true,
               // 移除注释
               removeComments: true
             }
           }),
         ],
         // 生产环境下会自动压缩js代码
         mode: 'production'
       };
       ```

14. 生产环境基本配置：

    1.  webpack.config.js中的配置代码：

       ```js
       const { resolve } = require("core-js/fn/promise");
       const HtmlWebpackPlugin = require("html-webpack-plugin");
       const MiniCssExtractPlugin = require("mini-css-extract-plugin");
       const OptimizeCssAssetsPlugin = require("optimize-css-assets-webpack-plugin");
       
       const commonCssLoader = [
         MiniCssExtractPlugin.loader,
           'css-loader',
           {
             //还需要在package.json中定义browserslist, 默认使用生产环境,
             /* 
             "browserslist": {
               "development": [
                 "last 1 chrome version",
                 "last 1 firefox version",
                 "last 1 safari version"
               ],
               "production": [
                 ">0.2%",
                 "not dead",
                 "not op_mini all"
               ]
             }
             */
             //若用开发环境需要修改nodejs环境变量(决定使用browserslist哪个环境): 
               //process.env.NODE_ENV = 'development'
             loader: 'postcss-loader',
             options: {
               ident: 'postcss',
               plugins: () => [
                 require('postcss-preset-env')()
               ]
             }
           }
       ]
       
       module.exports = {
         entry: './src/index.js',
         output: {
           filename: 'js/build.js',
           path: resolve(__dirname, 'build')
         },
         module: {
           rules: [
             {
               test: /\.css$/,
               use: [...commonCssLoader]
             },
             {
               test: /\.less$/,
               use: [...commonCssLoader, 'less-loader']
             },
             {
               // 需要在package.json中定义eslintConfig --> airbnb
               /* 
                 "eslintConfig": {
                   "extends": "airbnb-base"
                 }
               */
               test: /\.js$/,
               exclude: /node_modules/,
               //优先执行
               enforce: 'pre',
               loader: 'eslint-loader',
               options: {
                 fix: true
               }
             },
             /* 
             正常来说, 一个文件只能被一个loader处理,
             当一个文件要被多个loader处理,那么一定要指定loader执行的先后顺序:
                 先执行eslint 再执行 babel
             */
             {
               // js兼容性处理
               test: /\.js$/,
               exclude: /node_modules/,
               loader: 'babel-loader',
               options: {
                 presets: [
                   [
                     '@babel/preset-env',
                     {
                       useBuiltIns: 'usage',
                       corejs: { version: 3 },
                       targets: {
                         chrome: '60',
                         firefox: '50'
                       }
                     }
                   ]
                 ]
               }
             },
             {
               test: /\.(jpg|png|gif)$/,
               loader: 'url-loader',
               options: {
                 limit: 8 * 1024,
                 name: '[hash:10].[ext]',
                 outputPath: 'img',
                 esModule: false
               }
             },
             {
               //html中的img标签
               test: /\.html$/,
               loader: 'html-loader',
             },
             {
               exclude: /\.(html|css|js|less|jpg|png|gif)$/,
               loader: 'file-loader',
               options: {
                 outputPath: 'media'
               }
             }
           ]
         },
         plugins: [
           new MiniCssExtractPlugin({
             filename: 'css/build.css'
           }),
           // 压缩css
           new OptimizeCssAssetsPlugin(),
           new HtmlWebpackPlugin({
             template: './src/index.html',
             minify: {
               collapseWhitespace: true,
               removeComments: true
             }
           }),
           
         ],
         mode: 'production'
       }
       ```

15. HMR：热模块替换

    1. webpack.config.js中的配置代码：

       ```js
       /* 
         HMR: hot module replacement 热模块替换 / 模块热替换
           作用: 一个模块发生变化, 只会重新打包这一个模块(而不是打包所有)
               极大提升构建速度
       
           样式文件: 可以使用 HMR功能, 因为style-loader内部实现了
           js文件: 默认不能使用 HMR功能  --> 需要修改js代码, 添加支持HMR功能地代码
             [注]:HMR对js的处理, 只能处理非入口js文件
           html文件: 默认不能使用 HMR功能, 同时会导致问题: html文件不能热更新了(不需要做HMR功能)
             解决: 修改entry, 将html文件引入
       */
       
       const {resolve} = require('path');
       const HtmlWebpackPlugin = require('html-webpack-plugin');
       
       module.exports = {
         entry: './src/js/index.js',
         output: {
           filename: 'js/build.js',
           path: resolve(__dirname, 'build')
         },
         module: {
           rules: [
             {
               //处理less资源
               test: /\.less$/,
               use: ['style-loader', 'css-loader', 'less-loader']
             },
             {
               //处理css资源
               test: /\.css$/,
               use: ['style-loader', 'css-loader']
             },
             {
               //处理图片资源
               test: /\.(jpg|png|gif)$/,
               loader: 'url-loader',
               options: {
                 limit: 2 * 1024,
                 name: '[hash:10].[ext]',
                 //关闭es6模块化
                 esModule: false,
                 //把图片输出到img文件夹里
                 outputPath: 'img'
               }
             },
             {
               //处理html中img资源
               test: /\.html$/,
               loader: 'html-loader'
             },
             {
               //处理其他资源
               exclude: /\.(css|js|html|less|jpg|png|gif)$/,
               loader: 'file-loader',
               options: {
                 name: '[hash:10].[ext]',
                 outputPath: 'media'
               }
             },
           ]
         },
         plugins: [
           new HtmlWebpackPlugin({
             template: './src/index.html'
           })
         ],
         mode: 'development',
         devServer: {
           contentBase: resolve(__dirname, 'build'),
           compress: true,
           port: 3000,
           open: true,
           //开启 HMR 功能
           //当修改了 webpack配置, 新配置想要生效, 必须重启 webpack服务
           hot: true
         }
       }
       ```

    2. HMR对js的处理:

       ```js
       import print from './print'
       
       if(module.hot){
         //一旦 module.hot 为true, 说明开启了HMR功能 --> 让 HMR功能代码生效
         module.hot.accept('./print.js', function(){
           // 方法会监听 print.js 文件地变化, 一旦发生变化, 其他模块不会重新打包构建,
           //会执行后面的回调函数
           print();
         })
       }
       ```

16. source-map：

    1. webpack.config.js中的配置代码：

       ```js
       const {resolve} = require('path');
       const HtmlWebpackPlugin = require('html-webpack-plugin');
       
       module.exports = {
         entry: './src/js/index.js',
         output: {
           filename: 'js/build.js',
           path: resolve(__dirname, 'build')
         },
         module: {
           rules: []
         },
         plugins: [],
         mode: 'development',
         devServer: {
           contentBase: resolve(__dirname, 'build'),
           compress: true,
           port: 3000,
           open: true,
           hot: true
         },
         devtool: 'eval-source-map'
       }
       
       /* 
         source-map：一种 提供源代码到构建后代码的映射 技术（如果构建后代码出错了，通过映射可以追踪到源代码的错误）
           参数：[inline-|hidden-|eval-][nosources-][cheap-[module-]]sources-map (任意组合)
       
           source-map：外部
             错误代码准确信息 和 源代码的错误位置
       
           inline-source-map：内联
             只生成一个内联source-map
             错误代码准确信息 和 源代码的错误位置
       
           hidden-source-map：外部
             错误代码错误原因，但是没有源代码的错误位置
             不能追踪到源代码错误，只能提示到构建后代码的错误位置
       
           eval-source-map：内联
             每一个文件都生成对应的source-map，都在eval
             错误代码准确信息 和 源代码的错误位置
       
           nosources-source-map：外部
             错误代码准确信息，但是没有任何源代码信息
       
           cheap-source-map：外部
             错误代码准确信息 和 源代码的错误位置
             只能精确到行
           
           cheap-module-source-map：外部
             错误代码准确信息 和 源代码的错误位置
             module会将loader的source-map加入
       
           内联 和 外部的区别：1. 外部生成了文件，内部没有； 2. 内联构建速度更快
       
           开发环境：速度快，调试更友好
             速度快（eval>inline>cheap>...）
               eval-cheap-source-map
               eval-source-map
             调试更友好：
               source-map
               cheap-module-source-map
               cheap-source-map
       
             --> eval-source-map / eval-cheap-module-source-map
       
           生产环境：源代码要不要隐藏？调试要不要更友好？
             内联会让代码体积变大，所以生产环境下不用内联
             nosources-source-map  全部隐藏
             hidden-source-map   只隐藏源代码，会提示构建后代码错误信息
       
             --> source-map / cheap-module-source-map
       
       */
       ```

17. oneOf：oneOf 定义在 module 里，为一个数组，数组里面的loader只会匹配一个，匹配成功后不继续匹配

    1. 注意：不能有两个配置处理同一种类型文件，否则只会生效一个

    2.  webpack.config.js中的配置代码：

       ```js
       const { resolve } = require("core-js/fn/promise");
       const HtmlWebpackPlugin = require("html-webpack-plugin");
       const MiniCssExtractPlugin = require("mini-css-extract-plugin");
       const OptimizeCssAssetsPlugin = require("optimize-css-assets-webpack-plugin");
       
       const commonCssLoader = [
         MiniCssExtractPlugin.loader,
           'css-loader',
           {
             loader: 'postcss-loader',
             options: {
               ident: 'postcss',
               plugins: () => [
                 require('postcss-preset-env')()
               ]
             }
           }
       ]
       
       module.exports = {
         entry: './src/index.js',
         output: {
           filename: 'js/build.js',
           path: resolve(__dirname, 'build')
         },
         module: {
           rules: [
             {
               test: /\.js$/,
               exclude: /node_modules/,
               enforce: 'pre',
               loader: 'eslint-loader',
               options: {
                 fix: true
               }
             },
             {
               // oneOf里面的loader只会匹配一个，匹配成功后不继续匹配
               // 注意：不能有两个配置处理同一种类型文件，否则只会生效一个
               oneOf: [
                 {
                   test: /\.css$/,
                   use: [...commonCssLoader]
                 },
                 {
                   test: /\.less$/,
                   use: [...commonCssLoader, 'less-loader']
                 },
                 {
                   // js兼容性处理
                   test: /\.js$/,
                   exclude: /node_modules/,
                   loader: 'babel-loader',
                   options: {
                     presets: [
                       [
                         '@babel/preset-env',
                         {
                           useBuiltIns: 'usage',
                           corejs: { version: 3 },
                           targets: {
                             chrome: '60',
                             firefox: '50'
                           }
                         }
                       ]
                     ]
                   }
                 }
               ]
             }
           ]
         },
         plugins: [
           new MiniCssExtractPlugin({
             filename: 'css/build.css'
           }),
           // 压缩css
           new OptimizeCssAssetsPlugin(),
           new HtmlWebpackPlugin({
             template: './src/index.html',
             minify: {
               collapseWhitespace: true,
               removeComments: true
             }
           })
         ],
         mode: 'production'
       }
       ```

18. tree shaking：去除无用代码

    1. 前提：
       1. 必须使用 ES6 模块化
       2. 开启 production 模式
    2. 作用：减少代码体积
    3. 在 package 中配置：
       1. `"sideEffects": false` ：所有代码都没有副作用（都可以进行tree shaking）
       2. 问题：可能会把 css / @babel/polyfill （副作用）文件删除
       3. 解决方法：`"sideEffects": ["*.css", "*.less"]` ，可以排除相应类型文件。

