# less

less 是CSS一种动态语言，属于CSS预处理语言的一种，它使用类似CSS的语法，为CSS赋予了动态语言的特性，如变量、继承、运算、函数等

less包含一套自定义的语法及一个解析器，用户根据这些语法定义自己的样式规则，这些规则最终会通过解析器，编译生成对应的CSS文件

# 变量

## 语法

@后添加变量名称然后与变量值用:链接

```less
@color: #f00;
#header {
    color: @color
}
```

## 作用域

如果对同一个变量定义两次的话，在当前作用域中最后一次定义的将被使用. 在嵌套中，这个变量只能作用于嵌套之内的属性了，<font color=red>嵌套中的变量是没有顺序的</font>

```less
@width: 1px;

.meng {
    @width: 20px;
    .long{
        @width: 9000px;
        width: @width;
        @width: 220px;
    }
    width: @width;
}

#编译后的代码是
.meng {
    width: 20px;
}
.meng .long{
    width: 220px;
}
```

## 字符串插值

变量可以使用像@{name}这样的结构嵌入到字符串中，即字符串拼接

```less
@myurl:"www.xxx.xx";

.logo {
    background-image: url("@{myurl}/images/logog.jpg");
}
```

## 选择器插值

如果在选择器中使用less变量，只需要使用和字符串插件一样的@{selector}即可

```less
@myurl: logo;

.@{myurl}{
    background-image: url("www.xx/images/logog.jpg");
}
```

## media query变量

如果希望在media query中使用less变量，可以直接使用普通的变量方式. ~后面的值是不被编译的

```less
@singleQuery:~"(max-width:768px)";

div{
    background-color: red;
}

@media screen and @singleQuery{
    div{
        background-color: blue;
    }
}
```

# mixins

## 继承类名

在less中，mixins可以将一个定义好的class A轻松的引入到另一个class B中，从而简单实现 class B继承 class A中的所有属性

```less
.w{
    width: 100px;
}
#h{
    height: 200px;
}

.long{
    .w;
    .meng {
        #h
    }
}
```

以上代码编译的结果

```css
.w {
  width: 100px;
}
#h {
  height: 200px;
}
.long {
  width: 100px;
}
.long .meng {
  height: 200px;
}
```

## 带参数的mixin

```less
.w(@width){
    width: @width;
}
#h(@height){
    height: @height;
}

.long{
    .w(100px);
    .meng {
        #h(50%)
    } 
}
```

以上代码编译的结果

```less
.long {
  width: 100px;
}
.long .meng {
  height: 50%;
}
```

## 隐藏属性继承

定义不带参数属性集合，如果想隐藏这个属性集合，不让它暴露到CSS中去，但是能在其他的属性集合中引用

```less
.w(){
    width: 100px;
}
#h(){
    height: 200px;
}

.long{
    .w();
    .meng {
        #h()
    }
}
```

对比继承类名的例子，可以看到.w，#h没有被编译到CSS中

```css
.long {
  width: 100px;
}
.long .meng {
  height: 200px;
}
```

## mixins默认混合值

```less
.w(@width: 100px){
    width: @width;
}
#h(@height: 100%){
    height: @height;
}

.long{
    .w();
    .meng {
        #h(50%)
    } 
}
```

编译后的结果

```less
.long {
  width: 100px;
}
.long .meng {
  height: 50%;
}
```

## 多参数混合

1. 多个参数可以使用分号或者逗号分隔，这里推荐使用<font color=red>分号</font>分隔,因为逗号有两重含义：它既可以表示混合的参数，也可以表示一个参数中一组值得分隔符
2. 使用分号作为参数分隔符意味着可以将逗号分隔的一组值作为一个变量处理
3. 2个参数，每个参数都含有通过逗号分隔的一组值得情况：.name(1,2,3; something, else)
4. 3个参数，每个参数只含一个数字的情况：.name(1,2,3)
5. 使用一个象征性的分号可以创建一个只含一个参数，但参数包含一组值得混合：.name(1,2,3;)

```less
.mixin(@color) {
  color-1: @color;
}
.mixin(@color; @padding: 2) {
  color-2: @color;
  padding-2: @padding;
}
.mixin(@color; @padding; @margin: 2) {
  color-3: @color;
  padding-3: @padding;
  margin: @margin @margin @margin @margin;
}
.some .selector div {
  .mixin(#008000);
}

```

If you used the mixin with one parameter e.g. `.mixin(green);`, then properties of all mixins with exactly one mandatory parameter will be used. 也就是说如果.mixin(@color; @padding: 2) padding没有默认值2，那么只有第一个.mixin(@color)匹配

## @arguments

```less
.transition(@moveStyle: all; @delayTime: 4s; @moveType: ease-in; @moveTime: 2s){
    -webkit-transition: @arguments;
    -moz-transition: @arguments;
    -o-transition: @arguments;
    -ms-transition: @arguments;
    transition: @arguments;
}
div{
    .transition;
}

span{
    .transition(width)
}

h1{
    .transition(height; 8s)
}
```

