事务的隔离级别

* DEFAULT 使用数据库的默认隔离级别
* READ_UNCOMMITED 允许读取还未提交的改变了的数据
* READ_COMMITED 允许在并发事务提交后读取
* REPEATABLE_READ 相同字段多次读取是一致的，除非数据库事务本身被改变
* SERIALIZABLE 完全服从ACID的隔离级别，最慢的

事务的传播行为

* PROPAGATION_REQUIRED 支持当前事务，如果不存在就新建一个
* PROPAGATION_SUPPORTS 支持当前事务，如果不存在，就不使用事务
* PROPAGATION_MANDATORY 支持当前事务，如果不存在，就抛出异常
* PROPAGATION\_REQUIRES_NEW 如果有事物存在，就挂起当前事务，创建一个新事物
* PROPAGATION\_NOT_SUPPORTED 以非事务方式运行，如果有事务存在，挂起当前事务
* PROPAGATION_NEVER 以非事物方式运行，如果有事物存在，抛出异常
* PROPAGATION_NESTED 如果当前事务存在，则嵌套事物执行


Oracle默认隔离级别READ_COMMITED  
MySQL默认隔离级别REPEATABLE_READ  

*****************1

Oracle默认隔离级别READ_COMMITED  
MySQL默认隔离级别REPEATABLE_READ
