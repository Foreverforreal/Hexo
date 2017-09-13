title: 查找（Search）
id: 1505278091369
author: 不识
tags:
  - 算法
categories:
  - 算法
date: 2017-09-13 12:48:00
---
***
# 二分查找（Binary Search）
***
二分查找,也称折半查找、二分搜索，是一种在**有序数组**中查找某一特定元素的搜索算法。

二分查找在搜索过程从数组的中间元素开始，如果中间元素正好是要查找的元素，则搜素过程结束；如果某一特定元素大于或者小于中间元素，则在数组大于或小于中间元素的那一半中查找，而且跟开始一样从中间元素开始比较。如果在某一步骤数组已经为空，则表示找不到指定的元素。这种搜索算法每一次比较都使搜索范围缩小一半，其时间复杂度是O(logN)。
<!-- more -->
对于一个有N个项的数组a，在a[i...j] (j=N-1)中查找元素d，其步骤如下：
1. 计算数组中点mid = i + (j - i)/2
2. 比较a[mid]与d，如果a[mid]等于d则返回mid，大于查找mid左边元素，小于查找mid右边元素

java循环实现代码为：
```java
    public int binarySearch(int[] a, int d) {
        int low = 0; 
        int high = a.length - 1;

        while (low <= high) {
            int mid = low + (high - low ) / 2;
            if (a[mid] > d) {
                high = mid - 1;
            } else if (a[mid] < d) {
                low = mid + 1;
            } else {
                return mid;
            }
        }
        return -1;
    }
```
java递归实现代码为：
```java
    public int binarySearch1(int[] a,int d,int low,int high){
        if(low <= high){
            int mid = low + (high - low ) / 2;  
            if(a[mid] == d)
                return d;
            if(a[mid] > d)
                return binarySearch1(a,d,low,mid - 1);
            if(a[mid] < d)
                return binarySearch1(a,d,mid + 1,high);
        //可以使用三元运算符简写为
        //return a[mid] > d ? binarySearch1(a,d,low,mid - 1) : (a[mid] == d ? mid : binarySearch1(a,d,mid + 1,high));
        }
        return -1;
    }
```