编译后的代码

```less
div {
  -webkit-transition: all 4s ease-in 2s;
  -moz-transition: all 4s ease-in 2s;
  -o-transition: all 4s ease-in 2s;
  -ms-transition: all 4s ease-in 2s;
  transition: all 4s ease-in 2s;
}
span {
  -webkit-transition: width 4s ease-in 2s;
  -moz-transition: width 4s ease-in 2s;
  -o-transition: width 4s ease-in 2s;
  -ms-transition: width 4s ease-in 2s;
  transition: width 4s ease-in 2s;
}
h1 {
  -webkit-transition: height 8s ease-in 2s;
  -moz-transition: height 8s ease-in 2s;
  -o-transition: height 8s ease-in 2s;
  -ms-transition: height 8s ease-in 2s;
  transition: height 8s ease-in 2s;
}

```

## !important

Use the `!important` keyword after mixin call to mark all properties inherited by it as `!important`

```less
.my(@width: 200px; @height: 300px){
    width: @width;
    height: @height;
}

.meng{
    .my;
}

.long{
    .my(30px);
}

.menglong{
    .my(100px, 100px)
}

.meng2{
    .my !important;
}

.long2{
    .my(30px) !important;
}

.menglong2{
    .my(100px, 100px) !important;
}
```

## 高级动态参数

```less
//接收0-n个参数
.mixin1(...){
    padding: @arguments;
}
//接收0-n个参数
.mixin2(@a: 1; ...){
    margin: @arguments;
}
//接收1-n个参数
.mixin3(@a; ...){
    margin: @arguments;
}
//接收2-n个参数
.mixin4(@a; @b; ...){
    margin: @arguments;
}
.div1{
    .mixin1(20px 30px 40px 50px)
}
.div2{
    .mixin2(20px, 30px 40px 50px)
}
.div3{
    .mixin3(1)
}
.div3{
    .mixin4(1, 2)
}
```

## 模式匹配

```less
.mixin(dark; @color) {
  color: darken(@color, 10%);
}
.mixin(light; @color) {
  color: lighten(@color, 10%);
}
.mixin(@_; @color) {
  display: block;
}

@switch: light;

.class {
  .mixin(@switch; #888);
}
```

Where the color passed to `.mixin` was lightened. If the value of `@switch` was `dark`, the result would be a darker color.

Here's what happened:

- The first mixin definition didn't match because it expected `dark` as the first argument.
- The second mixin definition matched, because it expected `light`.
- The third mixin definition matched because it expected any value.

## 条件匹配

less选择使用<font color=red>guard混合</font>(类似@media的工作方式)进行条件判断，而不是加入if/else声明

```less
.mixin (@a) when (lightness(@a) >= 50%) {
  background-color: black;
}
.mixin (@a) when (lightness(@a) < 50%) {
  background-color: white;
}
.mixin (@a) {
  color: @a;
}
.class1 {
  .mixin(#ddd);
}
.class2 {
  .mixin(#555);
}

```

### 条件运算符

支持的运算符包括：`>`, `>=`, `=`, `=<`, `<`. Additionally, the keyword `true` is the only truthy value

### 逻辑运算符

Use the `and` keyword to combine guards:

```less
.mixin (@a) when (isnumber(@a)) and (@a > 0) { ... }
```

You can emulate the *or* operator by separating guards with a comma `,`. If any of the guards evaluate to true, it's considered a match:

```less
.mixin (@a) when (@a > 10), (@a < -10) { ... }
```

Use the `not` keyword to negate conditions:

```less
.mixin (@b) when not (@b > 0) { ... }
```

## &的使用

The `&` operator represents the parent selectors of a [nested rule](http://lesscss.cn/features/#features-overview-feature-nested-rules) and is most commonly used when applying a modifying class or pseudo-class to an existing selector

```less
a {
  color: blue;
  &:hover {
    color: green;
  }
}
```

编译后的代码

```css
a {
  color: blue;
}
a:hover {
  color: green;
}
```

### 高级使用1

```less
.button {
  &-ok {
    background-image: url("ok.png");
  }
  &-cancel {
    background-image: url("cancel.png");
  }

  &-custom {
    background-image: url("custom.png");
  }
}
```

编译后的代码

```css
.button-ok {
  background-image: url("ok.png");
}
.button-cancel {
  background-image: url("cancel.png");
}
.button-custom {
  background-image: url("custom.png");
}
```

### 高级使用2

```less
.link {
  & + & {
    color: red;
  }

  & & {
    color: green;
  }

  && {
    color: blue;
  }

  &, &ish {
    color: cyan;
  }
}
```

编译后的代码

```css
.link + .link {
  color: red;
}
.link .link {
  color: green;
}
.link.link {
  color: blue;
}
.link, .linkish {
  color: cyan;
}
```

