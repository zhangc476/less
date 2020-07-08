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

