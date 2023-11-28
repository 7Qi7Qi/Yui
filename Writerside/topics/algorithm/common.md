# 常见算法

## 

LFU(Least Frequently Used) 最不经常使用
LRU(Least Recently Used) 最近最少使用

## 文章
> 1. [Know Thy Complexities](https://www.bigocheatsheet.com/)

## 排序



## KMP 

### 文章

> 1. [如何更好地理解和掌握KMP算法](https://www.zhihu.com/question/21923021)



## 一些的数据结构


### [循环队列](https://blog.csdn.net/lilililililiki/article/details/104317286)

#### 判断队列空和满的3种方式 

##### 一、少用一个存储位
![img.png](cycle-queue1.png)

###### Ⅰ. 当没有下标，入链表相连

1. 队满 ``rear + 1 == front``
2. 队空 ``rear == front``

![img.png](cycle-queue2.png)

###### Ⅱ.当有下标标识

1. 队满 ``(rear + 1 ) % maxsize == front``
2. 队空 ``rear == front``

##### 二、设置一个标识位

1. 设置初始标记 ``boolean flag = false``;
2. 当入队时，让rear向后移动，flag = true;
3. 当出队时，让front向后移动，flag = false;（相反亦可，保持同向移动即可）
4. 判断
   1. 队满 ``rear == front && flag == true``
   2. 队空 ``rear == front && flag == false``

##### 三、计数count,队列中有效元素的个数

1. 队满 ``count == maxsize``
2. 队空 ``count == 0``
