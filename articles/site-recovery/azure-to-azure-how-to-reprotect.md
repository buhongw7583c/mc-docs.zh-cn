---
title: 重新保护使用 Azure Site Recovery 故障转移到主要区域的 Azure VM | Azure
description: 介绍在使用 Azure Site Recovery 将 Azure VM 从次要区域故障转移到主要区域后，如何重新保护 VM。
services: site-recovery
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.topic: article
origin.date: 11/27/2018
ms.date: 02/24/2020
ms.author: v-yeche
ms.openlocfilehash: 714bae23d6cceca8e04629d136cdf349c145516d
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "79290900"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>重新保护已故障转移到主要区域的 Azure VM

使用 [Azure Site Recovery](site-recovery-overview.md) 将 Azure VM 从一个区域[故障转移](site-recovery-failover.md)到另一个区域后，VM 将在次要区域中启动，且**不受保护**。 若要将 VM 故障回复到主要区域，请执行以下任务：

1. 在次要区域中重新保护 VM，以便这些 VM 开始复制到主要区域。
1. 重新保护完成并且 VM 开始复制后，可将其从次要区域故障转移到主要区域。

## <a name="prerequisites"></a>先决条件

- 必须提交将 VM 从主要区域故障转移到次要区域的操作。
- 主要目标站点应该可用，并且应该能够在该区域中访问或创建资源。

## <a name="reprotect-a-vm"></a>重新保护 VM

1. 在“保管库” > “复制的项”中，右键单击已故障转移的 VM，并选择“重新保护”。    重新保护方向应该显示为从次要区域到主要区域。

    ![重新保护](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. 查看资源组、网络、存储和可用性集。  。 如果有任何资源标记为“新”，则表示它们是在重新保护过程中创建的。
1. 重新保护作业使用最新数据植入目标站点。 该作业完成后，将发生增量复制。 然后，可以故障转移回到主要站点。 可以使用自定义选项，选择要在重新保护期间使用的存储帐户或网络。

    ![自定义选项](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>自定义重新保护设置

可在重新保护期间自定义以下目标 VM 的属性。

![自定义](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|属性 |注释  |
|---------|---------|
|目标资源组 | 修改创建 VM 的目标资源组。 在重新保护的过程中，将删除目标 VM。 可以选择在故障转移后要在其下创建 VM 的新资源组。 |
|目标虚拟网络 | 执行重新保护作业期间，无法更改目标网络。 若要更改网络，请重做网络映射。 |
|目标存储（辅助 VM 不使用托管磁盘） | 可以更改故障转移后 VM 使用的存储帐户。 |
|副本托管磁盘（辅助 VM 使用托管磁盘） | Site Recovery 在主要区域中创建副本托管磁盘以监视辅助 VM 的托管磁盘。 |
|缓存存储 | 可以指定复制期间要使用的缓存存储帐户。 默认情况下，如果不存在缓存存储帐户，则会创建一个帐户。 |
|可用性集 | 如果次要区域中的 VM 属于可用性集，则可为主要区域中的目标 VM 选择可用性集。 默认情况下，Site Recovery 会尝试在主要区域中查找并使用现有的可用性集。 在自定义期间，可以指定新的可用性集。 |

### <a name="what-happens-during-reprotection"></a>重新保护期间会发生什么情况？

默认会发生以下情况：

1. 在运行故障转移的 VM 的区域中创建一个缓存存储帐户。
1. 如果目标存储帐户（主要区域中的原始存储帐户）不存在，则会创建一个新帐户。 分配的存储帐户名称是辅助 VM 使用的存储帐户名称，并带有后缀 `asr`。
1. 如果 VM 使用托管磁盘，则主要区域中会创建副本托管磁盘以存储从辅助 VM 磁盘中复制的数据。
1. 如果目标可用性集不存在，将在执行重新保护作业期间根据需要新建一个可用性集。 如果已自定义重新保护设置，则会使用选定的可用性集。

触发重新保护作业时，如果目标 VM 存在，则会发生以下情况：

1. 关闭正在运行的目标端 VM。
1. 如果 VM 使用托管磁盘，则会创建原始磁盘的带有 `-ASRReplica` 后缀的副本。 原始磁盘将被删除。 `-ASRReplica` 副本用于复制。
1. 如果 VM 使用的是非托管磁盘，则会分离目标 VM 的数据磁盘并将其用于复制。 将会创建 OS 磁盘的一个副本并将其附加到 VM 上。 将分离原始 OS 磁盘并将其用于复制。
1. 仅同步源磁盘与目标磁盘之间的更改。 将通过比较两个磁盘来计算差异，然后传输这些差异。 查看以下信息来确定完成重新保护所需的估算时间。
1. 同步完成后，将开始增量复制，并根据复制策略创建恢复点。

触发重新保护作业时，如果目标 VM 和磁盘不存在，则会发生以下情况：

1. 如果 VM 使用托管磁盘，则会创建带有 `-ASRReplica` 后缀的副本磁盘。 `-ASRReplica` 副本用于复制。
1. 如果 VM 使用的是非托管磁盘，则会在目标存储帐户中创建副本磁盘。
1. 将全部磁盘从故障转移的区域复制到新的目标区域。
1. 同步完成后，将开始增量复制，并根据复制策略创建恢复点。

#### <a name="estimated-time-to-do-the-reprotection"></a>估计的进行重新保护所需的时间

大多数情况下，Azure Site Recovery 不会将完整数据复制到源区域。
以下条件确定了复制的数据量：

1. 如果源 VM 数据因某种原因（例如资源组更改/删除）而被删除、受损或不可访问，则在重新保护期间会执行完整的初始复制，因为源区域没有可用数据。
1. 如果源 VM 数据可访问，则在比较两个磁盘时，只计算差异，然后传输这些差异。 请查看下表获得估算的时间。

|示例场景 | 重新保护所需的时间 |
|---|---|
|源区域中有 1 个包含 1 TB 标准磁盘的 VM<br/>仅使用了 127 GB 数据，磁盘的剩余存储是空的。<br/>磁盘类型为“标准”，吞吐量为 60 MiB/秒。<br/>故障转移后未发生数据更改。| 大约时间：45 分钟 - 1.5 小时。<br/>在重新保护期间，Site Recovery 会填充所有数据的校验和，以 45 MBps 的速率填充 127 GB 数据大约需要 45 分钟。<br/>Site Recovery 进行自动缩放需要一些开销时间，大约为 20-30 分钟。<br/>没有流出费用。 |
|源区域中有 1 个包含 1 TB 标准磁盘的 VM<br/>仅使用了 127 GB 数据，磁盘的剩余存储是空的。<br/>磁盘类型为“标准”，吞吐量为 60 MiB/秒。<br/>故障转移后有 45 GB 的数据更改。| 大约时间：1 小时 - 2 小时。<br/>在重新保护期间，Site Recovery 会填充所有数据的校验和，以 45 MBps 的速率填充 127 GB 数据大约需要 45 分钟。<br/>以 45 MBps 的速率应用 45 GB 更改所需的传输时间大约为 17 分钟。<br/>流出费用仅针对 45 GB 的数据更改，不针对校验和。 |

## <a name="next-steps"></a>后续步骤

保护 VM 后，可以启动故障转移。 故障转移过程会关闭次要区域中的 VM，在主要区域中创建并启动 VM，这会出现短暂的停机。 我们建议选择适当的时间完成此过程，并在完全故障转移到主站点之前运行测试故障转移。 [详细了解](site-recovery-failover.md) Azure Site Recovery 故障转移。

<!-- Update_Description: update meta properties, wording update, update link -->