#### 缓存的本质

根据wikipedia的定义， cache是一个计算机硬件(或者软件)组件。该组件会保存数据，从而让后继对保存数据的访问变得更快。缓存保存的数据可以计算的结果，也可以来自其它数据存储的拷贝。当缓存的数据被访问到时，称为**缓存命中**，否则称为**缓存未命中**。 

缓存能够起作用的原因是计算机程序访问数据的时空局部性原理。时间局部性是指，短时间内，重复访问相同的数据/资源。空间局部性是指，访问存储在相对较近位置的数据。

web背景下，更常见的说法是“热点数据”。比如热卖的商品，热点微博等。

