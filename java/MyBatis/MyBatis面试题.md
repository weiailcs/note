### MyBatis 有几种分页方式？
答：逻辑分页和物理分页。
- 逻辑分页：使用 MyBatis自带的RowBounds进行分页，它是一次性查询很多数据，然后在数据中再进行检索。
- 物理分页：自己手写SQL分页或使用分页插件PageHelper，去数据库查询指定条数的分页数据的形式。

###  RowBounds 是一次性查询全部结果吗？为什么？
答：RowBounds 实并非是一次性查询出所有数据，因为 MyBatis 是对
jdbc 的封装，在 jdbc 驱动中有一个 Fetch Size 的配置，它规定了每次最多从数据库查询多少条数据。只是对于 jdbc 来说，
当你调用 next()的时候会自动帮你完成查询工作。

### MyBatis 逻辑分页和物理分页的区别是什么？
答：逻辑分页是一次性查询很多数据，然后再在结果中找到分页的数据。弊端是需要消耗大量的内存、有内存溢出的风险、对数据库压力较大。

物理分页是从数据库查询指定条数的数据，弥补了一次性全部查出的所有数据的种种缺点。

### MyBatis 是否支持延迟加载？延迟加载的原理是什么？
答：MyBatis 支持延迟加载，设置 lazyLoadingEnabled=true 即可。

延迟加载的原理：
调用的时候触发加载，而不是在初始化的时候就加载信息。(比如调用 a. getB(). 
getName()，这个时候发现 a. getB() 的值为 null，此时会单独触发事先保存好的关联 B 对象的 SQL，
先查询出来 B，然后再调用 a. setB(b)，而这时候再调用 a. getB(). getName() 就有值了，这就是延
迟加载的基本原理。)

### MyBatis一级缓存和二级缓存
答：

### MyBatis 有哪些执行器（Executor）？
答：mybatis有三种执行器。
- SimpleExecutor：每执行一次 update 或 select 就开启一个 Statement 对象，用完立刻关闭Statement 对象;
- ReuseExecutor：执行 update 或 select，以 SQL 作为 key 查找 Statement 对象，存在就使用，不存在就创建，用完后不关闭 Statement 对象，而是放置于 Map 内供下一次使用。简言之，就是重复使用Statement 对象;
- BatchExecutor:执行 update（没有 select，jdbc 批处理不支持 select），将所有 SQL 都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个 Statement 对象，每个Statement 对象都是 addBatch()完毕后，等待逐一执行 executeBatch()批处理，与 jdbc 批处理相同。

### MyBatis 分页插件的实现原理是什么？
答：使用 MyBatis 提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执
行的 SQL，然后重写 SQL，根据dialect方言，添加对应的物理分页语句和物理分页参数。

### 