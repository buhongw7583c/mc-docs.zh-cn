---
title: 使用 PowerShell 备份 Azure 文件存储
description: 本文介绍如何使用 Azure 备份服务和 PowerShell 来备份 Azure 文件存储。
author: lingliw
manager: digimobile
ms.topic: conceptual
origin.date: 08/20/2019
ms.date: 11/05/2019
ms.author: v-lingwu
ms.openlocfilehash: 73e59baa92d85fb814cce11041bde1aec1fe7975
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "79290727"
---
# <a name="back-up-azure-files-with-powershell"></a>使用 PowerShell 备份 Azure 文件存储

本文介绍如何在 Azure PowerShell 中使用 [Azure 备份](backup-overview.md)恢复服务保管库来备份“Azure 文件存储”文件共享。

本文介绍如何执行以下操作：

> [!div class="checklist"]
>
> * 设置 PowerShell 并注册 Azure 恢复服务提供程序。
> * 创建恢复服务保管库。
> * 配置 Azure 文件共享的备份。
> * 运行备份作业。

## <a name="before-you-start"></a>开始之前

- [详细了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
- 查看恢复服务的 PowerShell 对象层次结构。

## <a name="recovery-services-object-hierarchy"></a>恢复服务对象层次结构

下图汇总了对象层次结构。

![恢复服务对象层次结构](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

查看 Azure 库中的 **Az.RecoveryServices** [cmdlet 参考](https://docs.microsoft.com/powershell/module/az.recoveryservices)。

## <a name="set-up-and-install"></a>设置和安装

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

按如下所述设置 PowerShell：

1. [下载最新版本的 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。 所需的最低版本为 1.0.0。

> [!WARNING]
> 预览版所需的最低 PS 版本是“Az 1.0.0”。 由于正式版即将进行一些更改，所需的最低 PS 版本将是“Az.RecoveryServices 2.6.0”。 将所有现有 PS 版本升级到此版本非常重要。 否则，现有脚本在正式版发布后将会破坏。 使用以下 PS 命令安装最低版本

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. 找到包含以下命令的 Azure 备份 PowerShell cmdlet：

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. 查看 Azure 备份、Azure Site Recovery 和恢复服务保管库的别名与 cmdlet。 下面是可能显示的内容示例。 此 cmdlet 列表并不完整。

    ![恢复服务 cmdlet 列表](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. 使用 **Connect-AzAccount -EnvironmentName AzureChinaCloud** 登录到 Azure 帐户。
5. 在出现的网页上，系统会提示你输入帐户凭据。

    * 或者，可以结合 **-Credential** 在 **Connect-AzAccount** cmdlet 中将帐户凭据包含为参数。
    * 如果你是代表租户的 CSP 合作伙伴，则需使用 tenantID 或租户主域名将客户指定为一名租户。 例如，Connect-AzAccount -Tenant fabrikam.com  。

6. 由于一个帐户可以有多个订阅，因此请将要使用的订阅与帐户关联在一起。

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 首次使用 Azure 备份时，请使用 Register-AzResourceProvider cmdlet 将 Azure 恢复服务提供程序注册到订阅  。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 验证提供程序是否已成功注册：

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 在命令输出中，确认 **RegistrationState** 是否更改为 **Registered**。 如果不是，请再次运行 **Register-AzResourceProvider** cmdlet。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是一种资源管理器资源，因此必须将其放在资源组中。 可以使用现有资源组，也可以使用 New-AzResourceGroup cmdlet 创建资源组  。 创建资源组时，请指定资源组的名称和位置。

请按照以下步骤创建恢复服务保管库。

1. 保管库放在资源组中。 如果没有现有的资源组，请使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0) 新建一个。 此示例在“中国东部”区域创建一个新的资源组。

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "China East"
   ```

2. 使用 [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet 创建保管库。 请为保管库指定与资源组相同的位置。

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "China East"
    ```

3. 指定保管库存储使用的冗余类型。

   * 可以使用[本地冗余存储](../storage/common/storage-redundancy-lrs.md)或[异地冗余存储](../storage/common/storage-redundancy-grs.md)。
   * 以下示例针对 **testvault** 设置为 **GeoRedundant** 的 [Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) 命令设置 **-BackupStorageRedundancy** 选项。

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>在订阅中查看保管库

若要查看订阅中的所有保管库，请使用 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)。

```powershell
Get-AzRecoveryServicesVault
```

输出如下所示。 可以看到，其中已提供资源组名称和位置。

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : China North
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>设置保管库上下文

将保管库对象存储在变量中，并设置保管库上下文。

* 许多 Azure 备份 cmdlet 要求将恢复服务保管库对象用作输入，因此可以方便地在变量中存储保管库对象。
* 保管库上下文是在保管库中受保护的数据的类型。 可以使用 [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) 设置它。 设置上下文后，它将应用于所有后续 cmdlet。

以下示例为 testvault 设置保管库上下文  。

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>提取保管库 ID

我们已计划根据 Azure PowerShell 指导原则弃用保管库上下文设置。 可以改为存储或提取保管库 ID，并将其传递给相关命令。 因此，如果你尚未设置保管库上下文或想要指定为某个保管库运行的命令，请将保管库 ID 作为“-vaultID”传递给所有相关命令，如下所示：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>配置备份策略

备份策略指定备份计划，以及备份恢复点的保留期限：

* 一个备份策略至少与一个保留策略相关联。 保留策略定义了在将恢复点删除之前将其保留的时限。
* 使用 [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0) 查看默认的备份保留策略。
* 使用 [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0) 查看默认的备份策略计划。
* 使用 [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet 创建新的备份策略。 输入计划和保留策略对象。

默认情况下，会在“计划策略对象”中定义开始时间。 请使用以下示例将开始时间更改为所需的开始时间。 所需的开始时间也应采用 UTC 格式。 以下示例假设在进行每日备份时，所需的开始时间为 UTC 时间凌晨 1:00。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 只需以 30 分钟的倍数提供开始时间。 在上面的示例中，开始时间只能是“01:00:00”或“02:30:00”。 开始时间不能为“01:15:00”

以下示例将计划策略和保留策略存储在变量中。 然后，它使用这些变量作为新策略 (**NewAFSPolicy**) 的参数。 “NewAFSPolicy”进行每日备份，并将其保留 30 天  。

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

输出如下所示。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>启用备份

定义备份策略后，可以使用该策略为 Azure 文件共享启用保护。

### <a name="retrieve-a-backup-policy"></a>检索备份策略

使用 [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) 提取相关的策略对象。 使用此 cmdlet 获取特定的策略，或者查看与某个工作负荷类型关联的策略。

#### <a name="retrieve-a-policy-for-a-workload-type"></a>检索针对某个工作负荷类型的策略

以下示例检索针对工作负荷类型 **AzureFiles** 的策略。

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

输出如下所示。

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell 中“BackupTime”字段的时区是通用协调时间 (UTC)  。 在 Azure 门户中显示备份时间时，该时间会根据本地时区进行调整。

### <a name="retrieve-a-specific-policy"></a>检索特定的策略

以下策略检索名为“dailyafs”的备份策略  。

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>启用备份并应用策略

使用 [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0) 启用保护。 将策略与保管库关联后，系统会根据策略计划触发备份。

以下示例使用策略 **dailyafs**，为存储帐户 **testStorageAcct** 中的 Azure 文件共享 **testAzureFileShare** 启用保护。

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

此命令将等到保护作业配置完成为止，然后提供如下所示的输出。

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>触发按需备份

使用 [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) 针对受保护的 Azure 文件共享运行按需备份。

1. 使用 [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer) 从保管库中保存备份数据的容器中检索存储帐户。
2. 若要启动备份作业，请使用 [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem) 获取有关 VM 的信息。
3. 使用 [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem) 运行按需备份。

按如下所示运行按需备份：
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

该命令返回一个要跟踪的、带有 ID 的作业，如以下示例所示。

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

创建备份时使用 Azure 文件共享快照，因此通常作业在命令返回此输出时完成。

### <a name="using-on-demand-backups-to-extend-retention"></a>使用按需备份延长保留期

可以使用按需备份将快照保留 10 年。 计划程序可用于运行可选择保留期的按需 PowerShell 脚本，从而每周、每月或每年定期拍摄快照。 拍摄定期快照时，请参阅[使用 Azure 备份执行按需备份的限制](/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share)。

如果你正在寻找示例脚本，可以参考 GitHub 上的示例脚本 (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>)，该脚本使用 Azure 自动化 Runbook，可让你定期计划备份，甚至最多保留 10 年。

> [!WARNING]
> 确保将 PS 版本升级到“Az.RecoveryServices 2.6.0”的最低版本以用于自动化 runbook 中的 AFS 备份。 必须将旧的“AzureRM”模块替换为“Az”模块。 在此版本中，```Get-AzRecoveryServicesBackupItem``` 命令可使用“friendlyName”筛选器。 请将 Azure 文件共享名称传递给 friendlyName 参数。 如果你将 Azure 文件共享名称传递给“Name”参数，则此版本将引发一个警告来要求将此友好名称传递给 friendlyName 参数。


