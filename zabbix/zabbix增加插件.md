# 用户参数
如果我们想运行一个不是zabbix预定义好的Agent Item，就可以使用用户参数。

# 如何操作
你首先需要写一个用来得到数据的命令，然后按指定格式配置在agent配置文件中（通常是：zabbix_agentd.conf），
下面是相应的语法：
```conf
UserParameter=key[*],command
```
|参数|描述|
|:-|:-|
|key|key是用来标识这个Agent Item,必须在Agent中唯一|
|[*]|[*]定义这个key可以接收参数（位于[]内部），这些参数用来配置这个Item（可选）|
|command|command会被执行，生成key的结果|

*说明*：
1. 访问`key`Item中参数时，用位置参数$1...$9。
2. $0会被原始命令替换掉。如果`command`为`ls -l`,那么`$0`为`ls`。
3. 不管在`"`或在`'`中，位置参数都会被替换。
4. 为了使位置参数不被替换，需要指定$$。例如，`awk '{print $$2'}`,在这里$$2实际上转换为$2。不能直接写`$2`,否则会被替换成为`key` Item传进来的第二个参数。

*注意*： 用户参数遵循Agent检查超时，如果超时，fork出来的用户参数进程会被终止掉。

# 例子 -- 增加Agent fping外部地址。

