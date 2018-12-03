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
                
                
def insertion_sort_less(l):
    for i in range(1, len(l)):
        temp = l[i]
        for j in range(i, 0, -1):
            if l[j-1] > l[j]:
                l[j-1] = l[j]
            else:
                l[j] = temp
                break
```

**稳定性**

稳定排序

**效率**

* 最佳情况下（数组已按要求顺序排序），比较操作次数为$$n-1$$，赋值操作次数为$$0$$.
* 最坏情况下（数组已按要求逆序排序），比较操作次数为$$n(n-1)/2$$ , 赋值操作次数依据实现方式不同而不同。函数insertion_sort的赋值操作为$$n(n-1)$$(每一次比较操作，对应两次赋值操作)，函数insertion_sort_less的赋值操作次数为$$(n+5)n/2$$ ($$n-1$$轮循环中，每轮赋值操作的次数为$$n+2$$)
* 平均而言，复杂度为$$O(n^2)$$

**使用场景**

* 不适合$$n$$太大的场景
* 需要排序的数据量很小，例如，量级小于千；或者若已知輸入元素大致上按照順序排列。插入排序在工业级库中也有着广泛的应用，在STL的sort算法和stdlib的qsort算法中，都将插入排序作为快速排序的补充，用于少量元素的排序（通常为8个或以下）。

**Reference**

* [insertion sort][is]

* [插入排序][crpx]



[is]: https://en.wikipedia.org/wiki/Insertion_sort

[crpx]: https://zh.wikipedia.org/wiki/%E6%8F%92%E5%85%A5%E6%8E%92%E5%BA%8F

