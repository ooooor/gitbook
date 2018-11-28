1. 千万不要直接陷入细节里，应鸟瞰全貌。

MySQL的基本架构：

* 客户端

* server层

  * 连接器
    * 管理连接，权限验证
    * 用户认证通过，查出权限表中拥有权限。
    * show processlist; 查询连接状态
    * 长连接、短连接，默认wait\_timeout为8h。
    * 长连接，临时使用的内存管理在连接对象中，断开时释放，导致MySQL占用内存飙升。
    * v5.7后，可通过执行mysql\_reset\_connection初始化连接，不需要重新验证。
  * 查询缓存
    * 命中则直接返回结果
    * 得到查询请求，先查询缓存，命中则直接返回。
    * 不建议使用查询缓存，表的更新，表上所有查询缓存都会被清空，除非业务静态表。
    * select SQL\_CACHE \* from T where id=1; 使用SQL\_CACHE显示指定使用查询缓存。
    * v8.0 后，查询缓存功能被删掉。
  * 分析器
    * 词法分析：识别字符串， 语法分析：是否满足MySQL语法，语义检查：列是否存在等。
    * 预处理器进一步检查解析树的合法。比如: 数据表和数据列是否存在, 别名是否有歧义等。如果通过则生成新的解析树，再提交给优化器
  * 优化器
    * 生成执行计划，索引选择
  * 执行器
    * 操作引擎，返回结果
    * 开始执行时，首先判断是否有表的执行权限。
    * 有权限，则使用引擎接口。
    * e.g. 调用InnoDB引擎接口，取满足条件表第一行，不是则跳过，是则存入结果集，继续掉接口取下一行，直到遍历完成。
    * rows\_examined表示执行器调用引擎接口次数。

* 存储引擎（V5.5.5后默认为InnoDB）

  * 提供读写接口



