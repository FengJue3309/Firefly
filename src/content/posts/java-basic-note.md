---
title: Java基础笔记
published: 2026-03-26
description: Java初学者学习笔记，详细讲解Java基础语法、数据类型、变量类型、修饰符、运算符、循环结构、条件语句、switch case语句、Number和Math类、Character类等核心概念。
tags: [Java, 基础语法, 编程教程, 初学者]
category: Java
categories: [Java, 基础语法, 编程教程, 初学者]
lang: zh-CN
image: /images/java-basic/image-20240228195010033.png
---

> Java初学者学习笔记，详细讲解Java基础语法、数据类型、变量类型、修饰符、运算符、循环结构、条件语句、switch case语句、Number和Math类、Character类等核心概念。

一，Java初学者学习笔记

## 1，Java基础语法

一个 Java 程序可以认为是一系列对象的集合，而这些对象通过调用彼此的方法来协同工作。下面简要介绍下类、对象、方法和实例变量的概念。

- **对象**：对象是类的一个实例，有状态和行为。例如，一条狗是一个对象，它的状态有：颜色、名字、品种；行为有：摇尾巴、叫、吃等。
- **类**：类是一个模板，它描述一类对象的行为和状态。
- **方法**：方法就是行为，一个类可以有很多方法。逻辑运算、数据修改以及所有动作都是在方法中完成的。
- **实例变量**：每个对象都有独特的实例变量，对象的状态由这些实例变量的值决定。

### 1-1 第一个Java程序

下面看一个简单的 Java 程序，它将打印字符串 *Hello World*

```java
public class HelloWorld {
    /* 第一个Java程序
     * 它将打印字符串 Hello World
     */
    public static void main(String []args) {
        System.out.println("Hello World"); // 打印 Hello World
    }
}
```

下面将逐步介绍如何保存、编译以及运行这个程序：

- 打开代码编辑器，把上面的代码添加进去；
- 把文件名保存为：HelloWorld.java；
- 打开 cmd 命令窗口，进入目标文件所在的位置，假设是 C:\
- 在命令行窗口输入 **javac HelloWorld.java** 按下回车键编译代码。如果代码没有错误，cmd 命令提示符会进入下一行（假设环境变量都设置好了）。
- 再键输入 **java HelloWorld** 按下回车键就可以运行程序了

你将会在窗口看到 Hello World

Terminal window
```
C : > javac HelloWorld.java
C : > java HelloWorld
Hello World
```

Gif 图演示：

![java-HelloWorld.gif](/images/java-basic/java-HelloWorld.gif)

### 1-2 基本语法

编写 Java 程序时，应注意以下几点：

- **大小写敏感**：Java 是大小写敏感的，这就意味着标识符 Hello 与 hello 是不同的。
- **类名**：对于所有的类来说，类名的首字母应该大写。如果类名由若干单词组成，那么每个单词的首字母应该大写，例如 **MyFirstJavaClass** 。
- **方法名**：所有的方法名都应该以小写字母开头。如果方法名含有若干单词，则后面的每个单词首字母大写。
- **源文件名**：源文件名必须和类名相同。当保存文件的时候，你应该使用类名作为文件名保存（切记 Java 是大小写敏感的），文件名的后缀为 **.java**。（如果文件名和类名不相同则会导致编译错误）。
- **主方法入口**：所有的 Java 程序由 **public static void main(String []args)** 方法开始执行。

![image-20240228200611781.png](/images/java-basic/image-20240228200611781.png)

### 1-3 Java 标识符

Java 所有的组成部分都需要名字。类名、变量名以及方法名都被称为标识符。

关于 Java 标识符，有以下几点需要注意：

- 所有的标识符都应该以字母（A-Z 或者 a-z）,美元符（$）、或者下划线（_）开始
- 首字符之后可以是字母（A-Z 或者 a-z）,美元符（$）、下划线（_）或数字的任何字符组合
- 关键字不能用作标识符
- 标识符是大小写敏感的
- 合法标识符举例：age、$salary、_value、__1_value
- 非法标识符举例：123abc、-salary

### 1-4 Java修饰符

