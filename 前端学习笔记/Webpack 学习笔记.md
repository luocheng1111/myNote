#### Webpack 学习笔记



### 一、介绍

---

> 浏览器只能识别js、css、html等语法，浏览器识别不了Vue、Scss、jQuery等代码，我们要使用这些代码，就需要将这些代码需要最终转换为js、css、html等浏览器能解析识别的语言才可以使用。而这些语言每个都提供了各自的命令来进行加载转换，由于命令都不相同，所以这在实际项目中会很麻烦
> Webpack就解决了这一问题，他可以配合各种不同loader将前端的所有资源文件(js/css/scss/json/img/...)进行统一打包，最终编译转换成浏览器能识别的语言(.js/.css/.jpg)
> 在Webpack 看来, 前端的所有资源文件(js/json/css/img/less/...)都会作为模块处理。
> 它将根据模块的依赖关系进行静态分析，打包生成对应的静态资源(bundle)。



![在这里插入图片描述](https://img-blog.csdnimg.cn/20210618225851189.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1MjQ1OTg=,size_16,color_FFFFFF,t_70#pic_center)





### 二、环境搭建

---

`要求：Nodejs 10版本以上、Webpack 4.26版本以上`

```css
1.安装webpack依赖包
npm install --save-dev webpack
// 或指定版本
npm install --save-dev webpack@<version>
// 如果使用 webpack v4+ 版本，并且想要在命令行中调用 webpack，你还需要安装 webpack-cli
npm install --save-dev webpack-cli


2.在项目中创建webpack.config.js文件，并加入如下代码
const path = require('path')

module.exports = {
    //入口文件  需要打包的那个文件
    entry:'./src/main.js',
    //输出文件
    output:{
        path:path.join(__dirname,'./dist'),  //输出路径
        filename :'bundle.js' //输出文件名
    },
    
}

3.执行打包命令
  在项目中打开命令行，输入命令webpack， 则会进行打包编译，最后dist文件下生产编译好的bundle.js文件
  webpack   会按照项目中的webpack.config.js配置文件进行打包编译
  webpack --config webpack.config.dev.js 指定webpack按照其他配置文件进行打包编译
```





### 三、Webpack 五个核心概念

---

#### 1. Entry

​	 入口(Entry) 指示 webpack 以哪个文件为入口为起点开始打包，方便他构建内部依赖图

#### 2. Output

​      输出(Output) 指示 webpack 打包后的资源 bundles 输出位置及文件名

#### 3. Loader

​     Loader 让 webpack能够处理那些非JS文件，webpack自身只处理js/json资源，不能处理css、img、vue、scss的等资源，如果要处理这些资源，
​     则需要在 loader 中加入这些资源的处理器 比如css-loader,style-loader,vue-loader

#### 4. Plugins

​     插件(Plugins) 可以用于执行范围更广的任务，插件的范围包括，从打包优化到压缩，一直到重新定义环境中的变量等

#### 5. Mode

​     有两种模式，开发模式和生产模式
​     development: 会将process.env.NODE_ENV的值设为development,并会启动一些插件，为了能让代码本地调试能正常运行就行
​     production: 会将process.env.NODE_ENV的值设为production,并会启动一些插件, 插件相对开发模式会多很多，为了能优化代码并能在线上正常运





### 四、开发环境的基本配置

---

`开发环境配置主要目的是为了能让代码正常运行`

**主要考虑以下几个方面：**

- 打包样式资源   如： css/style/sass

  npm install css-loader -d
  npm install style-loader -d
  npm install sass-loader -d

- 打包 html 资源  如： html

  npm install html-webpack-plugin -d  //插件

- 打包图片资源

  npm install url-loader -d      //处理项目中的图片资源
  npm install file-loader -d     //因为url-loader依赖于file-loader，所以需要同时下载file-loader
  npm install html-loader -d  //处理html中引入图片时的路径处理，url-loader只能处理css中的图片,不能处理html中的图片

- 打包其他资源

  npm install file-loader -d

- devServer

  webpack提供的开发用来自动编译代码，相当于热更新，不需要每次改完代码后都执行webpack

  注：只会在内存中编译打包，不会输出，如果要输出，则还是需要执行webpack命令一下

  

```js
//webpack.config.js文件

const { resolve } = require('path')  // resolve用来拼接绝对路径的方法
const HtmlWebpackPlugin = require('html-webpack-plugin') // 引用plugin

// webpack配置
module.exports = {
  entry: './src/js/index.js', // 入口起点
      
  output: {
    filename: 'js/build.js',  // 输出文件名
    path: resolve(__dirname, 'build'), // 输出路径，所有资源打包都会输出到这个文件夹下 __dirname代表当前文件的目录绝对路径
  },
    
  //loader配置 不同文件必须配置不同loader
  module: {
    rules: [
      {
        test: /\.css$/,     // 处理以css结尾的文件 正则匹配规则表示以css结尾的文件名，以下列loader打包转换此文件
        use: [              // use数组中loader执行顺序：从右到左，从下到上，依次执行(先执行css-loader)
            'style-loader', // style-loader：创建style标签，将js中的样式资源插入进去，添加到head中生效
            'css-loader'    // css-loader：将css文件变成commonjs模块加载到js中，里面内容是样式字符串
        ], 
      },
      {
        test: /\.(scss|sass)$/,  //处理scss或sass结尾的文件
        use: [
          'style-loader',
          'css-loader',
          'sass-loader'
        ],
      },
      {
        test: /\.(jpg|png|gif)$/, // 处理图片资源，但处理不了html中的img图片
        loader: 'url-loader',
        options: {
          // 图片大小小于8kb，就会被base64处理，优点：减少请求数量（减轻服务器压力），缺点：图片体积会更大（文件请求速度更慢）
          // base64在客户端本地解码所以会减少服务器压力，如果图片过大还采用base64编码会导致cpu调用率上升，网页加载时变卡
          limit: 8 * 1024, 
          // 给图片重命名，[hash:10]：取图片的hash的前10位，[ext]：取文件原来扩展名
          name: '[hash:10].[ext]',  
          // 关闭url-loader的es6模块化，使用commonjs解析，为了配合html-loader，让html中引用的图片也加载出来
          esModule: false, 
          outputPath: 'imgs',
        },
      },
      {
        test: /\.html$/,  // 处理html文件的图片资源
        loader: 'html-loader',
      },
      { 
        loader: 'file-loader', // 打包其他资源(除了html/js/css资源以外的资源)
        exclude: /\.(html|js|css|less|jpg|png|gif)/, //// 排除html|js|css|less|jpg|png|gif文件
        options: {
          name: '[hash:10].[ext]',
          outputPath: 'media',
        },
      },
    ],
  },
    
  // plugin 插件的配置
  plugins: [
    // 处理html文件
    new HtmlWebpackPlugin({ 
      // 复制./src/index.html文件，并自动引入打包输出的所有资源（JS/CSS）
      // 注：原index.html文件中不要引入任何资源文件，如果引入了会导致引用两次，会出错
      template: './src/index.html',
    }),
  ],
    
  
  // 开发服务器 devServer：用来自动化，不用每次修改后都重新输入webpack打包一遍（自动编译，自动打开浏览器，自动刷新浏览器）
  // 特点：只会在内存中编译打包，不会有任何输出（不会像之前那样在外面看到打包输出的build包，而是在内存中，关闭后会自动删除）
  // 启动devServer指令为：npx webpack-dev-server
  devServer: {
    // 项目构建后路径
    contentBase: resolve(__dirname, 'build'),
    // 启动gzip压缩
    compress: true,
    // 端口号
    port: 3000,
    //编译后, 自动打开浏览器
    open: true,
  },
    
  mode: 'development' // 开发模式
}


//如果要启动devServer，则执行命令npx webpack-dev-server
```





### 五、生产环境的基本配置

------

`开发环境配置主要目的是为了能让代码 1.性能更好 2.兼容性更好 3.能在线上环境正常运行`

#### 1. 提取 Css 成单独文件

> 对css文件进行优化处理, 提取css成单独的文件 好处：
>
> * 将css从style变成link导入，解决闪屏问题 
> * 将css从js抽离出来了，可以减少js的体积，增加js的加载速度
>
> 插件：npm install mini-css-extract-plugin -d

```css
//webpack.config.js文件

const MiniCssExtractorPlugin = require('mini-css-extract-plugin')

// webpack配置
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.css$/,
        MiniCssExtractPlugin.loader,  // 这个loader取代style-loader。作用：提取js中的css成单独文件
        'css-loader' // 将css文件整合到js文件中
      },
      //...
    ],
  },
    
  plugins: [
    new MiniCssExtractPlugin({
      filename: 'css/built.css', // 对输出的css文件进行重命名
    }),
  ],

  // 生产环境
  mode: 'production'
}
```



#### 2. Css的兼容性处理

> css兼容性处理需要使用到一个库 postcss 这个库在webpack中使用需要 postcss-loader 和 postcss-preset-env插件
>
> 注：需要在package.json增加配置选项
>
> 下载：npm install postcss-loader postcss-preset-env -d

```css
//webpack.config.js文件

const MiniCssExtractorPlugin = require('mini-css-extract-plugin')

//webpack配置
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
          {
             // css兼容性的处理，修改css-loader的配置, 同时需要在package.json中browserslist里面增加配置
            loader: 'postcss-loader',
            options: {
              ident: 'postcss',
              plugins: () => [
                // postcss的插件
                require('postcss-preset-env')()
              ]
            }
          }
        ]
      },
      //...
    ],
  },
    
  plugins: [
    //...
  ],

  // 生产环境
  mode: 'production'
}

//package.json文件
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
},
```



#### 3. 压缩Css文件

> 插件：npm install optimize-css-assets-webpack-plugin -d

```css
//webpack.config.js文件

//css压缩插件
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')

//webpack配置
module.exports = {
  //...
    
  plugins: [
    // 压缩css
    new OptimizeCssAssetsWebpackPlugin()
    //...
  ],

  // 生产环境
  mode: 'production'
}
```



#### 4. JavaScript 语法检查

> JavaScript的语法检查库：eslint  Webpack中对应得库是eslint和eslint-loader，检查规则在package.json中设置
>
> 检查规则使用的是airbnb 对于的库为eslint-config-airbnb-base 而此库依赖eslint和eslint-plugin-import
>
> 1.检查js的语法是否有错误 
>
> 2.统一js的写法规范，统一写法风格 
>
> 
>
> 下载相关库：
>
> npm install eslint-loader -d
> npm install eslint -d
> npm install eslint-config-airbnb-base -d
> npm install eslint-plugin-import -d

```css
//webpack.config.js文件

//webpack配置
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.js$/, //js语法检查
        exclude: /node_modules/,
        loader: 'eslint-loader', //不检查第三方库
        options: {
          fix: true // 检查出错误后，自动修复eslint的错误
        }
      },
      //...
    ],
  },

  // 生产环境
  mode: 'production'
}
```



#### 5. JavaScript 兼容性处理

> 需要用到的库 babel-loader 可以将es6的语法转换成es5的语法, babel-loader 依赖于@babel/core库和@babel/preset-env
> 但babel-loader库有个缺点，他只能转换基本语法，有些es6高级语法如promise就不能转换
> 所以此时我们还需增加一个库core-js，@babel/preset-env和core-js配合就可以实现兼容性的处理，并会按需加载
>
> 下载相关库：
>
> npm install babel-loader -d
> npm install @babel/core -d
> npm install @babel/preset-env -d
> npm install core-js -d

```css
//webpack.config.js文件

//webpack配置
module.exports = {
  //...
  module: {
    rules: [
      {  
        test: /\.js$/,  //js的兼容性处理
        exclude: /node_modules/,
        loader: 'babel-loader',
        options: {
          // 预设：指示babel做怎么样的兼容性处理
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
                // 指定兼容性做到哪个版本浏览器
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
      //...
    ],
  },
    
  plugins: [
    //...
  ],

  // 生产环境
  mode: 'production'
}

//正常来说 一个类型只能被一个loader处理，但如果一个文件要被多个loader处理，比如js的语法和兼容性检查
//那么就要指定执行先后顺序, 字段: enforece:'pre'
module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.js$/, // js语法检查
        exclude: /node_modules/,
        loader: 'eslint-loader', 
        enforce: 'pre',  // 优先执行此顺序
        options: {
          fix: true 
        }
      },
      {  
        test: /\.js$/, // js兼容性处理
        exclude: /node_modules/,
        loader: 'babel-loader',
        options: {
          presets: [
            [
              '@babel/preset-env',
              {
                useBuiltIns: 'usage',
                corejs: {
                  version: 3
                },
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
      //...
    ],
  },
    
  plugins: [
    //...
  ],
      
  // 生产环境
  mode: 'production',
}
```



#### 6. JS的压缩和Html的压缩

> js在生产环境下会自动进行js压缩
> html的压缩则需要配置插件

```
//webpack.config.js文件

//webpack配置
module.exports = {
  //...
    
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html',
      // 压缩html代码
      minify: {
        // 移除空格
        collapseWhitespace: true,
        // 移除注释
        removeComments: true
      }
    }),
    //...
  ],
  
  // 生产环境
  mode: 'production',
}
```



#### 7. 生产环境的基本配置 完整写法

```css
//webpack.config.js文件

const { resolve } = require('path')
const MiniCssExtractorPlugin = require('mini-css-extract-plugin')
const OptimiziCssAssetsWebpackPlugin = require('optimizi-css-assets-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')

// 定义node.js的环境变量，决定使用browserslist的哪个环境
process.env.NODE_ENV = 'production'

// 复用loader的写法
const commonCssLoader = [
  // 这个loader取代style-loader。作用：提取js中的css成单独文件然后通过link加载
  MiniCssExtractPlugin.loader,
  // css-loader：将css文件整合到js文件中
  // 经过css-loader处理后，样式文件是在js文件中的
  // 问题：1.js文件体积会很大2.需要先加载js再动态创建style标签，样式渲染速度就慢，会出现闪屏现象
  // 解决：用MiniCssExtractPlugin.loader替代style-loader
  'css-loader',
  /*
    postcss-loader：css兼容性处理：postcss --> 需要安装：postcss-loader postcss-preset-env
    postcss需要通过package.json中browserslist里面的配置加载指定的css兼容性样式
    在package.json中定义browserslist：
    "browserslist": {
      // 开发环境 --> 设置node环境变量：process.env.NODE_ENV = development
      "development": [ // 只需要可以运行即可
        "last 1 chrome version",
        "last 1 firefox version",
        "last 1 safari version"
      ],
      // 生产环境。默认是生产环境
      "production": [ // 需要满足绝大多数浏览器的兼容
        ">0.2%",
        "not dead",
        "not op_mini all"
      ]
    },
  */
  {
    loader: 'postcss-loader',
    options: {
      ident: 'postcss', // 基本写法
      plugins: () => [
        // postcss的插件
        require('postcss-preset-env')(),
      ],
    },
  },
]

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build'),
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [...commonCssLoader],
      },
      {
        test: /\.less$/,
        use: [...commonCssLoader, 'less-loader'],
      },
      /*
        正常来讲，一个文件只能被一个loader处理
        当一个文件要被多个loader处理，那么一定要指定loader执行的先后顺序
        先执行eslint再执行babel（用enforce）
      */
      {
        /*
          js的语法检查： 需要下载 eslint-loader eslint
          注意：只检查自己写的源代码，第三方的库是不用检查的
          airbnb(一个流行的js风格) --> 需要下载 eslint-config-airbnb-base eslint-plugin-import
          设置检查规则：
            package.json中eslintConfig中设置
              "eslintConfig": {
                "extends": "airbnb-base"， // 继承airbnb的风格规范
                "env": {
                  "browser": true // 可以使用浏览器中的全局变量(使用window不会报错)
                }
              }
        */
        test: /\.js$/,
        exclude: /node_modules/, // 忽略node_modules
        enforce: 'pre', // 优先执行
        loader: 'eslint-loader',
        options: {
          // 自动修复
          fix: true,
        },
      },
      /*
        js兼容性处理：需要下载 babel-loader @babel/core
          1. 基本js兼容性处理 --> @babel/preset-env
            问题：只能转换基本语法，如promise高级语法不能转换
          2. 全部js兼容性处理 --> @babel/polyfill
            问题：只要解决部分兼容性问题，但是将所有兼容性代码全部引入，体积太大了
          3. 需要做兼容性处理的就做：按需加载  --> core-js
      */
      {
        // 第三种方式：按需加载
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
        options: {
          // 预设：指示babel做怎样的兼容性处理
          presets: [
            '@babel/preset-env', // 基本预设
            {
              useBuiltIns: 'usage', //按需加载
              corejs: { version: 3 }, // 指定core-js版本
              targets: { // 指定兼容到什么版本的浏览器
                chrome: '60',
                firefox: '50',
                ie: '9',
                safari: '10',
                edge: '17'
              },
            },
          ],
        },
      },
      {
        // 图片处理
        test: /\.(jpg|png|gif)/,
        loader: 'url-loader',
        options: {
          limit: 8 * 1024,
          name: '[hash:10].[ext]',
          outputPath: 'imgs',
          esModule: false, // 关闭url-loader默认使用的es6模块化解析
        },
      },
      // html中的图片处理
      {
        test: /\.html$/,
        loader: 'html-loader',
      },
      // 处理其他文件
      {
        exclude: /\.(js|css|less|html|jpg|png|gif)/,
        loader: 'file-loader',
        options: {
          outputPath: 'media',
        },
      },
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({
      // 对输出的css文件进行重命名
      filename: 'css/built.css',
    }),
    // 压缩css
    new OptimiziCssAssetsWebpackPlugin(),
    // HtmlWebpackPlugin：html文件的打包和压缩处理
    // 通过这个插件会自动将单独打包的样式文件通过link标签引入
    new HtmlWebpackPlugin({
      template: './src/index.html',
      // 压缩html代码
      minify: {
        // 移除空格
        collapseWhitespace: true,
        // 移除注释
        removeComments: true,
      },
    }),
  ],
  // 生产环境
  mode: 'production',
}
```



### 六、开发环境 配置优化

------

#### 1. HMR（模块热替换）

> HMR: hot module replacement 热模块替换 / 模块热替换
>
> 作用：一个模块发生变化，只会重新打包构建这一个模块（而不是打包所有模块） ，极大提升构建速度
>
> 缺点：1.此HRM功能只对样式css文件有效，对js无效  2.会让html失去热更新功能
>
> * 如果想要js文件实现HRM功能，则需要修改一些代码，但对入口js文件不适用(入口js文件修改，还是会全部打包)
>
> * 如果想要html有效，则需要再entry入口里面，将html文件引入 entry: ['./src/js/index.js', './src/index.html'],
>
> 代码：只需要在 devServer 中设置 hot 为 true，就会自动开启HMR功能（只能在开发模式下使用）

```css
//webpack.config.js文件

devServer: {
  contentBase: resolve(__dirname, 'build'),
  compress: true,
  port: 3000,
  open: true,
  // 开启HMR功能
  // 当修改了webpack配置，新配置要想生效，必须重启webpack服务
  hot: true
}

//注：此HRM功能只对样式css文件有效，对js文件(js修改，还是会全部重新打包)和 html失去热更新功能
   如果想要js文件实现HRM功能，则需要修改一些代码，但对入口js文件不适用(入口js文件修改，还是会全部打包)
   如果想要html有效，则需要再entry入口里面，将html文件引入 entry: ['./src/js/index.js', './src/index.html'],
```



#### 2. Source-Map  代码错误调试

> source-map: 如果代码构建后出错了，通过映射可以追踪个源代码错误
>
> 他有内联和外部两种：
>
> * 内联构建速度更快，但生成了一些文件，会让包体积变大 (适合开发环境)   
> * 外部慢，但不会生成文件(适合生产环境)
>
>  有多种前缀取值：\[inline-|hidden-|eval-]\[nosources-][cheap-[module-]]source-map
>
> source-map：外部                          有错误代码准确信息，有源代码的错误位置
> cheap-source-map：外部                有错误代码准确信息，有源代码的错误位置  但只能精确的行  
> hidden-source-map：外部               有错误代码错误原因，没有错误位置 不能追踪源代码错误，只能提示到构建后代码的错误位置
> nosources-source-map：外部           有错误代码错误原因,   无任何源代码信息
> heap-module-source-map：外部     有错误代码准确信息，有源代码的错误位置 module会将loader的source map加入     
> inline-source-map：内联                   有错误代码准确信息，有源代码的错误位置  只生成一个内联source-map
> eval-source-map：内联                      有错误代码准确信息，有源代码的错误位置  每一个文件都生成对应的source-map，都在eval  
>
> 速度：eval>inline>cheap>...
>
> 开发环境：eval-source-map  / eval-cheap-module-souce-map
>
> 生产环境：source-map / cheap-module-souce-map   

```css
//webpack.config.js文件

//...

devtool: source-map'  //开发环境使用 eval-source-map  生产环境使用source-map

// 生产环境
mode: 'production',
```





### 七、生产环境 优化构建速度

------

#### 1. OneOf

> 作用：让每个规则只匹配一个文件，大大增加构建速度
>
> module里面的rules里，每个规则都会扫一遍全部的文件，这样会大大增加构建的速度，
> 而加了OneOf，就可以让每个规则只匹配一个文件，大大增加构建速度，但不能有两个配置处理同一种类型文件，如js

```
//webpack.config.js文件

module: {
    rules: [
      {
        test: /\.js$/, //js语法检查
        exclude: /node_modules/,
        enforce: 'pre', // 优先执行
        loader: 'eslint-loader',
        options: {
          fix: true
        }
      },
      {
        // 以下loader只会匹配一个
        // 注意：不能有两个配置处理同一种类型文件
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
            test: /\.js$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
            options: {
              presets: [
                [
                  '@babel/preset-env',
                  {
                    useBuiltIns: 'usage',
                    corejs: {version: 3},
                    targets: {
                      chrome: '60',
                      firefox: '50'
                    }
                  }
                ]
              ]
            }
          }
          }
        ]
      }
    ]
  },
  
  plugins: [
    new MiniCssExtractPlugin({
      filename: 'css/built.[contenthash:10].css'
    })
    //...
  ],
  // 生产环境
  mode: 'production'
}
```



#### 2. 缓存 

> 作用：如果已经构建过了且没有变化，则直接读取缓存，如果有变化，则构建后读取
>
> 1. 对babel进行换缓存  只需要在loader: 'babel-loader', 加一行cacheDirectory: true就可以实现babel缓存
>
> 2. 对文件资源进行缓存
>
>    当别人访问你的服务时，css、js资源会被强制缓存下来，当下次访问时，不会重新加载资源，而是直接读缓存，
>    而决定走缓存还是重新加载资源，是根据资源名字是否改变决定的
>
>    比如a.js 内容为1，index.html引入a.js， 第一次会加载资源，第二次会走缓存，而此时你把a.js内容改为1、2，
>
>    index.html引入a.js，则别人再次访问时，还是会读取内容为1的js，因为a.js名称没变，会一直读取原来的缓存
>
>     
>
> Webpack中有3种hash值
>
> hash:  每次webpack构建时会生成一个唯一的hash值。
>
> chunkhash：根据chunk生成的hash值。如果打包来源于同一个chunk，那么hash值就一样
>
> contenthash: 根据文件的内容生成hash值。不同文件hash值一定不一样，可以打到改变那个文件，那个文件的文件名改变的效果
>
> 使用hash或chunkhash时，如果只改变了css，则会导致css和js都会重新生成一个新的hash值,就会导致改变1个文件，而多个文件同时变化的问题
>
> 而contenthash则是根据内容是否改变来决定是否生产新的hash值，不会出现改变css文件，而相关联文件也发生变化的情况，所以对文件资源进行缓存用contenthash



```
//webpack.config.js文件

module: {
    entry: './src/js/index.js', 
      
    output: {
        filename: 'js/built.[contenthash:10].js', //输出的文件名改为contenthash
        path: resolve(__dirname, 'build'), 
    },
    
    rules: [
      {
        test: /\.js$/, //js语法检查
        exclude: /node_modules/,
        enforce: 'pre', // 优先执行
        loader: 'eslint-loader',
        options: {
          fix: true
        }
      },
      {
        // 以下loader只会匹配一个
        // 注意：不能有两个配置处理同一种类型文件
        oneOf: [
          ../
          {
            test: /\.js$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
            options: {
              presets: [
                [
                  '@babel/preset-env',
                  {
                    useBuiltIns: 'usage',
                    corejs: {version: 3},
                    targets: {
                      chrome: '60',
                      firefox: '50'
                    }
                  }
                ]
              ],
              // 开启babel缓存
              // 第二次构建时，会读取之前的缓存，速度更快
              cacheDirectory: true
            }
          }
        ]
      }
    ]
  },
```



#### 3. 多进程打包

> 多进程打包：某个任务消耗时间较长会卡顿，多进程可以同一时间干多件事，效率更高。
>
> 优点是提升打包速度，缺点是每个进程的开启和交流都会有开销（babel-loader消耗时间最久，所以使用thread-loader针对其进行优化）
>
> 建议项目比较大，或者工作消耗时间比较长，才需要多进程打包
>
> 安装命令：npm install thread-loader -d

```
//webpack.config.js文件

module.exports = {
  //...
  module: {
    rules: [
      {  
        test: /\.js$/,  //js的兼容性处理
        exclude: /node_modules/,
        loader: 'babel-loader',
        options: {
          presets: [
            [
              '@babel/preset-env',
              {
                useBuiltIns: 'usage',
                corejs: {
                  version: 3
                },
                targets: {
                  chrome: '60',
                  firefox: '60',
                  ie: '9',
                  safari: '10',
                  edge: '17'
                }
              }
            ]
          ],
          // 开启babel缓存第二次构建时，会读取之前的缓存
          cacheDirectory: true
        }
      //...
    ],
  },
    
  plugins: [
    //...
  ],
  
}
```



#### 4. dll 动态链接库

> dll动态链接库，可以对某些库(第三方库jquery、react、vue...)进行单独打包
> 作用：只需要一开始对第三方库打包一次，后面再打包时，只需要打包源代码，不再对第三方库打包了，这样编译会快很多
>
> 需要新增一个 webpack.dll.js文件

```
//webpack.dll.js文件
/*
  使用dll技术，对某些库（第三方库：jquery、react、vue...）进行单独打包
    当你运行 webpack 时，默认查找 webpack.config.js 配置文件
    需求：需要运行 webpack.dll.js 文件 
    --> webpack --config webpack.dll.js  
*/

const { resolve } = require('path');
const webpack = require('webpack');

module.exports = {
  entry: {
    // 最终打包生成的[name] --> jquery
    // ['jquery'] --> 要打包的库是jquery
    jquery: ['jquery'],
  },
  output: {
    filename: '[name].js',
    path: resolve(__dirname, 'dll'),
    library: '[name]_[hash]' // 打包的库里面向外暴露出去的内容叫什么名字
  },
  plugins: [
    // 打包生成一个 manifest.json --> 提供和jquery映射
    new webpack.DllPlugin({
      name: '[name]_[hash]', // 映射库的暴露的内容名称
      path: resolve(__dirname, 'dll/manifest.json') // 输出文件路径
    })
  ],
  mode: 'production'
};


//webpack.config.js文件

  //...
  
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    }),
    // 告诉webpack哪些库不参与打包，同时使用时的名称也得变~
    new webpack.DllReferencePlugin({
      manifest: resolve(__dirname, 'dll/manifest.json')
    }),
    // 将某个文件打包输出去，并在html中自动引入该资源
    new AddAssetHtmlWebpackPlugin({
      filepath: resolve(__dirname, 'dll/jquery.js')
    })
  ],
```





### 八、生产环境 优化代码运行的性能

------

#### 1. tree shacking 树摇

> 前提： 1.必须使用ES6模块化   2.开启production环境（这样就自动会把无用代码去掉）
>
> 作用：去除无用代码 减少代码体积
>
> 在 package.json 中配置：
>
> `"sideEffects": false` 表示所有代码都没有副作用（都可以进行 tree shaking）
>
> 这样会导致的问题：可能会把 css / @babel/polyfill 文件干掉（副作用）
>
> 所以可以配置：`"sideEffects": ["*.css", "*.less"]` 不会对css/less文件tree shaking处理



#### 2. code split 代码分割

> 代码分割，将打包输出的一个大的 bundle.js 文件拆分成多个小文件，这样可以并行加载多个文件，比加载一个文件更快。

```css
//webpack.config.js文件

optimization: {
  splitChunks: {
      chunks: 'all'
  }
},
  


//xxx.js文件
/*
  在js代码中通过js代码，让某个文件被单独打包成一个chunk
  import动态导入语法：能将某个文件单独打包(test文件不会和index打包在同一个文件而是单独打包)
  webpackChunkName:指定test单独打包后文件的名字
*/
import(/* webpackChunkName: 'test' */'./test')
  .then(({ mul, count }) => {
    // 文件加载成功~
    // eslint-disable-next-line
    console.log(mul(2, 5));
  })
  .catch(() => {
    // eslint-disable-next-line
    console.log('文件加载失败~');
  });
```



### 九、Webpack 配置详解

---

#### 1. Entry 的四种配置

```
//Entry的四种写法

//第一种：单入口 string类型 -->  entry: './src/index.js'
module: {
    //单入口 string类型 
    entry: './src/js/index.js', 
      
    output: {
        filename: '[name].js',  //最终会输出一个文件，文件名为main.js
        path: resolve(__dirname, 'build'), 
    },
}

//第二种：多入口 array类型  --> entry: ['./src/index.js', './src/add.js']
module: {
    //多入口 array类型
    entry: ['./src/index.js', './src/add.js']
      
    output: {
        filename: '[name].js',  //最终会输出一个文件，文件名为main.js
        path: resolve(__dirname, 'build'), 
    },
}

//第三种：多入口 object类型
module: {
    //多入口 object类型
    entry: {
        index: './src/index.js',
        add: './src/add.js'
    },
      
    output: {
        filename: '[name].js',  //有几个入口文件就形成几个chunk，输出几个bundle文件，最终会生成index.js文件和add.js文件
        path: resolve(__dirname, 'build'), 
    },
}

//第四种：多入口的特殊用法
module: {
    //多入口特殊用法
    entry: {
    	index: ['./src/index.js', './src/count.js'], 
    	add: './src/add.js'
  	},
      
    output: {
        //会生成2个文件，index.js和count.js结合会生成一个index.js文件，add.js会转化生成一个add.js文件
        filename: '[name].js',  
        path: resolve(__dirname, 'build'), 
    },
}
```



#### 2. Output 配置

```css
//Output配置

const { resolve } = require('path')  // resolve用来拼接绝对路径的方法

module: {
    entry: './src/js/index.js',
      
    output: {
        // 文件名称（指定名称+目录）
        filename: 'js/[name].js',
        // 输出文件目录（将来所有资源输出的公共目录） __dirname代表当前文件的目录绝对路径
        path: resolve(__dirname, 'build'),
        // 所有资源引入公共路径前缀 --> 'imgs/a.jpg' --> '/imgs/a.jpg'  如果有/在代表在根目录下找
        publicPath: '/',
        chunkFilename: 'js/[name]_chunk.js', // 非入口chunk的名称
        // library: '[name]', // 整个库向外暴露的变量名
        // libraryTarget: 'window' // 变量名添加到哪个上 browser
        // libraryTarget: 'global' // 变量名添加到哪个上 node
        // libraryTarget: 'commonjs'
    },
}
```



#### 3. Module(Loader)配置

```
//Module 配置

const { resolve } = require('path')  // resolve用来拼接绝对路径的方法

module: {
  //...
    
  module: {
    rules: [
      //loader的配置
      {
        test: /\.css$/,
        // 多个loader用use
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.js$/,
         // 单个loader用loader
        loader: 'eslint-loader',
        // 排除node_modules下的js文件
        exclude: /node_modules/,
        // 只检查 src 下的js文件
        include: resolve(__dirname, 'src'),
        // 优先执行 默认为中间执行  post最后执行
        enforce: 'pre',
        //配置一些参数
        options: {}
      },
      {
        // 以下配置只会生效一个
        oneOf: []
      }
    ],
  },
}
```



#### 4.dev server

```
devServer: {
  // 运行代码所在的目录
  contentBase: resolve(__dirname, 'build'),
  // 监视contentBase目录下的所有文件，一旦文件变化就会reload
  watchContentBase: true,
  watchOptions: {
    // 忽略文件
    ignored: /node_modules/
  },
  // 启动gzip压缩
  compress: true,
  // 端口号
  port: 5000,
  // 域名
  host: 'localhost',
  // 自动打开浏览器
  open: true,
  // 开启HMR功能
  hot: true,
  // 不要显示启动服务器日志信息
  clientLogLevel: 'none',
  // 除了一些基本信息外，其他内容都不要显示
  quiet: true,
  // 如果出错了，不要全屏提示
  overlay: false,
  // 服务器代理，--> 解决开发环境跨域问题
  proxy: {
    // 一旦devServer(5000)服务器接收到/api/xxx的请求，就会把请求转发到另外一个服务器3000
    '/api': {
      target: 'http://localhost:3000',
      // 发送请求时，请求路径重写：将/api/xxx --> /xxx （去掉/api）
      pathRewrite: {
        '^/api': ''
      }
    }
  }
}

//其中，跨域问题：同源策略中不同的协议、端口号、域名就会产生跨域。
正常的浏览器和服务器之间有跨域，但是服务器之间没有跨域。代码通过代理服务器运行，所以浏览器和代理服务器之间没有跨域，浏览器把请求发送到代理服务器上，代理服务器替你转发到另外一个服务器上，服务器之间没有跨域，所以请求成功。代理服务器再把接收到的响应响应给浏览器。这样就解决开发环境下的跨域问题。
```



#### 5. optimization

> contenthash 缓存会导致一个问题：修改 a 文件导致 b 文件 contenthash 变化。
> 因为在 index.js 中引入 a.js，打包后 index.js 中记录了 a.js 的 hash 值，而 a.js 改变，其重新打包后的 hash 改变，导致 index.js 文件内容中记录的 a.js 的 hash 也改变，从而重新打包后 index.js 的 hash 值也会变，这样就会使缓存失效。（改变的是a.js文件但是 index.js 文件的 hash 值也改变了）
> 解决办法：runtimeChunk --> 将当前模块记录其他模块的 hash 单独打包为一个文件 runtime，这样 a.js 的 hash 改变只会影响 runtime 文件，不会影响到 index.js 文件

```
output: {
  filename: 'js/[name].[contenthash:10].js',
  path: resolve(__dirname, 'build'),
  chunkFilename: 'js/[name].[contenthash:10]_chunk.js' // 指定非入口文件的其他chunk的名字加_chunk
},
optimization: {
  splitChunks: {
    chunks: 'all',
    /* 以下都是splitChunks默认配置，可以不写
    miniSize: 30 * 1024, // 分割的chunk最小为30kb（大于30kb的才分割）
    maxSize: 0, // 最大没有限制
    minChunks: 1, // 要提取的chunk最少被引用1次
    maxAsyncRequests: 5, // 按需加载时并行加载的文件的最大数量为5
    maxInitialRequests: 3, // 入口js文件最大并行请求数量
    automaticNameDelimiter: '~', // 名称连接符
    name: true, // 可以使用命名规则
    cacheGroups: { // 分割chunk的组
      vendors: {
        // node_modules中的文件会被打包到vendors组的chunk中，--> vendors~xxx.js
        // 满足上面的公共规则，大小超过30kb、至少被引用一次
        test: /[\\/]node_modules[\\/]/,
        // 优先级
        priority: -10
      },
      default: {
        // 要提取的chunk最少被引用2次
        minChunks: 2,
        prority: -20,
        // 如果当前要打包的模块和之前已经被提取的模块是同一个，就会复用，而不是重新打包
        reuseExistingChunk: true
      }
    } */
  },
  // 将index.js记录的a.js的hash值单独打包到runtime文件中
  runtimeChunk: {
    name: entrypoint => `runtime-${entrypoint.name}`
  },
  minimizer: [
    // 配置生产环境的压缩方案：js/css
    new TerserWebpackPlugin({
      // 开启缓存
      cache: true,
      // 开启多进程打包
      parallel: true,
      // 启用sourceMap(否则会被压缩掉)
      sourceMap: true
    })
  ]
}
```



### 十、我学到了什么

---

> 1. Webpack是什么？为什么要使用
> 2. Webpack的整体执行流程  3'
> 3. Webpack五个核心概念
>    1. Entry
>    2. Output
>    3. Loader
>    4. Plugins
>    5. Mode
> 4. Webpack开发环境配置
> 5. Webpack正式环境配置
> 6. Webpack的开发环境配置优化
> 7. Webpack的正式环境配置优化
> 8. Webpack的配置详解详解





### 十一、学习参考

---

* 视频：<https://www.bilibili.com/video/BV1e7411j7T5>

* 文档：<http://www.woc12138.com/article/45>



