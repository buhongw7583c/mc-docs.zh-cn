---
title: Azure 中的监视数据源
description: 了解 Azure 上目前提供的所有监视数据源。
author: lingliw
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
origin.date: 06/12/2018
ms.date: 11/26/2018
ms.author: v-lingwu
ms.component: ''
ms.openlocfilehash: 3e078c906ad3cdce41b3f382dcb5bebc1f1bf7a9
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52675150"
---
# <a name="consume-monitoring-data-from-azure"></a>使用 Azure 中的监视数据

在 Azure 平台上，我们使用 Azure Monitor 管道将监视数据集中在单个位置，但实际上需承认，目前并非所有监视数据都可在该管道中使用。 本文汇总了可以从 Azure 服务以编程方式访问监视数据的各种方法。

## <a name="options-for-data-consumption"></a>数据使用选项

| 数据类型 | 类别 | 支持的服务 | 访问方法 |
| --- | --- | --- | --- |
| Azure Monitor 平台级别指标 | 指标 | [请参阅此处的列表](monitoring-supported-metrics.md) | <ul><li>**REST API：**[Azure Monitor 指标 API](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**存储 blob 或事件中心：** [诊断设置](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| 计算来宾 OS 指标（例如 性能计数器） | 指标 | [Windows](../virtual-machines/virtual-machines-dotnet-diagnostics.md) 和 Linux 虚拟机 (v2)、[云服务](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)、[Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**存储表或 blob：**[Windows 或 Linux Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**事件中心：**[Windows Azure 诊断](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| 自定义或应用程序指标 | 指标 | 使用 Application Insights 检测到的任何应用程序 | <ul><li>**REST API：**[Application Insights REST API](https://dev.applicationinsights.io/reference)</li></ul> |
| 存储度量值 | 指标 | Azure 存储 | <ul><li>**存储表：**[存储分析](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| 活动日志 | 事件 | 所有 Azure 服务 | <ul><li>**REST API：**[Azure Monitor 事件 API](https://docs.microsoft.com/en-us/rest/api/monitor/eventcategories)</li><li>**存储 blob 或事件中心：**[日志配置文件](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Azure Monitor 诊断日志 | 事件 | [请参阅此处的列表](monitoring-diagnostic-logs-schema.md) | <ul><li>**存储 blob 或事件中心：** [诊断设置](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| 计算来宾 OS 日志（例如 IIS、ETW、syslog） | 事件 | [Windows](../virtual-machines/virtual-machines-dotnet-diagnostics.md) 和 Linux 虚拟机 (v2)、[云服务](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)、[Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**存储表或 blob：**[Windows 或 Linux Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**事件中心：**[Windows Azure 诊断](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| 应用服务日志 | 事件 | 应用程序服务 | <ul><li>**文件、表或 blob 存储：**[Web 应用诊断](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| 存储日志 | 事件 | Azure 存储 | <ul><li>**存储表：**[存储分析](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| 安全中心警报 | 事件 | Azure 安全中心 | <ul><li>**REST API：**[安全警报](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Active Directory 报告 | 事件 | Azure Active Directory | <ul><li>**REST API：**[Azure Active Directory 图形 API](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| 安全中心资源状态 | 状态 | [所有支持的资源](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**REST API：**[安全状态](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| 资源运行状况 | 状态 | 支持的服务 | <ul><li>**REST API：**[资源运行状况 REST API](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Azure Monitor 指标警报 | 通知 | [请参阅此处的列表](monitoring-supported-metrics.md) | <ul><li>**Webhook：**[Azure 指标警报](insights-webhooks-alerts.md)</li></ul> |
| Azure Monitor 活动日志警报 | 通知 | 所有 Azure 服务 | <ul><li>**Webhook：** Azure 活动日志警报</li></ul> |
| 自动缩放通知 | 通知 | [查看此处的列表](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Webhook：**[自动缩放通知 webhook 有效负载架构](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |

## <a name="next-steps"></a>后续步骤

- 深入了解 [Azure Monitor 指标](monitoring-overview-metrics.md)
- 深入了解 [Azure 活动日志](monitoring-overview-activity-logs.md)
- 深入了解 [Azure 诊断日志](monitoring-overview-of-diagnostic-logs.md)