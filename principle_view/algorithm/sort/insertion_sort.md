#### insertion sort

**原理**

* 比较排序
* 按照某一顺序（从前到后或者相反）遍历待排序数组，进行排序
* 特定顺序下的第一个元素（例如，按照从后向前的顺序遍历，则最后一个数据为第一个数据），视为已排序数据
* 将未排序数据逐个插入已排序数据。

**步骤**

```
Insertion Sort 和打撲克牌時，從牌桌上逐一拿起撲克牌，在手上排序的過程相同。
舉例：
Input: {5 2 4 6 1 3}。

首先拿起第一張牌, 手上有 {5}。
拿起第二張牌 2, 把 2 insert 到手上的牌 {5}, 得到 {2 5}。
拿起第三張牌 4, 把 4 insert 到手上的牌 {2 5}, 得到 {2 4 5}。

以此類推。
```

**实现**

```python
def insertion_sort(l):
    for i in range(1, len(l)):
        for j in range(i, 0, -1):
            if l[j-1] > l[j]:
                l[j-1], l[j] = l[j], l[j-1]
            else:
                break
```

**稳定性**

稳定排序

**效率**

**Reference**

* [insertion sort][is]

* [插入排序][crpx]



[is]: https://en.wikipedia.org/wiki/Insertion_sort

[crpx]: https://zh.wikipedia.org/wiki/%E6%8F%92%E5%85%A5%E6%8E%92%E5%BA%8F

