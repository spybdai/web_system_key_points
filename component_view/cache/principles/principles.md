### Principles of Cache System 

* [缓存的本质](definition.md)
* [缓存的副作用](side_effects.md)
* [缓存替换策略](replacement_policies.md)
* [缓存更新模式](update_patterns.md)
* 分布式缓存[TBF]

### Reference

- [wikipedia cache][wc]
- [计算计程序局部性原理][jsjjb]
- [程序的状态][cxzt]
- [Service statelessness principle][ssp]
- [可扩展的有状态服务][kkzdy]
- [Cache replacement policies][crp]
- [缓存更新的套路][hcgxt]
- [Why does Facebook use delete to remove the key-value pair in Memcached instead of updating the Memcached during write request to the backend?][wdfbu]



[wc]: https://en.wikipedia.org/wiki/Cache_(computing)

[jsjjb]: https://en.wikipedia.org/wiki/Locality_of_reference

[cxzt]:  https://en.wikipedia.org/wiki/State_(computer_science)

[ssp]: https://en.wikipedia.org/wiki/Service_statelessness_principle

[kkzdy]: https://zhuanlan.zhihu.com/p/25874641

[crp]: https://en.wikipedia.org/wiki/Cache_replacement_policies

[hcgxt]: https://coolshell.cn/articles/17416.html

[wdfbu]: https://www.quora.com/Why-does-Facebook-use-delete-to-remove-the-key-value-pair-in-Memcached-instead-of-updating-the-Memcached-during-write-request-to-the-backend