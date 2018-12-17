---
title: Azure 存储可伸缩性和性能目标
description: 了解标准 Azure 存储帐户的可伸缩性和性能目标，包括容量、请求速率以及入站和出站带宽。
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 11/08/2018
ms.date: 12/10/2018
ms.author: v-jay
ms.component: common
ms.openlocfilehash: 57bdba995253d8f8588ccea386edb2d0866ebce5
ms.sourcegitcommit: 5f2849d5751cb634f1cdc04d581c32296e33ef1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53028755"
---
# <a name="azure-storage-scalability-and-performance-targets-for-standard-storage-accounts"></a>标准存储帐户的 Azure 存储可伸缩性和性能目标

本文详细介绍了标准 Azure 存储帐户的可伸缩性和性能目标。 以下所列的可伸缩性和性能目标为高端目标，但却是能够实现的。 在任何情况下，存储帐户实现的请求速率和带宽取决于存储对象大小、使用的访问模式、应用程序执行的工作负荷类型。 

请务必测试服务，以确定其性能是否达到要求。 如果可能，应避免流量速率突发峰值，并确保流量在各个分区上均匀分布。

当应用程序达到分区能够处理的工作负荷极限时，Azure 存储将开始返回错误代码 503（服务器忙）或错误代码 500（操作超时）响应。 如果发生 503 错误，请考虑修改应用程序以使用指数退避策略进行重试。 使用指数退让策略，可以减少分区上的负载，缓解该分区的流量高峰。

## <a name="standard-storage-account-scale-limits"></a>标准存储帐户缩放限制
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="storage-resource-provider-scale-limits"></a>存储资源提供程序缩放限制 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob 存储缩放目标
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure 文件规模目标
有关 Azure 文件缩放和性能目标的详细信息，请参阅 [Azure 文件可伸缩性和性能目标](../files/storage-files-scale-targets.md)。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure 队列存储缩放目标
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure 表存储缩放目标
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>另请参阅
* [存储定价详细信息](https://www.azure.cn/pricing/details/storage/)
* [Azure 订阅和服务限制、配额和约束](../../azure-subscription-service-limits.md)
* [Azure 存储复制](../storage-redundancy.md)
* [Azure 存储性能和可伸缩性清单](../storage-performance-checklist.md)

<!--Update_Description: update content-->