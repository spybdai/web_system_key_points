#### Radix Sort

**致谢FIRST**

本文重度参考[拜托，面试别再问我基数排序了][btmsb]，**向作者致谢**！

**原理**

* 非比较排序。

* 维基的定义有点模糊。简单来说，排序元素可以转化为

  $$C = a_{k-1} \times R^{k-1} + a_{k-2} \times R^{k-2} + ... + a_1\times R^1 + a_0 \times R^0$$的形式，

  然后按照$$a_{k-1}, a_{k-2}, ..., a_1, a_0$$的顺序(或者相反)，进行排序。

* $$R$$(基)：将数字按进制分隔。如，1024，
  * 按10进制分隔，R=10, $$a_3, a_2, a_1, a_0$$为$$1，0，2，4​$$
  * 按16进制分隔，R=16, $$a_2, a_1, a_0$$值为$$4，0，0$$

* $$k$$(循环轮数)。数字分隔之后的位数。

* buckets(桶): 分隔后，$$a_{k-1}, a_{k-2}, ..., a_1, a_0$$的取值范围。等于$$R$$

**步骤**

```
FOR k轮循环 {
    step1. 遍历排序数组，分隔每个元素，根据分隔后对应位数的数字，将元素放入对应的桶
    stpe2. 遍历桶，将元素放回原数组
}

```

以下图片来自微信文章[拜托，面试别再问我基数排序了][btmsb]

1. 待排序数组

   按10进制分隔，可知循环轮数为2(每个数字分隔后，均为2位)，需要10个桶

   ![alt](../images/radix_sort/1.png)

2. 第一轮循环第一步，按照分隔后的“个位”，将元素放入对应的桶

   ![alt](../images/radix_sort/2-1.png)

   操作完之后，桶内数据如图

   ![alt](../images/radix_sort/2-2.png)

3. 第一轮循环第二步，将桶内数据放回原数组。(注意，**FIFO可以保证排序是稳定的**)

   ![alt](../images/radix_sort/2-3.png)

   操作完成之后，数组整体按照“个位”排序

4. 第二轮循环第一步，按照分隔后的“十位”，将元素放入对应的桶

   ![alt](../images/radix_sort/3-1.png)

   操作完之后，桶内数据如图

   ![alt](../images/radix_sort/3-2.png)

5. 第一轮循环第二步，将桶内数据放回原数组。

   ![alt](../images/radix_sort/3-3.png)

   操作完成之后，数组整体按照“十位”排序。排序完成。


**实现**

radix = 16 的 python 实现

```python
"""
describe the details of the approach, 
so, try not to use the lib or built-in functions
"""


def find_max(l):
    mx = 0
    for x in l:
        if mx < x:
            mx = x
    return mx


def get_loop_number(num):
    """
    how many loops need.
    """
    c = 0
    while num:
        c += 1
        num >>= 4
    if not c:
        return 1
    return c


def radix_sort(l):
    max_number = find_max(l)
    loop_number = get_loop_number(max_number)

    for x in range(loop_number):
        buckets = [[] for _ in range(16)]  # need 16 buckets.
        for num in l:
            buckets[(num >> 4 * x) & 15].append(num)
        del l[:]  # reuse original list
        for bucket in buckets:
            l.extend(bucket)
```

**效率**

* 时间复杂度是$$O(kn)$$, $$k$$是循环的轮数，$$k$$是排序元素的个数。
* $$k = \log_B(N)$$, $$B$$是基, $$N$$可以简单理解为待排序元素的最大值。 
* 每一轮循环，分隔每个待排序元素的操作为常数$$O(1)$$，整轮循环是$$O(n)$$。
* 因此，排序的平均时间$$T \approx log_B(N) \times n$$
* $$\log_B(N)$$与$$n$$无关
* 当$$N$$非常大，$$B$$相对较小，比如$$2$$, 会导致$$\log_B(N) > \log(n)$$，从而导致基数排序的性能低于快速排序等算法。

**Reference:**

* [基数排序][jspx]
* [拜托，面试别再问我基数排序了][btmsb]
* [Radix sort][radix_sort]




[jspx]: https://zh.wikipedia.org/wiki/%E5%9F%BA%E6%95%B0%E6%8E%92%E5%BA%8F
[btmsb]: https://mp.weixin.qq.com/s?__biz=MjM5ODYxMDA5OQ==&mid=2651961634&idx=1&sn=1e9617d6f6bd2790eabedca22ea49879&chksm=bd2d0cfe8a5a85e8dd52dd0453abe7118932f3dd0068682c6829c37d69e197acfe0efff051e7&scene=21#wechat_redirect
[radix_sort]: https://en.wikipedia.org/wiki/Radix_sort
