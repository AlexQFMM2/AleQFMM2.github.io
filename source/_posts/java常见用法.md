---
title: java常见用法
categories: java
date: 2022-05-31 09:22:21
tags:	理论
---

# java常见

#### 1、随机数

```
import java.util.Random;

Random r = new Random();

int num = r.nextInt(10);   //0-10 不包括10
```

<!--more-->

#### 2、数组

###### 创建数组

```
int[] nums = new int[100];

String[] names = new String[]{"alex","qm","tom"};

Object[] object = {"alex","qm"};
```

#### 3、输出（print，printf，println）

```
1、print：将信息显示在命令窗口中，输出光标定位在最后一个字符之后。

2、printf：将信息进行格式化显示在命令窗口中，输出光标定位在最后一个字符之后。

3、println：将信息显示在命令窗口中，输出光标换行定位在下一行开头。
```

#### 4、包装类数据类型

```
byte            Byte
short           Short
int             Integer     【特殊】
long            Long
float           Float
double          Double
char            Character   【特殊】
boolean         Boolean
```

#### 5、for-each

###### 定义

```
for-each循环是通过应该与数组内元素类型相同的变量进行遍历，直接得到数组内从下标为0的位置至最后一个位置的元素的元素值，便于数组内元素的查找
不用返回元素对应的数组下标时，for-each循环是一个不错的选择。
```

###### 格式

```
for ( 变量类型  变量名 : 数组名 ) {

    需要执行的循环语句;

 }
 
 其中变量类型为与数组内元素类型相同的类型。
```

###### 对比

```
int[] a =new int[10];

//for 循环
for(int i=0;i < 10;i++) a[i];

//for-each
for(int i : a);
```

#### 6、输入Scanner

```
import java.util.Scanner

Scanner scan(自定义) = new Scanner(System.in); 

int a=scan.nextInt();

string a=scan.next();

scanner语句中没有char字符型的方法所以不能直接输入字符
```