像其他语言一样，Java可以使用修饰符来修饰类中方法和属性。主要有两类修饰符：

- 访问控制修饰符 : default, public , protected, private
- 非访问控制修饰符 : final, abstract, static, synchronized

在后面的章节中我们会深入讨论 Java 修饰符。

### 1-5 Java 变量

Java 中主要有如下几种类型的变量

- 局部变量
- 类变量（静态变量）
- 成员变量（非静态变量）

![image-20240228200803273.png](/images/java-basic/image-20240228200803273.png)

### 1-6 Java 数组

数组是储存在堆上的对象，可以保存多个同类型变量。在后面的章节中，我们将会学到如何声明、构造以及初始化一个数组。

### 1-7 Java 枚举

Java 5.0引入了枚举，枚举限制变量只能是预先设定好的值。使用枚举可以减少代码中的 bug。

例如，我们为果汁店设计一个程序，它将限制果汁为小杯、中杯、大杯。这就意味着它不允许顾客点除了这三种尺寸外的果汁。

```java
class FreshJuice {
   enum FreshJuiceSize{ SMALL, MEDIUM , LARGE }
   FreshJuiceSize size;
}

public class FreshJuiceTest {
   public static void main(String []args){
      FreshJuice juice = new FreshJuice();
      juice.size = FreshJuice.FreshJuiceSize.MEDIUM  ;
   }
}
```

### 1-8 Java 关键字

![image-20240228195010033.png](/images/java-basic/image-20240228195010033.png)

### 1-9 Java注释

类似于 C/C++、Java 也支持单行以及多行注释。注释中的字符将被 Java 编译器忽略。

```java
public class HelloWorld {
   /* 这是第一个Java程序
    *它将打印Hello World
    * 这是一个多行注释的示例
    */
    public static void main(String []args){
       // 这是单行注释的示例
       /* 这个也是单行注释的示例 */
       System.out.println("Hello World");
    }
}
```

### 1-10 Java 空行

空白行或者有注释的行，Java 编译器都会忽略掉。

### 1-11 继承

在 Java 中，一个类可以由其他类派生。如果你要创建一个类，而且已经存在一个类具有你所需要的属性或方法，那么你可以将新创建的类继承该类。

利用继承的方法，可以重用已存在类的方法和属性，而不用重写这些代码。被继承的类称为超类（super class），派生类称为子类（subclass）。

---

### 1-12 接口

在 Java 中，接口可理解为对象间相互通信的协议。接口在继承中扮演着很重要的角色。

接口只定义派生要用到的方法，但是方法的具体实现完全取决于派生类。

## 2，Java对象和类

### 2-1 Java中的对象

现在让我们深入了解什么是对象。看看周围真实的世界，会发现身边有很多对象，车，狗，人等等。所有这些对象都有自己的状态和行为。

拿一条狗来举例，它的状态有：名字、品种、颜色，行为有：叫、摇尾巴和跑。

对比现实对象和软件对象，它们之间十分相似。

软件对象也有状态和行为。软件对象的状态就是属性，行为通过方法体现。

在软件开发中，方法操作对象内部状态的改变，对象的相互调用也是通过方法来完成。

### 2-2 Java中的类

类可以看成是创建Java对象的模板。

通过下面一个简单的类来理解下Java中类的定义：

```java
public class Dog{
  String breed;
  int age;
  String color;
  void barking(){

  }

  void hungry(){

  }

  void sleeping(){

  }
}
```

一个类可以包含以下类型变量：

- **局部变量**：在方法、构造方法或者语句块中定义的变量被称为局部变量。变量声明和初始化都是在方法中，方法结束后，变量就会自动销毁。
- **成员变量**：成员变量是定义在类中，方法体之外的变量。这种变量在创建对象的时候实例化。成员变量可以被类中方法、构造方法和特定类的语句块访问。
- **类变量**：类变量也声明在类中，方法体之外，但必须声明为static类型。

一个类可以拥有多个方法，在上面的例子中：barking()、hungry()和sleeping()都是Dog类的方法。

---

### 2-3 构造方法

