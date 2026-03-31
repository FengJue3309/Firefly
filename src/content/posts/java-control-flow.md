---
title: JAVA常用结构语句
description: 本文详细介绍了Java编程中的核心控制流语句。内容涵盖三大循环结构（for、while、do...while）的语法与执行逻辑，深入解析条件判断语句（if...else、switch case）的使用规则与嵌套技巧，并补充了增强for循环及break、continue等跳转控制语句。通过本文，你将全面掌握如何构建逻辑严密、高效复用的Java代码。
published: 2026-04-01
tags: [Java, 循环结构, 条件语句, Switch Case, 编程基础]
category: Java
lang: zh-CN
image: /images/java-control-flow/java.png
---

![Java控制流](/images/java-control-flow/java.png)

## Java 循环结构 - for, while 及 do...while

### 顺序结构的程序语句只能被执行一次。

### 如果您想要同样的操作执行多次，就需要使用循环结构。

## Java中有三种主要的循环结构：

- while 循环
- do…while 循环
- for 循环

### while 循环

```java
while( 布尔表达式 ) {
  //循环内容
}
```

### do…while 循环

```java
do {
  //代码语句
}while(布尔表达式);
```

### for循环

```java
for(初始化; 布尔表达式; 更新) {
  //代码语句
}

//增强for语句
for(声明语句 : 表达式)
{
  //代码句子
}
```

### 关于 for 循环有以下几点说明：

- 最先执行初始化步骤。可以声明一种类型，但可初始化一个或多个循环控制变量，也可以是空语句。
- 然后，检测布尔表达式的值。如果为 true，循环体被执行。如果为false，循环终止，开始执行循环体后面的语句。
- 执行一次循环后，更新循环控制变量。
- 再次检测布尔表达式。循环执行上面的过程。

## Java 条件语句 - if...else

### 基本 if 语句

```java
if(布尔表达式)
{
  //如果布尔表达式为true将执行的语句
}
```

### if...else 语句

```java
if(布尔表达式){
  //如果布尔表达式的值为true
}else{
  //如果布尔表达式的值为false
}
```

### if...else if...else 语句

```java
if(布尔表达式 1){
  //如果布尔表达式 1的值为true执行代码
}else if(布尔表达式 2){
  //如果布尔表达式 2的值为true执行代码
}else if(布尔表达式 3){
  //如果布尔表达式 3的值为true执行代码
}else {
  //如果以上布尔表达式都不为true执行代码
}
```

### 嵌套的 if…else 语句

```java
if(布尔表达式 1){
  //如果布尔表达式 1的值为true执行代码
  if(布尔表达式 2){
    //如果布尔表达式 2的值为true执行代码
  }
}
```

## Java switch case 语句

```java
switch(expression){
  case value :
    //语句
    break; //可选
  case value :
    //语句
    break; //可选
  //你可以有任意数量的case语句
  default : //可选
    //语句
}
```

### switch case 语句有如下规则：

- **支持的数据类型**：switch 语句中的变量类型可以是 byte、short、int 或者 char。从 Java SE 7 开始，switch 支持字符串 String 类型了，同时 case 标签必须为字符串常量或字面量。

- **多个 case 语句**：switch 语句可以拥有多个 case 语句。每个 case 后面跟一个要比较的值和冒号。

- **数据类型匹配**：case 语句中的值的数据类型必须与变量的数据类型相同，而且只能是常量或者字面常量。

- **执行流程**：当变量的值与 case 语句的值相等时，那么 case 语句之后的语句开始执行，直到 break 语句出现才会跳出 switch 语句。

- **break 语句**：当遇到 break 语句时，switch 语句终止。程序跳转到 switch 语句后面的语句执行。case 语句不必须要包含 break 语句。如果没有 break 语句出现，程序会继续执行下一条 case 语句，直到出现 break 语句。

- **default 分支**：switch 语句可以包含一个 default 分支，该分支一般是 switch 语句的最后一个分支（可以在任何位置，但建议在最后一个）。default 在没有 case 语句的值和变量值相等的时候执行。default 分支不需要 break 语句。

### 实际应用示例

**for 循环示例：**

```java
// 基本 for 循环
for(int i = 0; i < 10; i++) {
  System.out.println("i = " + i);
}

// 增强 for 循环（遍历数组）
int[] numbers = {1, 2, 3, 4, 5};
for(int num : numbers) {
  System.out.println(num);
}
```

**switch case 示例：**

```java
int day = 3;
switch(day) {
  case 1:
    System.out.println("Monday");
    break;
  case 2:
    System.out.println("Tuesday");
    break;
  case 3:
    System.out.println("Wednesday");
    break;
  default:
    System.out.println("Other day");
}
```

**嵌套条件示例：**

```java
int score = 85;
if(score >= 90) {
  System.out.println("Grade: A");
} else if(score >= 80) {
  System.out.println("Grade: B");
} else if(score >= 70) {
  System.out.println("Grade: C");
} else {
  System.out.println("Grade: F");
}
```

## 总结

掌握这些基本的控制流语句是编写高效 Java 代码的基础。通过合理使用循环、条件判断和 switch 语句，你可以构建复杂的业务逻辑。记住：

- 使用 **for 循环** 处理已知次数的迭代
- 使用 **while 循环** 处理条件驱动的迭代
- 使用 **if...else** 处理简单的二元或多元条件
- 使用 **switch case** 处理多个离散值的判断
