### 缓存误用

- 缓存作为数据管道

  问题描述：多个服务约定key和value，通过缓存来传递数据

  缺点：

  - 多个服务因为缓存耦合(相同的key约定，相同的节点路由等)。
  - 服务共享数据的场景下，MQ支持push, 而cache只能pull,  实时性低。
  - MQ天然支持高可用，cache未必。
  - MQ支持数据落地，cache未必支持。

  解决方案：使用MQ

- 多个服务有读写同一份数据的需求(位于缓存，位于DB同理)

  问题描述：多个服务读写同一份位于缓存的数据

  缺点：这种实践直接违反“服务化”理念中“数据私有”的原则。需要界定该数据，属于哪一个业务。

  解决方案：

  - 根据“服务化”的原则，数据是“私有”的。
  - 服务需要通过接口的方式，隐藏数据修改的复杂性。
  - 服务调用方不能绕过接口直接修改服务提供方的数据。

- 多服务共用缓存实例

  问题描述：多个服务共用同一缓存实例

  缺陷：

  - 服务之间 key 冲突	
  - 不同服务吞吐量不同，容易导致数据量较小服务的“热数据”被挤压
  - 共用一个实例，导致服务间的耦合(如key的约束规则等)

  解决方案： 

  - 各个服务私有化各自的缓存实例。
  - 使用统一的缓存服务。此时，key的约束讲之发生在服务和统一缓存服务之间。(统一缓存服务负责分配namespace给不同的需求方)

- 服务提供方和调用方同时使用缓存

  问题描述：服务提供方和调用方同时缓存数据，造成数据不一致。

  问题原因：

  - 服务提供方使用缓存，向服务调用方隐藏获取数据的复杂性。
  - 服务调用方再次缓存数据。造成数据一致性难以维持。

  解决方案：

  - 服务提供/调用双方需协商。但是这种协商会增加服务双方的耦合性。
  - 一种可操作的代码层面的实践方式时，保留服务调用方引入缓存的能力，根据服务提供方是否使用缓存来打开/关闭服务调用方使用缓存的能力。比如django的dummy cache.