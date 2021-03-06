---
title: 评审用户话语 - LUIS
titleSuffix: Azure Cognitive Services
description: 评审终结点话语通过主动学习获取正确的意向和实体。 LUIS 选择它不确定的终结点话语。
services: cognitive-services
author: lingliw
manager: digimobile
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
origin.date: 10/10/2019
ms.date: 12/04/2019
ms.author: v-lingwu
ms.openlocfilehash: 8d43971c8c0cbb30db792655c023219fb4d6cf7d
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "79292216"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>通过评审终结点话语启用主动学习的相关概念
主动学习是提高预测准确性的三个策略之一，也是最易于实现的策略。 评审终结点话语通过主动学习获取正确的意向和实体。 LUIS 选择它不确定的终结点话语。

## <a name="what-is-active-learning"></a>什么是主动学习
主动学习是一个两步过程。 首先，LUIS 选择其在应用终结点收到的需要验证的陈述。 第二步由应用所有者或协作者执行：验证要审查的所选话话，包括正确意向及该意向中的所有实体。 评审陈述后，再次训练并发布应用。 

## <a name="which-utterances-are-on-the-review-list"></a>评审列表上有哪些陈述
首要触发意向分数较低或者两个最高的意向分数过于接近时，LUIS 会将陈述添加到评审列表。 

## <a name="single-pool-for-utterances-per-app"></a>每个应用的话语的单个池
“查看终结点话语”列表不会根据版本更改  。 要审核的话语只有一个池，不管经常编辑哪个版本的话语，也不管在终结点上发布哪个版本的应用。 

## <a name="where-are-the-utterances-from"></a>陈述来自哪里
终结点陈述来自应用程序 HTTP 终结点上的最终用户查询。 如果未发布应用或应用未收到点击，则没有要评审的陈述。 如果未收到针对特定意向或实体的终结点点击，则没有包含它们的陈述需要进行评审。 

## <a name="schedule-review-periodically"></a>定期计划评审
不需要每天评审建议的陈述，但应将此纳入 LUIS 的常规维护。 

## <a name="delete-review-items-programmatically"></a>以编程方式删除评审项
使用 **[删除未标记话语](https://{region}.dev.cognitive.azure.cn/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API。 在删除之前，请通过 **[导出日志文件](https://{region}.dev.cognitive.azure.cn/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** 备份这些话语。




