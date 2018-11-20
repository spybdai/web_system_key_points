### 更新缓存，淘汰or修改？

问题描述：允许cache miss的场景，更新缓存数据时，更好的做法是淘汰还是修改缓存？

问题分析：

- 淘汰和修改的区别，前者增加一次cache miss
- 简单类型数据，如int, 可直接set数据，成本较低。
- 复杂类型数据，如序列化后的对象(User对象)，需要先反序列化，修改字段，序列化，最后set数据，成本较高。
- 复杂类型数据，如json dump之后的文本数据,html等，需要先Json load或者parse成对应对象，修改字段，json dump或者序列化成文本，最后set数据，成本较高。
- 并发更新(同一缓存)较高的场景，尽量考虑淘汰缓存。并发操作存在竞争条件，容易导致缓存数据不一致。参考[Cache Aside Pattern][cap]

结论：

- 复杂数据类型，一般淘汰缓存。
- 简单数据类型，视情况而定。如果修改缓存的成本高于一次cache miss，则考虑淘汰缓存
- 并发写场景，考虑淘汰缓存。



[cap]: ../principles/update_patterns.md
