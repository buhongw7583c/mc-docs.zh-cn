---
title: 慢查询日志 - Azure Database for MariaDB
description: 介绍了 Azure Database for MariaDB 中提供的日志，以及用于启用不同日志记录级别的可用参数。
author: WenJason
ms.author: v-jay
ms.service: mariadb
ms.topic: conceptual
origin.date: 01/28/2020
ms.date: 02/17/2020
ms.openlocfilehash: a6f335f06163766ddfaafc23730a91b9245a2c88
ms.sourcegitcommit: 3f9d780a22bb069402b107033f7de78b10f90dde
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192472"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的慢查询日志
在 Azure Database for MariaDB 中，慢查询日志可供用户使用。 不支持访问事务日志。 可以使用慢查询日志来查明性能瓶颈以进行故障排除。

有关慢查询日志的详细信息，请参阅[慢查询日志](https://mariadb.com/kb/en/library/slow-query-log-overview/)的 MariaDB 文档。

## <a name="access-slow-query-logs"></a>访问慢查询日志
可以使用 Azure 门户和 Azure CLI 列出和下载 Azure Database for MariaDB 慢查询日志。

在 Azure 门户中，选择 Azure Database for MariaDB 服务器。 在“监视”标题下，选择“服务器日志”页面。  

有关 Azure CLI 的详细信息，请参阅[使用 Azure CLI 配置和访问服务器日志](howto-configure-server-logs-cli.md)。

同样，你可使用诊断日志，通过管道将日志传送到 Azure Monitor。 有关详细信息，请参阅[下文](concepts-server-logs.md#diagnostic-logs)。

## <a name="log-retention"></a>日志保留期
日志从其创建时开始算起，最多可保留七天。 如果可用日志的总大小超过了 7 GB，则会删除最旧的文件，直到有空间可用。

日志每 24 小时或每 7 GB 轮换一次（以先达到的条件为准）。

## <a name="configure-slow-query-logging"></a>配置慢查询日志记录
默认情况下，慢查询日志被禁用。 若要启用它，请将 slow_query_log 设置为 ON。

可以调整的其他参数包括：

- **long_query_time**：如果某个查询花费的时间超过了 long_query_time（以秒为单位），则会记录该查询。 默认为 10 秒。
- **log_slow_admin_statements**：如果为 ON，则会在写入到 slow_query_log 的语句中包括管理性语句，例如 ALTER_TABLE 和 ANALYZE_TABLE。
- **log_queries_not_using_indexes**：确定是否将未使用索引的查询记录到 slow_query_log 中
- **log_throttle_queries_not_using_indexes**：此参数限制可以写入到慢查询日志的非索引查询的数目。 当 log_queries_not_using_indexes 设置为 ON 时，此参数生效。
- **log_output**：如果设置为“文件”，则允许将慢查询日志写入本地服务器存储和 Azure Monitor 诊断日志。 如果设置为“无”，则仅将慢查询日志写入 Azure Monitor 诊断日志。 

> [!IMPORTANT]
> 如果表未编制索引，则将 `log_queries_not_using_indexes` 和 `log_throttle_queries_not_using_indexes` 参数设置为 ON 可能会影响 MariaDB 性能，因为对这些非索引表运行的所有查询都将写入到慢速查询日志。<br><br>
> 如果计划在较长一段时间内记录慢速查询，建议将 `log_output` 设置为“None”。 如果设置为“File”，则这些日志将写入到本地服务器存储，并且可能会影响 MariaDB 性能。 

有关慢查询日志参数的完整说明，请参阅 MariaDB [慢查询日志文档](https://mariadb.com/kb/en/library/slow-query-log-overview/)。

## <a name="diagnostic-logs"></a>诊断日志
Azure Database for MariaDB 集成了 Azure Monitor 诊断日志。 在 MariaDB 服务器上启用慢查询日志后，可以选择将它们发送到 Azure Monitor 日志、事件中心或 Azure 存储。 若要详细了解如何启用诊断日志，请参阅[诊断日志文档](../azure-monitor/platform/platform-logs-overview.md)中的操作说明部分。

下表介绍了每个日志中的内容。 根据输出方法，包含的字段以及这些字段出现的顺序可能会有所不同。

| **属性** | **说明** |
|---|---|
| `TenantId` | 租户 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | 记录日志时的时间戳 (UTC) |
| `Type` | 日志的类型。 始终是 `AzureDiagnostics` |
| `SubscriptionId` | 服务器所属的订阅的 GUID |
| `ResourceGroup` | 服务器所属的资源组的名称 |
| `ResourceProvider` | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | 资源 URI |
| `Resource` | 服务器的名称 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | 服务器的名称 |
| `start_time_t` [UTC] | 查询开始时间 |
| `query_time_s` | 查询执行的总时间 |
| `lock_time_s` | 查询被锁定的总时间 |
| `user_host_s` | 用户名 |
| `rows_sent_s` | 发送的行数 |
| `rows_examined_s` | 检查的行数 |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | 插入 ID |
| `sql_text_s` | 完整查询 |
| `server_id_s` | 服务器 ID |
| `thread_id_s` | 线程 ID |
| `\_ResourceId` | 资源 URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>分析 Azure Monitor 日志中的日志

通过诊断日志将慢速查询日志传输到 Azure Monitor 日志后，可以进一步分析慢速查询。 下面是一些可帮助你入门的示例查询。 请确保使用你的服务器名称更新下面的内容。

- 特定服务器上超过 10 秒的查询

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- 列出特定服务器上最长的 5 个查询

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- 按最小、最大、平均和标准偏差查询时间汇总特定服务器上的慢速查询

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- 绘制特定服务器上的慢速查询的分布图

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 显示启用了诊断日志的所有 MariaDB 服务器上超过 10 秒的查询

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>后续步骤
- [如何通过 Azure 门户配置慢速查询日志](howto-configure-server-logs-portal.md)
- [如何通过 Azure CLI 配置慢速查询日志](howto-configure-server-logs-cli.md)
