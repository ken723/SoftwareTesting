
# JMeter数据库测试

## 概述:

    本测试方法用于数据库语句的功能测试和性能测试.

## 总结思路：

    通过java数据库驱动连接mysql数据库, 创建线程执行sql语句.


## 使用方法:

    1. 测试计划

    2. 创建线程组

    3. 在线程组中创建 JDBC Connection Configuration

    4. 在线程组中创建 JDBC Request
 
    5. 测试计划中创建查看结果树

    6. 测试计划中创建聚合报告(可选)

    7. 测试计划中创建汇总报告(可选)

## 参数设置
### JDBC Connection Configuration
    名称: 配置文件名称
    注释: 配置文件说明
    Variable Name for created pool: 数据库连接池的名称
    Max Number for Connections: 最大数据库连接数

    Database URL: jdbc:mysql://地址:端口号/数据库名
    JDBC Driver class: com.mysql.jdbc.Driver
    Username: 数据库用户名
    Password: 数据库密码

### JDBC Request
    名称: 数据库请求的名称

    Query Type: 查询类型

    Query: 查询语句
    例如: show tables;
 


## 常见的报错问题：

    错误: Can not issue data manipulation statements with executeQuery().
    原因: Query  Type的问题。


## 查询类型的参考: 

- Select statement

    查询语句类型；
    如果JDBC Request中的Query内容为一条查询语句，则选择这种类型。

    PS：
    
        多个查询语句(不使用参数的情况下)可以放在一起顺序执行，需要设置Query Type为：Callable Statement；
        如果Query Type为：select Statement，则只执行第一条select语句。

 

- Update statement

    更新语句类型（包含insert和update）；
    如果JDBC Request中的Query内容为一条更新语句，则选择这种类型。

    PS：
        
        如果该类型下写入多条update语句，依然只执行第一条（原因同上，具体下面介绍）。

 

- Callable statement

    调用语句类型，
    CallableStatement 为所有的 DBMS 提供了一种以标准形式调用已储存过程的方法。
    已储存过程储存在数据库中，对已储存过程的调用是 CallableStatement 对象所含的内容。
    
    这种调用是用一种换码语法来写的，有两种形式：一种形式带结果参数，另一种形式不带结果参数；结果参数是一种输出 (OUT) 参数，是已储存过程的返回值。
    两种形式都可带有数量可变的输入（IN 参数）、输出（OUT 参数）或输入和输出（INOUT 参数）的参数，问号将用作参数的占位符。 

    在 JDBC 中调用已储存过程的语法如下所示。注意，方括号表示其间的内容是可选项；方括号本身并不是语法的组成部份。 
    {call 过程名[(?, ?, ...)]}，返回结果参数的过程的语法为： {? = call 过程名[(?, ?, ...)]}；
    不带参数的已储存过程的语法类似：{call 过程名}。




- Prepared select statement

    SQL语句生成执行计划（这也是为什么select statement只会执行第一条select语句的原因），
    如果只执行一次SQL语句，statement是最好的类型；

    Prepared statement用于绑定变量重用执行计划，对于多次执行的SQL语句，Prepared statement无疑是最好的类型（生成执行计划极为消耗资源，两种实现速度差距可能成百上千倍）；

    PS：
        PreparedStatement的第一次执行消耗是很高的. 它的性能体现在后面的重复执行。
 

- Prepared update statement

    Prepared update statement和Prepared select statement的用法是极为相似的，
    具体可以参照第四种类型。

 

- Commit

    将未存储的SQL语句结果写入数据库表；
    在jmeter的JDBC请求中，同样可以根据具体使用情况，选择这种Query类型。

 

- Rollback

    撤销回滚指定SQL语句的过程；
    

- AutoCommit(false)

    MySQL默认操作模式就是autocommit自动提交模式。
    表示除非显式地开始一个事务，否则每条SQL语句都被当做一个单独的事务自动执行；
    我们可以通过设置autocommit的值改变是否是自动提交autocommit模式；

    而AutoCommit(false)的意思是AutoCommit（假），即将用户操作一直处于某个事务中，直到执行一条commit提交或rollback语句才会结束当前事务重新开始一个新的事务。

 
- AutoCommit(true)

    这个选项的作用和上面一项作用相反，即：无论何种情况，都自动提交将结果写入，结束当前事务开始下一个事务。

