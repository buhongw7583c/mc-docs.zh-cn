---
title: 排查 Azure 文件共享备份问题
description: 本文提供在保护 Azure 文件共享时所发生的问题的故障排除信息。
author: lingliw
origin.date: 08/20/2019
ms.date: 10/18/2019
ms.author: v-lingwu
ms.topic: troubleshooting
ms.openlocfilehash: 491c62a699a5dec154db0e3b93cf836d314a6603
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "78850252"
---
# <a name="troubleshoot-problems-backing-up-azure-file-shares"></a>排查 Azure 文件共享备份问题
可参考下表中所列信息，排查使用 Azure 文件共享备份时遇到的问题和错误。

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>预览版期间 Azure 文件共享备份的限制
Azure 文件共享备份处于预览状态。 常规用途 v1 和常规用途 v2 存储帐户中的 Azure 文件共享均受支持。 Azure 文件共享不支持以下备份场景：
- 无法使用 CLI 通过 Azure 备份来保护 Azure 文件。
- 每天的计划备份数上限为 1。
- 每天的按需备份数上限为 4。
- 在存储帐户上使用[资源锁定](/cli/resource/lock?view=azure-cli-latest)，防止意外删除恢复服务保管库中的备份。
- 请勿删除由 Azure 备份创建的快照。 删除快照可能导致恢复点丢失和/或还原失败。
- 请勿删除受 Azure 备份保护的文件共享。 当前的解决方案会在文件共享删除后删除 Azure 备份创建的所有快照，因此会失去所有还原点。


## <a name="configuring-backup"></a>配置备份

下表用于配置备份：

| 错误消息 | 解决办法或解决方法提示 |
| ------------------ | ----------------------------- |
| 找不到存储帐户，因此无法配置 Azure 文件共享的备份 | <ul><li>等到发现完成。 <li>检查是否已使用另一恢复服务保管库对存储帐户中的任何文件共享进行保护。 **注意**：一个存储帐户中的所有文件共享只能在一个恢复服务保管库中进行保护。 <li>请确保文件共享不是存在于不受支持的存储帐户中。<li> 确保在存储帐户中选中“允许受信任的 Microsoft 服务访问此存储帐户”  复选框。[了解更多。](../storage/common/storage-network-security.md)|
| 门户中的错误指出无法发现存储帐户。 | 如果订阅为合作伙伴（已启用 CSP），请忽略此错误。 如果订阅未启用 CSP，且无法发现存储帐户，请联系支持部门。|
| 所选存储帐户验证或注册失败。| 重试该操作。如果问题仍然存在，请联系支持部门。|
| 无法在所选存储帐户中列出或查找文件共享。 | <ul><li> 确保存储帐户存在于资源组中（且自上次在保管库中进行验证/注册后尚未删除或移动）。<li>确保尚未删除要保护的文件共享。 <li>确保存储帐户是支持进行文件共享备份的存储帐户。<li>检查是否已在同一恢复服务保管库中对文件共享进行保护。|
| 备份文件共享配置（或保护策略配置）故障。 | <ul><li>请重试该操作，看问题是否仍然存在。 <li> 确保尚未删除要保护的文件共享。 <li> 如果在尝试同时保护多个文件共享时部分文件共享故障，请再次重试配置故障文件共享的备份。 |
| 在取消对文件共享的保护之后，无法删除恢复服务保管库。 | 在 Azure 门户中打开保管库 >“备份基础结构”   >   “存储帐户”，然后单击“注销”  ，将存储帐户从恢复服务保管库中删除。|

## <a name="error-messages-for-backup-or-restore-job-failures"></a>备忘或还原作业故障时的错误消息

| 错误消息 | 解决办法或解决方法提示 |
| -------------- | ----------------------------- |
| 由于找不到文件共享，操作失败。 | 确保尚未删除要保护的文件共享。|
| 存储帐户找不到或不受支持。 | <ul><li>确保存储帐户存在于资源组中，且自上次验证后未从资源组中删除或移除。 <li> 确保存储帐户是支持进行文件共享备份的存储帐户。|
| 你已达到此文件共享的最大快照限制，在旧的快照过期后才能继续生成快照。 | <ul><li> 为文件创建多个按需备份时，可能发生此错误。 <li> 每个文件共享的快照限制为 200 个，包括通过 Azure 备份生成的快照。 较旧的计划备份（或快照）会自动清除。 如果达到最大限制，则必须删除按需备份（或快照）。<li> 从 Azure 文件门户删除按需备份（Azure 文件共享快照）。 **注意**：如果删除 Azure 备份创建的快照，会失去恢复点。 |
| 文件共享备份或还原因存储服务限制而失败。 这可能是因为存储服务正忙于处理给定存储帐户的其他请求。| 稍后重试操作。 |
| 还原失败，找不到目标文件共享。 | <ul><li>确保所选存储帐户存在，且目标文件共享未删除。 <li> 确保存储帐户是支持进行文件共享备份的存储帐户。 |
| 由于存储帐户处于“已锁定”状态，备份或还原作业失败。 | 解除存储帐户上的锁定，或者使用删除锁定而不是读取锁定，然后重试该操作。 |
| 恢复失败，因为故障文件数超出阈值。 | <ul><li> 恢复失败原因在文件中列出（作业详细信息中提供了路径）。 请解决导致失败的问题，然后只对故障文件重试还原操作。 <li> 文件还原失败的常见原因： <br/> - 确保目前没有在使用故障文件。 <br/> - 父目录中存在其名称与故障文件名称相同的目录。 |
| 恢复失败，因为没有可以恢复的文件。 | <ul><li> 恢复失败原因在文件中列出（作业详细信息中提供了路径）。 解决导致失败的问题，然后只对故障文件重试还原操作。 <li> 文件还原失败的常见原因： <br/> - 确保目前没有在使用故障文件。 <br/> - 父目录中存在其名称与故障文件名称相同的目录。 |
| 还原失败，因为源中的某个文件不存在。 | <ul><li> 所选项不在恢复点数据中。 若要恢复这些文件，请提供正确的文件列表。 <li> 与恢复点对应的文件共享快照已手动删除。 请选择另一恢复点，然后重试还原操作。 |
| 正在进行恢复到同一目标的恢复作业。 | <ul><li>文件共享备份不支持并行恢复到同一目标文件共享。 <li>等待现有恢复完成，然后再试一次。 如果在恢复服务保管库中找不到恢复作业，请查看同一订阅中的其他恢复服务保管库。 |
| 还原操作失败，因为目标文件共享已满。 | 增加目标文件共享大小配额，使之能够容纳还原数据，然后重试该操作。 |
| 由于对与目标文件共享关联的文件同步服务资源执行预还原操作时出错，还原操作失败。 | 请稍后重试，如果问题仍然存在，请联系 Microsoft 支持部门。 |
| 一个或多个文件无法成功恢复。 有关详细信息，请查看上面给出的路径中的故障文件列表。 | <ul> <li> 恢复失败原因已在文件中列出（作业详细信息中提供了路径），请针对原因解决相关问题，只对故障文件重试还原操作。 <li> 文件还原失败的常见原因如下： <br/> - 确保目前没有在使用故障文件。 <br/> - 父目录中存在其名称与故障文件名称相同的目录。 |

## <a name="modify-policy"></a>修改策略

| 错误消息 | 解决办法或解决方法提示 |
| ------------------ | ----------------------------- |
| 正在对此项进行另一个配置保护操作。 | 请等待上一个修改策略操作完成，并过一段时间重试。|
| 正在对所选项进行另一项操作。 | 请等待其他正在进行的操作完成，并过一段时间重试 |

