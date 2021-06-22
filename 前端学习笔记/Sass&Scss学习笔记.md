#### CSS预处理器 · Sass/Scss 学习笔记



## 一、介绍

> CSS预处理器是一个CSS的增强语法，让我们可以在 CSS 中使用变量、循环、嵌套等功能, 可以让我们的代码变通性更强，可读性、层级关系更加明显
> CSS预处理器种类繁多，目前三种主流的css预处理器是Sass（Scss）、Less及Stylus



### Sass/Scss、Less、Stylus介绍

- **Sass/Scss：**
  2007年诞生，最早也是最成熟的CSS预处理器，拥有ruby社区的支持和compass这一最强大的css框架。
  目前受LESS影响，已经进化到了全面兼容CSS的SCSS（SCSS 需要使用分号和花括号而不是换行和缩进）。

  1.文件扩展名不同，Sass 是以“.sass”后缀为扩展名，而 SCSS 是以“.scss”后缀为扩展名
  2.语法书写方式不同，Sass 是以严格的缩进式语法规则来书写，不带大括号({})和分号(而 SCSS 的语法书写和我们的 CSS 语法书写方式非常类似。

  

- **Less：**
  2009年出现，受SASS的影响较大，但又使用CSS的语法，相对于Sacc其优点是简单上手和兼容CSS
  其缺点是比起SASS来，他的编程功能不够，不好用。他反过来也影响了SASS演变到了SCSS的时代，著名的Twitter Bootstrap就是采用LESS做底层语言的。

  

- **Stylus：**
  2010年产生，来自Node.js社区。
  主要用来给Node项目进行CSS预处理支持，在此社区之内有一定支持者，在广泛的意义上人气还完全不如SASS和LESS







## 二、环境搭建

------

###  **Windows**

```scss
1.先从官网下载Ruby，安装过程中请注意勾选Add Ruby executables to your PATH添加到系统  官网
  https://rubyinstaller.org/downloads/
2.ruby -v 如安装成功会打印版本信息
3.gem update --system //该命令请翻墙一下  升级版本，建议 2.6.x 以上
   gem -v
4.安装sass  
   gem install sass
   gem install compass
   sass -v
```

### **Mac**

```css
1.系统自带Ruby环境
2.安装sass  
   gem install sass
   gem install compass
   sass -v
```

### **安装后将sass文件转换成css文件**

```scss
//单文件转换命令
sass input.scss output.css

//单文件监听命令
sass --watch input.scss:output.css

//如果你有很多的sass文件的目录，你也可以告诉sass监听整个目录：
sass --watch app/sass:public/stylesheets
```



### **Vue里的Sass 环境搭建**

```scss
1.安装nodejs，拥有nodejs环境
2.创建vue项目
3.在项目中安装sass环境，需要两个依赖 sass-loader， sass-loader依赖于node-sass,
  npm install -D node-sass sass-loader
4.在style标签上加上一个属性：lang=”scss” 就可以了
   <style lang="scss">
     .app{
       .....
     }
   </style>
```



### 安装环境参考:

> <https://www.sass.hk/install/>





## 三、Sass/Scss学习

### 一、嵌套写法

Sass 允许将一套 CSS 样式嵌套进另一套样式中，内层的样式将它外层的选择器作为父选择器

```scss
#main {
  color: #00ff00;
  width: 97%;

  .redbox {
    background-color: #ff0000;
    color: #000000;
  }
}

// 编译后, 转为css后的结果:
#main  {
  color: #00ff00;
  width: 97%; }

#main .redbox {
    background-color: #ff0000;
    color: #000000; }
```



### 二、变量

#### 1. 变量的定义及使用

~~~scss
$width: 1600px;
$pen-size: 3em;
.app {
    height: $width;
    font-size: $pen-size;
}
~~~

#### 2. 局部变量

```scss
#foo {
  $width: 5px;
  width: $width; //编译时没问题
}

#bar {
  width: $width; //编译时会报错
}
```





------

### 三、数据类型

SassScript 支持 7 种主要的数据类型：

- 数字，`1, 2, 13, 10px`
- 字符串，有引号字符串与无引号字符串，`"foo", 'bar', baz`
- 布尔型，`true, false`
- 颜色，`blue, #04a3f9, rgba(255,0,0,0.5)`
- 数组 (list)，用空格或逗号作分隔符，`1.5em 1em 0 2em, Helvetica, Arial, sans-serif`
- maps, 相当于 JavaScript 的 object，`(key1: value1, key2: value2)`
- 空值，`null`

```scss
判断数据类型的方式：type-of($value)
```



#### 1. 数字(Numbers)

~~~scss
$my-age: 19;
$your-age: 19.5;
$height: 120px; // 单位会和数字当做一个整体，进行算数运算
~~~



#### 2. 字符串 (Strings)

~~~scss
$name: 'Tom Bob';
$container: "top bottom";
$what: heart;
~~~



#### 3. 布尔型(Booleans)

~~~scss
$a: true;
$b: false;  // 只有自身是false和null才会返回false，其他一切都将返回true
~~~



#### 4. 颜色 (Colors)

~~~scss
//有多种表达方式
$color: #04a3f9;
$color0: green;
$color1: rgb(255, 255, 0);
$color2: rgba(#f3f3f3m 0.3);  //这种写法css会报错，但scss支持 特别的好用的写法
$color3: lighten($color, 15%);
$color4: darken($color, 15%);
~~~



#### 5. 数组 (Lists)

~~~scss
$list0: 1px 2px 5px 6px;  //普通的数组 用空格隔开
$list1: 1px 2px, 5px 6px; //二维数组 用逗号隔开
~~~



#### 6. 映射(Maps)

~~~scss
//以圆括号包含内容
$map: (      
  $key1: value1, 
  $key2: value2, 
  $key3: value3 
)
~~~



#### 7. 空值(Null)

~~~scss
$value: null; 
~~~





------

###  四、运算符

#### 1. 数字运算符 `+, -, *, /, %`

- ##### `+ 号运算符`

  ~~~scss
  // 纯数字 
  $add2: 1 + 2px; // 3px
  $add3: 1px + 2; // 3px
  $add4: 1px + 2px;//3px
  
  // 纯字符串
  $add5: "a" + "b"; // "ab"
  $add6: "a" + b;	  // "ab"
  $add7: a + "b";	  // ab
  $add8: a + b;	  // ab
  
  // 数字和字符串 
  $add9: 1 + a;	// 1a
  $adda: a + 1;	// a1
  $addb: "1" + a; // "1a"
  $addc: 1 + "a"; // "1a"
  $addd: "a" + 1; // "a1"
  $adde: a + "1"; // a1
  $addf: 1 + "1"; // "11"
  
  // 总结：
  a.纯数字：只要有单位，结果必有单位
  b.纯字符串：第一个字符串有无引号决定结果是否有引号
  c.数字和字符串：第一位有引号，结果必为引号；第一位对应数字非数字且最后一位带有引号，则结果必为引号
  
  ~~~

  

- ##### `- 号运算符`

  ~~~scss
  $add1: 1 - 2;	// -1
  $add2: 1 - 2px; // -1px
  $add3: 1px - 2; // -1px
  $add4: 1px - 2px;//-1px
  
  $sub1: a - 1;  // a-1
  $sub2: 1 - a;  // 1-a
  $sub3: "a" - 1;// "a"-1
  $sub4: a - "1";// a-"1"
  ~~~

  

- ##### `* 号运算符`

  ~~~scss
  $num1: 1 * 2;    // 2
  $mul2: 1 * 2px;  // 2px
  $num3: 1px * 2;  // 2px
  $num4: 2px * 2px;// 编译不通过 两个字段只能一个后部分是字符
  $num5: 1 * 2abc; // 2abc
  ~~~

  

- ##### `/ 号运算符`

  ~~~scss
  // 总结：
  a.不会四舍五入，精确到小数点后5位
  b.每个字段必须前部分为数字，且当前者只是单纯数字无单位时，后者(除数)后部分不能有字符。   其余结果就按顺序去除空格后拼接起来。(因为此时后缀被当被单位看待了)
  ~~~

  

* ##### `% 号运算符`

  ~~~scss
  // 总结：
  a.值与"%"之间必须要有空格，否则会被看做字符串
  ~~~



#### 2. 关系运算符 `>, <, >=,<=`

#### 3. 相等运算符 `==, !=`

#### 4. 布尔运算符 `and` `or` 以及 `not`

~~~scss
$a: 1>0 and 0>=5; // fasle 注：值与"and"、"or"和"not"之间必须要有空格，否则会被看做字符串
~~~

#### 5. 三目运算符 `if(条件, 结果1，结果2)`

```scss
p {
    color: if(1 + 1 = 2, green, yellow);
}

// 编译后, 转为css后的结果:
p{ color: green;}
```



### 五、控制指令

------

#### 1. `@if 指令`

~~~scss
$age: 19;

p {
    @if $age == 18 {
        color: red;
    } @else if $age == 19 {
        color: blue;
    } @else {
        color: green;
    }
}

// 编译后, 转为css后的结果:
p {
  color: blue;
}
~~~



#### 2. `@for 指令`

~~~scss
@for $i from 1 through 3 { //案例1 through包含end值  值为1,2,3
  .item-#{$i} { width: 2em * $i; }
}

@for $i from 1 to 3 {     //案例2 to不包含end值  值为1,2
  .item-#{$i} { width: 2em * $i; }
}

// 案例1 转为css后的结果:
.item-1 {
  width: 2em; }
.item-2 {
  width: 4em; }
.item-3 {
  width: 6em; }
~~~



#### 3. `@each 指令`

```scss
$list: puma, sea-slug, egret, salamander;

@each $animal in list { 
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
  }
}

// 编译后, 转为css后的结果:
.puma-icon {
  background-image: url('/images/puma.png'); }
.sea-slug-icon {
  background-image: url('/images/sea-slug.png'); }
.egret-icon {
  background-image: url('/images/egret.png'); }
.salamander-icon {
  background-image: url('/images/salamander.png'); }
```



#### 4. `@while 指令`

~~~scss
$i: 6;
@while $i > 0 {  //while用的很少
  .item-#{$i} { width: 2em * $i; }
  $i: $i - 2;
}

// 编译后, 转为css后的结果:
.item-6 {
  width: 12em; }
.item-4 {
  width: 8em; }
.item-2 {
  width: 4em; }
~~~





------

### 六、混合样式指令 @mixin

#### 1. 正常使用 @mixin @include

~~~scss
// 例1
@mixin large-text {    //定义混合指令
  font: {
    family: Arial;
    size: 20px;
    weight: bold;
  }
  color: #ff0000;
}

p {
    @include large-text; //引用
}

// 编译后, 转为css后的结果:
p {
  font-family: Arial;
  font-size: 20px;
  font-weight: bold;
  color: #ff0000;
}
~~~



#### 2. 有参数的 @mixin

~~~scss
@mixin mp($width) {
    margin: $width;
}

body {
    @include mp($width: 300px);
}
~~~



#### 3. 设置参数默认值

~~~scss
@mixin mp($width: 500px) {
    margin: $width;
}

body {
    @include mp(300px);
}
~~~



#### 4.  混合样式扩展

~~~scss
@mixin example {
   color: #ff0000;
}
@include example{ //扩展
    background-color: red;
}

p {
    @include example; 
}

// 编译后, 转为css后的结果:
p {
    color: #ff0000;
    background-color: red;
}
~~~





------

### 七、函数指令 @function

~~~scss
// example:
@function fn-name($params...) {
    @return nth($params, 1);
}
p {
    height: fn-name(1px);
}

// 编译后, 转为css后的结果:
p {
  height: 1px;
}
~~~





------

### 八、其他指令

#### 1. `@import 引入其他scss文件`

~~~scss
@import "foo.scss"; 
@import "foo";   // 以上两种方式均可， 注，只能引入scss文件，其他的引入不行，css文件也不行
@import "rounded-corners", "text-shadow"; //同时引入多个文件

// 以下方式均不可行
@import "foo.css";
@import "foo" screen;
@import "http://foo.com/bar";
@import url(foo);
~~~



#### 2. `@extend 继承属性`

~~~scss
.img {
    color: red;
}
.path{
    @extend .img;
}

// 编译后, 转为css后的结果:
.img .path {
  color: red;
}
~~~



#### 3. `@warn 命令行输出警告信息`

#### 4. `@error 命令行输出错误信息`





------

### 九、其他语法

#### 1. `插值语法`

~~~scss
$name: foo;
$attr: border;
p.#{$name} {   //通过 `#{}` 插值语句可以在选择器、属性名和属性值中使用变量
  #{$attr}-color: $name;
}

// 编译后：
p.foo {
  border-color: foo;
}
~~~



#### 2. `& 父选择器`

~~~scss
a {
    color: yellow;
    &:hover{
        color: green;
    }
    &:active{
        color: blank;
    }
}
~~~



#### 3. `!default` 设置默认值

~~~scss
$content: "First content";
$content: "Second content?" !default;  //变量是 null 空值时将视为未被 `!default` 赋值。
$new_content: "First time reference" !default; 

#main {
  content: $content;
  new-content: $new_content;
}

// 编译后, 转为css后的结果:
#main {
  content: "First content";
  new-content: "First time reference"; }
~~~





## 四、知识点总结



> 1. **什么是Css预处理器**
>
> 2. **Sass/Scss、Less、Stylus的区别**
>
> 3. **嵌套写法**
>
> 4. **变量的定义与使用**
>
> 5. **7种常见的数据类型**
>    **数字、字符串、布尔型、颜色、数组、map、空值**
>
> 6. 运算符
>    布尔运算符、三目运算符
>
> 7. 4种控制指令
>
>    @if  @for  @each  @while
>
> 8. **@mixin 混合指令的定义与使用**
>
> 9. 2种其他指令 
>
>    @import  @extend
>
> 10. 其他语法 
>
>     插值语法 、父选择器





## 五、学习参考

* 视频：<https://www.bilibili.com/video/BV1Zg4y1v75U?p=30>

* 官方文档：<https://www.sass.hk/docs/>



