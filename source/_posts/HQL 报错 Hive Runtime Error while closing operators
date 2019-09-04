---
title: HQL 报错 Hive Runtime Error while closing operators
date: 2019/9/4 21:39
tags: 
---

多个  select * from table group by key 相互join时, 会报

```log
[2019-09-04 21:36:48] Caused by: java.lang.RuntimeException: Hive Runtime Error while closing operators: org.apache.hadoop.hive.ql.metadata.HiveException: org.apache.hadoop.hive.ql.metadata.HiveException: java.lang.RuntimeException: org.apache.hadoop.hive.ql.metadata.HiveException: Hive Runtime Error while processing row (tag=1) {"key":{"_col0":"珠宝黄金","_col1":{0:{"_col0":"URL"}}},"value":null}
[2019-09-04 21:36:48] 	at org.apache.hadoop.hive.ql.exec.tez.ReduceRecordProcessor.close(ReduceRecordProcessor.java:328)
[2019-09-04 21:36:48] 	at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:164)
[2019-09-04 21:36:48] 	... 14 more
[2019-09-04 21:36:48] Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: org.apache.hadoop.hive.ql.metadata.HiveException: org.apache.hadoop.hive.ql.metadata.HiveException: java.lang.RuntimeException: org.apache.hadoop.hive.ql.metadata.HiveException: Hive Runtime Error while processing row (tag=1) {"key":{"_col0":"珠宝黄金","_col1":{0:{"_col0":"URL"}}},"value":null}
[2019-09-04 21:36:48] 	at org.apache.hadoop.hive.ql.exec.GroupByOperator.closeOp(GroupByOperator.java:1102)
[2019-09-04 21:36:48] 	at org.apache.hadoop.hive.ql.exec.Operator.close(Operator.java:622)
[2019-09-04 21:36:48] 	at org.apache.hadoop.hive.ql.exec.tez.ReduceRecordProcessor.close(ReduceRecordProcessor.java:304)
[2019-09-04 21:36:48] 	... 15 more
```

类似的错误

解决方法不用tez换用mr, 加上

```sql
set hive.execution.engine=mr;
```
