---
title: 使用 PowerShell 将托管磁盘的快照复制到同一订阅或不同订阅
description: Azure PowerShell 脚本示例 - 将托管磁盘的快照复制（移动）到相同或不同的订阅
services: virtual-machines-windows
documentationcenter: storage
author: rockboyfor
manager: digimobile
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
origin.date: 02/28/2019
ms.date: 02/10/2020
ms.author: v-yeche
ms.openlocfilehash: a77125f82f4e1b4f698fa8ed40ac32ef949779a7
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "77428789"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>使用 PowerShell 将托管磁盘的快照复制到同一订阅或不同订阅

此脚本会将托管磁盘的快照复制到同一订阅或不同订阅。 将此脚本用于以下方案：

1. 将高级存储 (Premium_LRS) 中的快照迁移到标准存储（Standard_LRS 或 Standard_ZRS）以降低成本。
1. 将快照从本地冗余存储（Premium_LRS、Standard_LRS）迁移到区域冗余存储（Standard_ZRS），以从 ZRS 存储的更高可靠性中受益。
1. 将快照移到同一区域中的不同订阅，以延长保留时间。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>示例脚本

```powershell
# Sign-in the Azure China Cloud
Connect-AzAccount -Environment AzureChinaCloud

#Provide the subscription Id of the subscription where snapshot exists
$sourceSubscriptionId='yourSourceSubscriptionId'

#Provide the name of your resource group where snapshot exists
$sourceResourceGroupName='yourResourceGroupName'

#Provide the name of the snapshot
$snapshotName='yourSnapshotName'

#Set the context to the subscription Id where snapshot exists
Select-AzSubscription -SubscriptionId $sourceSubscriptionId

#Get the source snapshot
$snapshot= Get-AzSnapshot -ResourceGroupName $sourceResourceGroupName -Name $snapshotName

#Provide the subscription Id of the subscription where snapshot will be copied to
#If snapshot is copied to the same subscription then you can skip this step
$targetSubscriptionId='yourTargetSubscriptionId'

#Name of the resource group where snapshot will be copied to
$targetResourceGroupName='yourTargetResourceGroupName'

#Set the context to the subscription Id where snapshot will be copied to
#If snapshot is copied to the same subscription then you can skip this step
Select-AzSubscription -SubscriptionId $targetSubscriptionId

#We recommend you to store your snapshots in Standard storage to reduce cost. Please use Standard_ZRS in regions where zone redundant storage (ZRS) is available, otherwise use Standard_LRS
#Please check out the availability of ZRS here: https://docs.microsoft.com/Az.Storage/common/storage-redundancy-zrs#support-coverage-and-regional-availability
$snapshotConfig = New-AzSnapshotConfig -SourceResourceId $snapshot.Id -Location $snapshot.Location -CreateOption Copy -SkuName Standard_LRS

#Create a new snapshot in the target subscription and resource group
New-AzSnapshot -Snapshot $snapshotConfig -SnapshotName $snapshotName -ResourceGroupName $targetResourceGroupName

```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令并使用源快照的 Id 在目标订阅中创建新快照。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | 创建用于创建快照的快照配置。 它包括父快照的资源 Id 以及与父快照相同的位置。  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) | 使用作为参数传递的快照配置、快照名称和资源组名称创建快照。 |

## <a name="next-steps"></a>后续步骤

[基于快照创建虚拟机](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。

<!--Update_Description: update meta properties, update link-->