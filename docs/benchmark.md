## 测试

#### 环境
我们分别使用三个机房的五台物理机搭建了五副本的 etcd 集群和其中两个机房的另外两台物理机搭建了一主一从的 MySQL 集群，

主机配置

|              |   | 
| --------           | :----- |
|OS	| Centos 7.2 64位 |
|CPU	| E5-2680v4(14核)*2|
|内存	| 256GB|
|RAID类型	| RAID 1|
|MySQL 主机间延迟 | 0.2 ms|
|MySQL 主机与 etcd 主机最大延迟 | 3.6 ms|


#### 测试方法 
在 MySQL 有写流量的情况下执行计划内的主从切换，并统计发生主从切换时，主库首次不可写到新主库首次可写之间的时间，也就是对业务写操作的影响时间。

#### 测试结果

并发: 4 , TPS: 2000 :

| 百分比（%）             | 写影响时间（秒）  | 
| --------           | :----- |
|  8  | 5     | 
|  32| 4      | 
|  34| 3      | 
|  26| 2      | 


并发: 8 , TPS: 4000 :

| 百分比（%）             | 写影响时间（秒）  | 
| --------           | :----- |
|  22  | 5     | 
|  33| 4      | 
|  33| 3      | 
|  12| 2      | 


并发: 16 , TPS: 6000 :

| 百分比（%）             | 写影响时间（秒）  | 
| --------           | :----- |
|  3  | 5     | 
|  35| 4      | 
|  31| 3      | 
|  27| 2      | 
|  4| 1      | 



并发: 32 , TPS: 6000 :

| 百分比（%）             | 写影响时间（秒）  | 
| --------           | :----- |
|  16  | 5     | 
|  33| 4      | 
|  33| 3      | 
|  18| 2      |


并发: 64 , TPS: 7700 :

| 百分比（%）             | 写影响时间（秒）  | 
| --------           | :----- |
|  29  | 5     | 
|  35| 4      | 
|  35| 3      | 
|  1| 2      |

#### 结论

可以看到，在 MySQL 的 QPS 达到 7700 时，发生**计划内主从切换**时对写操作的影响可以保证 99 线在 5s。
**计划外主从切换**的影响时间可通过 `Lease 时间 + 计划内切换影响时间` 推算。
