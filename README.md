# real-time-data-warehouse
数据仓库 实时数仓

# 什么是数据仓库？

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
