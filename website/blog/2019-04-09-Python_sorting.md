---
title: Python排序算法
author: woobamboo
authorURL:
authorFBID:
---


python实现常见排序算法

<!--truncate-->

### 冒泡排序

```
'''冒泡排序，我的第一版'''
def bubble_sort(arr):
    n = len(arr)
    while n >= 1:
        i, j = 0, 1
        while j <= n -1:
            if arr[i] > arr[j]:
                arr[i], arr[j] = arr[j], arr[i]
            
            i += 1
            j += 1
        n -= 1
    return arr


'''冒泡排序，从文章来'''
def bubble_sort2(lists):
    
    count = len(lists)
    for i in range(0, count):
        for j in range(i + 1, count):
            if lists[i] > lists[j]:
                lists[i], lists[j] = lists[j], lists[i]
    return lists


'''冒泡排序，我的第二版:while 改成for'''
def bubble_sort3(lists):
    count = len(lists)
    for i in range(count, 0, -1):
        for j in range(count-1): # count-1是因为它是慢指针，与它右边的值进行比较
            if lists[j] > lists[j+1]:
                lists[j], lists[j+1] = lists[j+1], lists[j]
    return lists
```

### 插入排序

```
'''插入排序, 我的第一版'''
def insert_sort(lists):
    if len(lists) <= 1:
        return lists

    out = [lists[0]]
    for i in lists[1:]:
        out.append(i)

        for i in range(len(out)-1, 0, -1):
            if out[i-1] > out[i]:
                out[i-1], out[i] = out[i], out[i-1]
            else:
                break
    return lists


'''插入排序，文章来'''
def insert_sort2(lists):
    # 插入排序
    count = len(lists)
    for i in range(1, count):
        key = lists[i]
        j = i - 1
        while j >= 0:
            if lists[j] > key:
                lists[j + 1] = lists[j]
                lists[j] = key
            j -= 1
    return lists

'''插入排序，我的第二版：原址排序'''
def insert_sort3(lists):
    count = len(lists)
    for i in range(1, count):
        for j in range(i, 0, -1):
            if lists[j-1] > lists[j]:
                lists[j-1], lists[j] = lists[j], lists[j-1]
            else:
                break

    return lists
```

### 选择排序

每次从未排序部分选择最小值，将其放到已排序的末尾

```
'''select sort'''
'''我的需要多比n次。可以在前面添加：if len(count) <= 1: return lists'''
def select_sort1(lists):
    count = len(lists)
    
    for i in range(count):
        min_n = i
        for j in range(i, count):
            if lists[j] < lists[min_n]:
                min_n = j
        lists[min_n], lists[i] = lists[i], lists[min_n]
            
    return lists


'''from 文章'''
def select_sort2(lists):
    # 选择排序
    count = len(lists)
    for i in range(0, count):
        min = i
        for j in range(i + 1, count):
            if lists[min] > lists[j]:
                min = j
        lists[min], lists[i] = lists[i], lists[min]
    return lists
```



### 归并排序

对于递归算法，主要是写出递归公式及终止条件。

```
递归公式：
merge_sort(p..r) = merge(merge_sort(p..q), merge_sort(q+1...r))

终止条件：
p >= r 不用再继续分解
```



```
'''归并排序, 我的第一版'''
def merge_sort(lists):
    def merge(list1, list2):
        i, j = 0, 0
        out = []
        while i < len(list1) and j < len(list2):
            if list1[i] >= list2[j]:
                out.append(list2[j])
                j += 1
            else:
                out.append(list1[i])
                i += 1
        if i == len(list1):
            out.extend(list2[j:])
        else:
            out.extend(list1[i:])
        return out

    if len(lists) == 1:
        return lists
    else:
        return merge(merge_sort(lists[:len(lists)//2]), merge_sort(lists[len(lists)//2:]))

    return lists
```

> 感觉我的merge函数不太好。要修改一下。

```
# 高级版merge函数！
def merge_sort(arr):
    def merge(left, right):
        result = []
        while left and right:
            result.append((left if left[0] < right[0] else right).pop(0))
        result = result + left + right
        return result

    if len(arr) <= 1:
        return arr
    mid = len(arr)//2
    return merge(merge_sort(arr[:mid]), merge_sort(arr[mid:]))
```




### 快速排序

> 这个挖坑填数+分治法甚好。https://blog.csdn.net/MoreWindows/article/details/6684558


```
# 基础版
def quick_sort(arr):
    arr_len = len(arr)
    
    if arr_len <= 1:
        return arr
    else:
        less, greater = [], []
        pivot = arr.pop()
        for element in arr:
            if element < pivot:
                less.append(element)
            else:
                greater.append(element)
        return quick_sort(less) + [pivot] + quick_sort(greater)
```

```
# 挖坑分治法

```




### 堆排序









文章算法参考：[八大排序算法的 Python 实现](http://python.jobbole.com/82270/)
