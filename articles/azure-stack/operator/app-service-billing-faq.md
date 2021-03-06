---
title: Azure Stack Hub 上的 Azure 应用服务计费概述和常见问题解答
description: 有关 Azure Stack Hub 上的 Azure 应用服务的计量和计费方式的详细信息。
author: WenJason
manager: digimobile
ms.topic: article
origin.date: 06/10/2019
ms.date: 02/24/2020
ms.author: v-jay
ms.reviewer: anwestg
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 812b6cd2c0d28091a65efde4f2a4a6298d967aa1
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "77540633"
---
# <a name="azure-app-service-on-azure-stack-hub-billing-overview-and-faq"></a>Azure Stack Hub 上的 Azure 应用服务计费概述和常见问题解答

本文介绍 Azure 如何对提供 Azure Stack Hub 上的 Azure 应用服务的云运营商计费，以及云运营商如何对租户收取其服务使用费。

## <a name="billing-overview"></a>计费概述

Azure Stack Hub 云运营商选择将 Azure Stack Hub 上的 Azure 应用服务部署在其 Azure Stack Hub 阵列上，以便为其客户提供 Azure 应用服务和 Azure Functions 的租户功能。 Azure 应用服务资源提供程序包括多个可划分为基础结构层或辅助角色层的角色。

基础结构角色对于服务的核心操作是不可或缺的，因此不会产生费用。 可根据需要横向扩展基础结构角色，以支持云运营商租户的需求。 基础结构角色包括：

- 控制器
- 管理角色
- 发布服务器
- 前端

辅助角色层包括两种主要类型：共享和专用。 辅助角色使用费将根据以下准则计入云运营商的默认提供商订阅帐户。

## <a name="shared-workers"></a>共享辅助角色

共享辅助角色是多租户、无主机的共享应用服务计划和基于消耗量的 Azure Functions，可供许多租户使用。 共享辅助角色在 Azure 应用服务资源提供程序中标记为就绪时，将发出用量指标。

## <a name="dedicated-workers"></a>专用辅助角色

专用辅助角色绑定到租户创建的应用服务计划。 例如，在 S1 SKU 中，租户默认可以扩展到 10 个实例。 当租户创建 S1 应用服务计划时，Azure 应用服务会将小型辅助角色层规模集中的一个实例分配到该租户的应用服务计划。 然后，分配的辅助角色不再可以分配到其他任何租户。 如果租户选择将应用服务计划扩展到 10 个实例，则会进一步从可用池中删除 9 个辅助角色，并将其分配到租户的应用服务计划。

专用辅助角色在以下情况下发出指标：

- 在 Azure 应用服务资源提供程序中标记为就绪。
- 已分配到应用服务计划。

这种计费模式可让云运营商预配随时可供客户使用的专用辅助角色池，并且可等到租户的应用服务计划有效预留辅助角色之后，再支付辅助角色的费用。 

例如，假设你的小型辅助角色层中包含 20 个辅助角色。 随后有 5 个客户各创建了两个 S1 应用服务计划，且分别将应用服务计划扩展到两个实例，则你没有可用的辅助角色。 因此，任何现有客户或新客户也没有足够的容量可扩展或创建新的应用服务计划。 

云运营商可以在 Azure Stack Hub 管理界面的 Azure 应用服务配置中查看辅助角色层，以查看每个辅助角色层当前可用的辅助角色数目。

![应用服务 - 辅助角色层屏幕][1]

## <a name="see-customer-usage-by-using-the-azure-stack-hub-usage-service"></a>使用 Azure Stack Hub 用量服务查看客户的用量

云运营商可以查询 [Azure Stack Hub 租户资源用量 API](azure-stack-tenant-resource-usage-api.md) 以检索客户的用量信息。 可以在[使用情况常见问题解答](azure-stack-usage-related-faq.md)中，找到应用服务发出的用于描述租户用量的每个指标。 然后，这些指标将用于计算每个客户订阅的用量，以计算费用。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-prerequisites"></a>如何为先决条件中所需的 SQL Server 和文件服务器基础结构授权？

Azure Stack Hub 上的 Azure 应用服务[开始之前](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server)一文中介绍了如何为 Azure 应用服务资源提供程序所需的 SQL Server 和文件服务器基础结构授权。

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>“使用情况常见问题解答”列出了租户计量值，但未列出这些计量值的价格。 在哪里可以找到这些信息？

云运营商可以任意对客户应用自己的定价模型。 使用情况服务将提供用量计量值。 你可以使用计量数量根据确定的定价模型向客户收费。 自行定价可让不同的 Azure Stack Hub 运营商各自保留独特之处。

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-try-out-the-service"></a>身为 CSP，我要如何提供免费和共享的 SKU 让客户试用服务？

云运营商提供免费和共享的 SKU 会产生费用，由于这些 SKU 托管在共享的辅助角色中。 若要尽量降低成本，可以选择将共享辅助角色层缩减到最低限度。 

例如，若要提供免费和共享的应用服务计划 SKU，并提供基于用量的功能，至少需要 1 个可用的 A1 实例。 共享辅助角色是多租户的，因此可以托管多个客户应用，各自独立且受应用服务沙盒的保护。 以这种方式缩放共享的辅助角色层，可将支出限制为每月 1 个 vCPU 的成本。

然后，可以选择创建要在计划中使用的配额，此计划只提供免费和共享的 SKU，并且限制客户可以创建的免费和共享应用服务计划数。

## <a name="sample-scripts-to-assist-with-billing"></a>用于帮助计费的脚本示例

Azure 应用服务团队创建了 PowerShell 脚本示例，以帮助客户查询 Azure Stack Hub 使用情况服务。 云运营商可以使用这些示例脚本为租户准备计费。 示例脚本位于 GitHub 上的 [Azure Stack Hub 工具存储库](https://github.com/Azure/AzureStack-tools)中。 应用服务脚本位于 [Usage 下的 AppService 文件夹](https://aka.ms/aa6zku8)中。

可用的脚本示例如下：

- [Get-AppServiceBillingRecords](https://aka.ms/aa6zku2)：此示例从 Azure Stack Hub 用量 API 提取 Azure Stack Hub 上的 Azure 应用服务计费记录。
- [Get-AppServiceSubscriptionUsage](https://aka.ms/aa6zku6)：此示例计算每个订阅的 Azure Stack Hub 上的 Azure 应用服务用量金额。 此脚本根据用量 API 中的数据和云运营商对每种计量所提供的价格，计算用量金额。
- [Suspend-UserSubscriptions](https://aka.ms/aa6zku7)：此示例根据云运营商指定的用量限制来暂停或启用订阅。

## <a name="next-steps"></a>后续步骤

- [Azure Stack Hub 租户资源用量 API](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png
