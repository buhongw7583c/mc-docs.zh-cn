---
title: Azure Monitor 日志查询中的函数 | Azure Docs
description: 本文介绍了如何在 Azure Monitor 中从一个日志查询中使用函数调用另一个查询。
services: log-analytics
documentationcenter: ''
author: lingliw
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/21/19
ms.author: v-lingwu
ms.openlocfilehash: 985c34764a29e5615ce41f8e8ba84b2449f13822
ms.sourcegitcommit: 7e25a709734f03f46418ebda2c22e029e22d2c64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56440616"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>在 Azure Monitor 日志查询中使用函数

> [!NOTE]
> 在学习本课程之前，需完成 [Analytics 门户入门](get-started-portal.md)和[查询入门](get-started-queries.md)。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


若要将某个日志查询用于其他查询，可以将其保存为函数。 这使你能够通过分解复杂查询将其简化，并能够对多个查询重用通用代码。

## <a name="create-a-function"></a>创建函数

在 Azure 门户中的 Log Analytics 中，单击“保存”然后提供下表中的信息来创建一个函数。

| 设置 | 说明 |
|:---|:---|
| Name           | 查询资源管理器中查询的显示名称。 |
| 另存为        | 函数 |
| 函数别名 | 在其他查询中使用该函数的短名称。 不可包含空格，必须唯一。 |
| 类别       | 用于在查询资源管理器中整理已保存的查询和函数的类别。 |

> [!NOTE]
> Azure Monitor 中的函数不能包含其他函数。

> [!NOTE]
> 可以在 Azure Monitor 日志查询中保存函数，但目前无法将这些函数用于 Application Insights 查询。



## <a name="use-a-function"></a>使用函数
通过在另一个查询中添加其别名来使用函数。 可以像使用其他任何表一样使用它。

## <a name="example"></a>示例
以下示例查询将返回最近一天报告的所有缺失的安全更新。 使用别名 security_updates_last_day 将此查询另存为函数。 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

创建另一个查询并引用 security_updates_last_day 函数，以搜索 SQL 相关的必需安全更新。

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>后续步骤
参阅有关编写 Azure Monitor 日志查询的其他课：

- [字符串操作](string-operations.md)
- [JSON 和数据结构](json-data-structures.md)
- [联接](joins.md)




