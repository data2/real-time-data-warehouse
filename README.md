# real-time-data-warehouse
数据仓库 实时数仓

# 什么是数据仓库？

数据仓库：**一个面向主题的、集成的、相对稳定的、反映历史变化的数据集合，用于支持管理决策**

数据仓库是将多个数据源的数据经过ETL（**Extract（抽取）、Transform（转换）、Load（加载）**）处理之后，按照一定的主题集成起来**提供决策支持**和联机分析应用的结构化数据环境

早期数据仓库构建主要指的是把企业的业务数据库如 ERP、CRM、SCM 等数据按照决策分析的要求建模并汇总到数据仓库引擎中，其应用以报表为主，目的是支持管理层和业务人员决策（中长期策略型决策）

随着业务和环境的发展，这两方面都在发生着剧烈变化。

* 随着IT技术走向互联网、移动化，数据源变得越来越丰富，在原来业务数据库的基础上出现了**非结构化数据**，比如网站 log，IoT 设备数据，APP 埋点数据等，这些数据量比以往结构化的数据大了几个量级，对 ETL 过程、存储都提出了更高的要求；

* 互联网的在线特性也将业务需求推向了**实时化**，随时根据当前客户行为而调整策略变得越来越常见，比如大促过程中库存管理，运营管理等（即既有中远期策略型，也有短期操作型）；同时公司业务互联网化之后导致同时服务的客户剧增，有些情况人工难以完全处理，这就需要机器自动决策。比如欺诈检测和用户审核。

