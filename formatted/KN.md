- [Java9/Java10/Java11的新特性](https://www.cnblogs.com/laizhenghua/articles/13211557.html)
    - JDK9 主要新特性一览
        - JDK9模块化系统
            - 本质上讲也就是说，用模块来管理各个package，通过声明某个package 暴露，**模块(module)的概念，其实就是package外再裹一层**，不声明默 认就是隐藏。因此，模块化使得代码组织上更安全，因为它可以指定哪 些部分可以暴露，哪些部分隐藏。
        -  jShell命令
            - 让**Java**可以像脚本语言一样运行，从 控制台启动**jShell**，利用**jShell**在没有创建类的情况下直接声明变量，计算表达式， 执行语句。
        - 可以实现资源的自动关闭，但是要求执行后必须关闭的所有资源必 须在try子句中初始化，否则编译不通过
        - JDK9 String存储结构变更
            - 在以前的版本中，String一直是用char[]存储，但是在Java9中，String 再也不用 char[] 来存储啦，改成了 byte[] 加上编码标记，节约 了一些空间
        -  集合工厂方法：快速创建只读集合
        - Stream API的加强
            - Stream 接口中添加了 **4 个新的方法： takeWhile, dropWhile, ofNullable，还有个 iterate 方法的新重载方法**
        - Javascript引擎升级：Nashorn
    - JDK10 主要新特性一览
        - 局部变量类型推断
        - 集合新增创建不可变集合的方法
    - JDK11 新特性
        - 新增了一系列字符串处理方法
        - **Optional** 也增加了几个非常酷的方法，现在可以很方便的将一个 **Optional** 转换 成一个 **Stream**, 或者当一个空 **Optional** 时给它一个替代的
        - 局部变量类型推断升级
        - HttpClient替换原有的HttpURLConnection
        - 

# Backlinks
## [CV](<CV.md>)
- # [KN](<KN.md>)

## [September 10th, 2021](<September 10th, 2021.md>)
- [KN](<KN.md>)

