---
title: java ArrayList与HashMap
categories: java
date: 2022-05-31 09:42:00
tags:	理论
---

# ArrayList

#### 一、概念

```
ArrayList 类是一个可以动态修改的数组，与普通数组的区别就是它是没有固定大小的限制，我们可以添加或删除元素。

ArrayList 继承了 AbstractList ，并实现了 List 接口。
```

<!--more-->

#### 二、使用

###### 导入

```
import java.util.ArrayList;
```

###### 初始化

```
ArrayList<E> list =new ArrayList<>();　
```

```
E只能为引用数据类型。

​	Byte
​	Short
​	Integer     【特殊】
​	Long
​	Float
​	Double
​	Character   【特殊】
​	Boolean
```



#### 三、常用方法

| 方法                                                         | 描述                                          |
| :----------------------------------------------------------- | :-------------------------------------------- |
| add()                                                        | 将元素插入到指定位置的 arraylist 中           |
| addAll()                                                     | 添加集合中的所有元素到 arraylist 中           |
| clear()                                                      | 删除 arraylist 中的所有元素                   |
| clone()                                                      | 复制一份 arraylist                            |
| contains()                                                   | 判断元素是否在 arraylist                      |
| get()                                                        | 通过索引值获取 arraylist 中的元素             |
| indexOf()                                                    | 返回 arraylist 中元素的索引值                 |
| [removeAll()](https://www.runoob.com/java/java-arraylist-removeall.html) | 删除存在于指定集合中的 arraylist 里的所有元素 |
| remove()                                                     | 删除 arraylist 里的单个元素                   |
| size()                                                       | 返回 arraylist 里元素数量                     |
| isEmpty()                                                    | 判断 arraylist 是否为空                       |
| [subList()](https://www.runoob.com/java/java-arraylist-sublist.html) | 截取部分 arraylist 的元素                     |
| set()                                                        | 替换 arraylist 中指定索引的元素               |
| sort()                                                       | 对 arraylist 元素进行排序                     |
| toArray()                                                    | 将 arraylist 转换为数组                       |
| toString()                                                   | 将 arraylist 转换为字符串                     |
| [ensureCapacity](https://www.runoob.com/java/java-arraylist-surecapacity.html)() | 设置指定容量大小的 arraylist                  |
| lastIndexOf()                                                | 返回指定元素在 arraylist 中最后一次出现的位置 |
| [retainAll()](https://www.runoob.com/java/java-arraylist-retainall.html) | 保留 arraylist 中在指定集合中也存在的那些元素 |
| [containsAll()](https://www.runoob.com/java/java-arraylist-containsall.html) | 查看 arraylist 是否包含指定集合中的所有元素   |
| [trimToSize()](https://www.runoob.com/java/java-arraylist-trimtosize.html) | 将 arraylist 中的容量调整为数组中的元素个数   |
| [removeRange()](https://www.runoob.com/java/java-arraylist-removerange.html) | 删除 arraylist 中指定索引之间存在的元素       |
| [replaceAll()](https://www.runoob.com/java/java-arraylist-replaceall.html) | 将给定的操作内容替换掉数组中每一个元素        |
| removeif()                                                   | 删除所有满足特定条件的 arraylist 元素         |
| [forEach()](https://www.runoob.com/java/java-arraylist-foreach.html) | 遍历 arraylist 中每一个元素并执行特定操作     |



#### 四、排序

###### 导入Collections类

```
import java.util.Collections;
```

###### 使用

```
Collections.sort(list);
```



#### 五、遍历

###### 1、for-each遍历

```
for(int i : list)
System.out.println( i ); 
```

###### 2、迭代器遍历

```
import java.util.Iterator;

Iterator<Integer> i = list.iterator(); //定义迭代器

while(i.hasNext)

{	
 	System.out.println( i.next() );
}

PS：i.next()的值的类型为Object！
```



# HashMap

#### 一、概念

```
HashMap 是一个散列表，它存储的内容是键值对(key-value)映射。

HashMap 实现了 Map 接口，根据键的 HashCode 值存储数据，具有很快的访问速度，最多允许一条记录的键为 null，不支持线程同步。

HashMap 是无序的，即不会记录插入的顺序。

HashMap 继承于AbstractMap，实现了 Map、Cloneable、java.io.Serializable 接口。

HashMap 的 key 与 value 类型可以相同也可以不同，可以是字符串（String）类型的 key 和 value，也可以是整型（Integer）的 key 和字符串（String）类型的 value。

HashMap 中的元素实际上是对象，一些常见的基本类型可以使用它的包装类。
```



#### 二、使用

###### 导入

```
import java.util.HashMap;
```

###### 创建

```
HashMap<Integer, String> Hashmap = new HashMap<Integer, String>();
```



#### 三、常见方法

| 方法                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| clear()                                                      | 删除 hashMap 中的所有键/值对                                 |
| clone()                                                      | 复制一份 hashMap                                             |
| isEmpty()                                                    | 判断 hashMap 是否为空                                        |
| size()                                                       | 计算 hashMap 中键/值对的数量                                 |
| put()                                                        | 将键/值对添加到 hashMap 中                                   |
| putAll()                                                     | 将所有键/值对添加到 hashMap 中                               |
| [putIfAbsent()](https://www.runoob.com/java/java-hashmap-putifabsent.html) | 如果 hashMap 中不存在指定的键，则将指定的键/值对插入到 hashMap 中。 |
| remove()                                                     | 删除 hashMap 中指定键 key 的映射关系                         |
| containKey()                                                 | 检查 hashMap 中是否存在指定的 key 对应的映射关系。           |
| containsValue()                                              | 检查 hashMap 中是否存在指定的 value 对应的映射关系。         |
| replace()                                                    | 替换 hashMap 中是指定的 key 对应的 value。                   |
| [replaceAll()](https://www.runoob.com/java/java-hashmap-replaceall.html) | 将 hashMap 中的所有映射关系替换成给定的函数所执行的结果。    |
| get()                                                        | 获取指定 key 对应对 value                                    |
| getOrDefault()                                               | 获取指定 key 对应对 value，如果找不到 key ，则返回设置的默认值 |
| forEach()                                                    | 对 hashMap 中的每个映射执行指定的操作。                      |
| [entrySet()](https://www.runoob.com/java/java-hashmap-entryset.html) | 返回 hashMap 中所有映射项的集合集合视图。                    |
| keySet()                                                     | 返回 hashMap 中所有 key 组成的集合视图。                     |
| values()                                                     | 返回 hashMap 中存在的所有 value 值。                         |
| [merge()](https://www.runoob.com/java/java-hashmap-merge.html) | 添加键值对到 hashMap 中                                      |
| [compute()](https://www.runoob.com/java/java-hashmap-compute.html) | 对 hashMap 中指定 key 的值进行重新计算                       |
| [computeIfAbsent()](https://www.runoob.com/java/java-hashmap-computeifabsent.html) | 对 hashMap 中指定 key 的值进行重新计算，如果不存在这个 key，则添加到 hasMap 中 |
| [computeIfPresent()](https://www.runoob.com/java/java-hashmap-computeifpresent.html) | 对 hashMap 中指定 key 的值进行重新计算，前提是该 key 存在于 hashMap 中。 |



#### 四、遍历输出

###### 1、输出所有的key和value

```
for (Integer i : map.keySet()) {	
	            System.out.println( i + hashmap.get(i));
	        }
```

###### 2、仅输出所有的Value值

```
for (String value: Sites.values()) {
    			 System.out.print(value + ", ");
    }
```



# 例题

#### 随机20个数（大小0-9），放到arraylist里面，统计每个数（0-9）各出现几次（用hashmap）

###### 包导入

```
package test;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Random;
import java.util.HashMap;
import java.util.Iterator;
```

###### List存放数据

```
public class TEST {

​	static ArrayList<Integer> saves = new ArrayList<Integer>();

​	public static void data_in() {// 存放数字
​		Random r = new Random();
​		for (int i = 0; i < 20; i++) {
​			int num = r.nextInt(10); // 0~9
​			saves.add(num);
​		}
​		System.out.println(saves);
​	}
```

###### 遍历List存入hashmap

```
	public static void count() {	// 计数
		HashMap<Integer, Integer> map = new HashMap<>();
		Iterator iterator = saves.iterator(); 	// 遍历saves
		while (iterator.hasNext()) { 	// 直到saves最后一个元素
			Integer i = (Integer) iterator.next();	// iterator.next()值为object，需强制转换
			if (map.containsKey(i)) 	// 如果有key则value+1否则value为1
				map.put(i, map.get(i) + 1);
			else
				map.put(i, 1);		
		}		
		 for (Integer i : map.keySet()) {	//输出key和value
	            System.out.println( "数字"+i + "出现的次数为:" + map.get(i));
	        }
	}
```

###### 调用

```
	public static void main(String[] args) {
​		data_in();
​		count();
​	}

}
```

