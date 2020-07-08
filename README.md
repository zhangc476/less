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

