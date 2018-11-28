# Summary

* [Introduction](README.md)

* [Principle View](principle_view/principle_view.md)
  * [algorithm](principle_view/algorithm/algorithm.md)
    * [sort](principle_view/algorithm/sort/sort.md)
      * [radix sort](principle_view/algorithm/sort/radix_sort.md)
      * [counting sort](principle_view/algorithm/sort/counting_sort.md)
  * [os](principle_view/os/os.md)
  * [nework](principle_view/network/network.md)
    * [tcp/ip](principle_view/network/tcp-ip/tcp-ip.md)
      * [keep-alive](principle_view/network/tcp-ip/keep_alive/keep_alive.md)
        * http keep-alive
        * web server keep-alive
        * [tcp supporting of keep-alive](principle_view/network/tcp-ip/keep_alive/tcp_support.md)
    * [http](principle_view/network/http.md)
  * [distributed system](principle_view/distributed_system/distributed_system.md)
    * [consensus](principle_view/distributed_system/consensus/consensus.md)
      * [Paxos](principle_view/distributed_system/consensus/paxos.md)
* [Component View](component_view/component_view.md)
  * database
  * web server
  * [app server](component_view/app_server/app_server.md)
    * [guicorn](component_view/app_server/gunicorn/gunicorn.md)
      * [gunicode + django key points](component_view/app_server/gunicorn/gunicorn_django_key_points/gunicorn_django_keep_points.md)
  * [cache](component_view/cache/cache.md)
    * [principles](component_view/cache/principles/principles.md)
      * [definition](component_view/cache/principles/definition.md)
      * [side effects](component_view/cache/principles/side_effects.md)
      * [replacement policies](component_view/cache/principles/replacement_policies.md)
      * [update patterns](component_view/cache/principles/update_patterns.md)
    * [key-points](component_view/cache/key_points/key_points.md)
      * [cache miss](component_view/cache/key_points/cache_miss.md)
      * [cache miss-usage](component_view/cache/key_points/cache_misusage.md)
      * [standalone cache service](component_view/cache/key_points/standalone_cache_service.md)
      * [cache update, delete or set](component_view/cache/key_points/cache_update_delete_or_set.md)
      * [data update, db first or cache first](component_view/cache/key_points/update_data_db_first_or_cache_first.)
    * [memcache](component_view/cache/memcache/memcache.md)
    * Redis 
  * mq
  * log
  * monitor 
* Architecture View
  * performance
  * availabilty
  * scalability
  * extensibility
  * security
* [Scenairo View]()
  * payment
* Miscellaneous