![image](https://user-images.githubusercontent.com/13504729/114484488-e5110200-9c3c-11eb-8c4e-88f677fd2411.png)



# 实时数仓的应用场景

实时 OLAP 分析：OLAP 分析本身就是数仓领域重点解决的问题，基于公司大数据架构团队提供的基于 Flink 计算引擎的 stream sql 工具，Kafka 和 ddmq (滴滴自研)等消息中间件，druid 和 ClickHouse 等 OLAP 数据库，提升数仓的时效性能力，使其具有较优的实时数据分析能力。

实时数据看板：这类场景是目前公司实时侧主要需求场景，例如“全民拼车日”订单和券花销实时大屏曲线展示，顺风车新开城当日分钟级订单侧核心指标数据展示，增长类项目资源投入和收益实时效果展示等。

实时业务监控：滴滴出行大量核心业务指标需要具备实时监控能力，比如安全指标监控，财务指标监控，投诉进线指标监控等。

实时数据接口服务：由于各业务线之间存在很多业务壁垒，导致数仓开发很难熟悉公司内全部业务线，需要与各业务线相关部门在数据加工和数据获取方面进行协作，数仓通过提供实时数据接口服务的方式，向业务方提供数据支持。

![image](https://user-images.githubusercontent.com/13504729/114483863-b9414c80-9c3b-11eb-84ee-28436482d967.png)



# 架构图

![image](https://user-images.githubusercontent.com/13504729/114486505-9ebda200-9c40-11eb-976b-d98d834e74fe.png)

![image](https://user-images.githubusercontent.com/13504729/114489898-7df84b00-9c46-11eb-96a2-81b90c940a08.png)


#### 离线数仓 vs 实时数仓

![image](https://user-images.githubusercontent.com/13504729/114487291-ec86da00-9c41-11eb-9c65-b174cec43a60.png)



#### 实时数仓三种架构



![image](https://user-images.githubusercontent.com/13504729/114487319-fad4f600-9c41-11eb-8391-d5d310ae64e7.png)


# 大厂实践 - 美团

![image](https://user-images.githubusercontent.com/13504729/114491445-39ba7a00-9c49-11eb-854e-71f4abf88383.png)

![image](https://user-images.githubusercontent.com/13504729/114491518-55258500-9c49-11eb-94a1-1b7ba6c5dc84.png)

下图所示的是美团点评实时计算平台的架构。

* 最底层是收集层，这一层负责收集用户的实时数据，包括 Binlog、后端服务日志以及 IoT 数据，经过日志收集团队和 DB 收集团队的处理，数据将会被收集到 Kafka 中。这些数据不只是参与实时计算，也会参与离线计算。
* 收集层之上是存储层，这一层除了使用 Kafka 做消息通道之外，还会基于 HDFS 做状态数据存储以及基于 HBase 做维度数据的存储。
* 存储层之上是引擎层，包括 Storm 和 Flink。实时计算平台会在引擎层为用户提供一些框架的封装以及公共包和组件的支持。
* 在引擎层之上就是平台层了，平台层从数据、任务和资源三个视角去管理。
* 架构的最上层是应用层，包括了实时数仓、机器学习、数据同步以及事件驱动应用等。

传统数仓模型
为了更有效地组织和管理数据，数仓建设往往会进行数据分层，一般自下而上分为四层：ODS（操作数据层）、DWD（数据明细层）、DWS（汇总层）和应用层。即时查询主要通过 Presto、Hive 和 Spark 实现。

![image](https://user-images.githubusercontent.com/13504729/114491666-8bfb9b00-9c49-11eb-8fb4-fea0df93e2e5.png)


实时数仓模型
实时数仓的分层方式一般也遵守传统数据仓库模型，也分为了 ODS 操作数据集、DWD 明细层和 DWS 汇总层以及应用层。但实时数仓模型的处理的方式却和传统数仓有所差别，如明细层和汇总层的数据一般会放在 Kafka 上，维度数据一般考虑到性能问题则会放在 HBase 或者 Tair 等 KV 存储上，即席查询则可以使用 Flink 完成。

![image](https://user-images.githubusercontent.com/13504729/114491672-8f8f2200-9c49-11eb-9f2f-5927516c57ac.png)


实时数仓和传统数仓的对比主要可以从四个方面考虑：

* 第一个是分层方式，离线数仓为了考虑到效率问题，一般会采取空间换时间的方式，层级划分会比较多；则实时数仓考虑到实时性问题，一般分层会比较少，另外也减少了中间流程出错的可能性。
* 第二个是事实数据存储方面，离线数仓会基于 HDFS，实时数仓则会基于消息队列（如 Kafka）。
* 第三个是维度数据存储，实时数仓会将数据放在 KV 存储上面。
* 第四个是数据加工过程，离线数仓一般以 Hive、Spark 等批处理为主，而实时数仓则是基于实时计算引擎如 Storm、Flink 等，以流处理为主。


# 大厂实践 - 网易基于Flink的严选实时数仓实践

![image](https://user-images.githubusercontent.com/13504729/114491803-d7ae4480-9c49-11eb-9308-37528e6d1ad3.png)

技术选型 

![image](https://user-images.githubusercontent.com/13504729/114491844-ea287e00-9c49-11eb-89a6-ffb899d1af84.png)


# 大厂实践 - OPPO 实时数仓揭秘：从顶层设计实现离线与实时的平滑迁移

![image](https://user-images.githubusercontent.com/13504729/114491919-1217e180-9c4a-11eb-984a-09a443057b33.png)



![image](https://user-images.githubusercontent.com/13504729/114491995-3b387200-9c4a-11eb-9c75-29d17820727e.png)


如下图所示的是 OPPO 实时数仓的分层结构，从接入层过来之后，所有的数据都是会用 Kafka 来支撑的，数据接入进来放到 Kafka 里面实现 ODS 层，然后使用 Flink SQL 实现数据的清洗，然后就变到了 DWD 层，中间使用 Flink SQL 实现一些聚合操作，就到了 ADS 层，最后根据不同的业务使用场景再导入到ES等系统中去。当然，其中的一些维度层位于 MySQL 或者 Hive 中。
![image](https://user-images.githubusercontent.com/13504729/114492000-3ecbf900-9c4a-11eb-81c1-3738f536acee.png)


# 总结 - 实时数仓的层级划分

总结下，实时数仓主要有两个要点。首先是分层设计上，一般也是参考离线数仓的设计，通常会分为ODS操作数据层、DWD明细层、DWS汇总层以及ADS应用层，可能还会分出一层DIM维度数据层。另外分层设计上也有不同的思路，比如可以将DWS和ADS归为DM数据集市层，网易严选就是这样设计的。

技术选型上，离线数仓一般依托HDFS或Hive构建，选择MR或Spark计算引擎；实时数仓存储层更多是选择Kafka等消息引擎，通常明细层和汇总层都放在Kafka，计算层则多是选择Flink/Spark Streaming/Storm，这方面Flink更有优势，社区也更倾向于选择Flink。