每个类都有构造方法。如果没有显式地为类定义构造方法，Java编译器将会为该类提供一个默认构造方法。

在创建一个对象的时候，至少要调用一个构造方法。构造方法的名称必须与类同名，一个类可以有多个构造方法。

下面是一个构造方法示例：

```java
public class Puppy{
    public Puppy(){

    }

    public Puppy(String name){
        // 这个构造器仅有一个参数：name
    }
}
```

### 2-4 创建对象

对象是根据类创建的。在Java中，使用关键字new来创建一个新的对象。创建对象需要以下三步：

- **声明**：声明一个对象，包括对象名称和对象类型。
- **实例化**：使用关键字new来创建一个对象。
- **初始化**：使用new创建对象时，会调用构造方法初始化对象。

下面是一个创建对象的例子：

```java
public class Puppy{
   public Puppy(String name){
      //这个构造器仅有一个参数：name
      System.out.println("小狗的名字是 : " + name );
   }
   public static void main(String[] args){
      // 下面的语句将创建一个Puppy对象
      Puppy myPuppy = new Puppy( "tommy" );
   }
}
```

编译并运行上面的程序，会打印出下面的结果：

```
小狗的名字是 : tommy
```

---

## 3，Java数据类型

### 3-1 基础数据类型

Java 中的基础数据类型分为以下几类：

#### 整数类型

- **byte**：8位，范围 -128 到 127
- **short**：16位，范围 -32768 到 32767
- **int**：32位，范围 -2147483648 到 2147483647
- **long**：64位，范围 -9223372036854775808 到 9223372036854775807

#### 浮点类型

- **float**：32位浮点数
- **double**：64位浮点数

#### 字符类型

- **char**：16位 Unicode 字符

#### 布尔类型

- **boolean**：true 或 false

![image-20240228213115946.png](/images/java-basic/image-20240228213115946.png)

---

## 4，Java运算符

### 4-1 算术运算符

```
+  加   -  减   *  乘   /  除   %  取模
++  自增   --  自减
```

### 4-2 关系运算符

```
==  等于   !=  不等于   >  大于   <  小于
>=  大于等于   <=  小于等于
```

### 4-3 逻辑运算符

```
&&  逻辑与   ||  逻辑或   !  逻辑非
```

---

## 5，Java循环

### 5-1 for循环

```java
for(int i = 0; i < 10; i++) {
    System.out.println(i);
}
```

### 5-2 while循环

```java
while(condition) {
    // 循环体
}
```

### 5-3 do-while循环

```java
do {
    // 循环体
} while(condition);
```

---

## 6，Java条件语句

### 6-1 if语句

```java
if(条件) {
    // 条件为true时执行
}
```

### 6-2 if-else语句

```java
if(条件) {
    // 条件为true时执行
} else {
    // 条件为false时执行
}
```

### 6-3 switch case语句

```java
switch(变量) {
    case 值1:
        // 执行代码
        break;
    case 值2:
        // 执行代码
        break;
    default:
        // 默认执行代码
}
```

![image-20240228213253912.png](/images/java-basic/image-20240228213253912.png)

---

## 7，Java Number和Math类

### 7-1 Number类

Java 为每种基本数据类型都提供了对应的包装类，如 Integer、Double、Float 等。

### 7-2 Math类

Math 类提供了常用的数学运算方法：

```java
Math.abs(-5);      // 绝对值
Math.max(5, 10);   // 最大值
Math.min(5, 10);   // 最小值
Math.sqrt(16);     // 平方根
Math.pow(2, 3);    // 幂运算
Math.random();     // 随机数
```

---

## 8，Java Character类

Character 类用于处理单个字符：

```java
Character ch = 'A';
Character.isDigit(ch);       // 是否为数字
Character.isLetter(ch);      // 是否为字母
Character.isUpperCase(ch);   // 是否为大写
Character.isLowerCase(ch);   // 是否为小写
Character.toUpperCase(ch);   // 转换为大写
Character.toLowerCase(ch);   // 转换为小写
```

---

*本文为 Java 基础学习笔记，详细讲解了 Java 基础语法、面向对象等核心概念，适合 Java 初学者入门学习。*
