---
title: 策略分配结构的详细信息
description: 介绍策略分配定义，Azure Policy 使用该定义将策略定义和参数关联到资源，以进行评估。
ms.author: v-tawe
origin.date: 09/23/2019
ms.date: 03/09/2020
ms.topic: conceptual
ms.openlocfilehash: 6bde77b51de1e8d3ea9e474307236596b5974e3b
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "79292787"
---
# <a name="azure-policy-assignment-structure"></a>Azure Policy 分配结构

Azure Policy 使用策略分配来定义为哪些资源分配了哪些策略或计划。 在分配时，策略分配可以确定该组资源的参数值，因此，可以重复使用能够处理相同资源属性并满足不同合规需求的策略定义。

使用 JSON 创建策略分配。 策略分配包含以下各项的元素：

- 显示名称
- description
- metadata
- 强制模式
- 策略定义
- parameters

例如，以下 JSON 显示包含动态参数的、处于 _DoNotEnforce_ 模式的策略分配：

```json
{
    "properties": {
        "displayName": "Enforce resource naming rules",
        "description": "Force resource names to begin with DeptA and end with -LC",
        "metadata": {
            "assignedBy": "Cloud Center of Excellence"
        },
        "enforcementMode": "DoNotEnforce",
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming",
        "parameters": {
            "prefix": {
                "value": "DeptA"
            },
            "suffix": {
                "value": "-LC"
            }
        }
    }
}
```

所有 Azure Policy 示例均位于 [Azure Policy 示例](../samples/index.md)中。

## <a name="display-name-and-description"></a>显示名称和说明

使用 **displayName** 和 **description** 来标识策略分配，并提供它与特定资源集配合使用时的上下文。 **displayName** 的最大长度为 128  个字符，**description** 的最大长度为 512  个字符。

## <a name="enforcement-mode"></a>强制模式

**enforcementMode** 属性使客户能够测试对现有资源应用某个策略后的结果，而无需启动策略效果，或触发 [Azure 活动日志](../../../azure-monitor/platform/platform-logs-overview.md)中的条目。 此方案通常称为“What If”，与安全部署做法相符。 **enforcementMode** 不同于 [Disabled](./effects.md#disabled) 效果，后者会彻底阻止资源评估的发生。

此属性具有以下值：

|“模式” |JSON 值 |类型 |手动修正 |活动日志条目 |说明 |
|-|-|-|-|-|-|
|已启用 |默认 |字符串 |是 |是 |在创建或更新资源期间强制实施策略效果。 |
|已禁用 |DoNotEnforce |字符串 |是 |否 | 在创建或更新资源期间不强制实施策略效果。 |

如果未在策略或计划定义中指定 **enforcementMode**，则使用值 _Default_。 即使 [enforcementMode](../how-to/remediate-resources.md) 设置为 [DoNotEnforce](./effects.md#deployifnotexists)，也可以针对 **deployIfNotExists** 策略启动_修正任务_。

## <a name="policy-definition-id"></a>策略定义 ID

此字段必须是策略定义或计划定义的完整路径名称。
`policyDefinitionId` 是字符串，而不是数组。 如果经常要一起分配多个策略，我们建议改用[计划](./definition-structure.md#initiatives)。

## <a name="parameters"></a>parameters

此策略分配段为[策略定义或计划定义](./definition-structure.md#parameters)中定义的参数提供值。
通过这种设计，可对不同的资源重复使用某个策略或计划定义，但需要检查不同的业务价值或成果。

```json
"parameters": {
    "prefix": {
        "value": "DeptA"
    },
    "suffix": {
        "value": "-LC"
    }
}
```

在此示例中，事先在策略定义中定义的参数为 `prefix` 和 `suffix`。 此特定策略分配将 `prefix` 设置为 **DeptA**，将 `suffix` 设置为 **-LC**。 可对不同部门的一组不同参数重复使用同一个策略定义，以降低策略定义的重复性和复杂性，同时提供灵活性。

## <a name="next-steps"></a>后续步骤

- 了解[策略定义结构](./definition-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。