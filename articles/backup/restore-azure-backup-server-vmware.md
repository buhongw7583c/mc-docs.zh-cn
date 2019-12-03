---
title: 使用 Azure 备份服务器还原 VMware VM
description: 使用 Azure 备份服务器 (MABS) 还原 VMware vCenter/ESXi 服务器上运行的 VMware VM。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: dacurwin
ms.openlocfilehash: a51b16fce6f304a533a394e39daf240fd9b73de5
ms.sourcegitcommit: 3a9c13eb4b4bcddd1eabca22507476fb34f89405
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74528593"
---
# <a name="restore-vmware-virtual-machines"></a>还原 VMware 虚拟机

本文介绍如何使用 Microsoft Azure 备份服务器 (MABS) 还原 VMware VM 恢复点。 有关使用 MABS 恢复数据的概述，请参阅[恢复受保护的数据](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)。 在 MABS 管理员控制台中，可通过两种方式查找可恢复的数据 - 搜索或浏览。 恢复数据时，你不一定想要将数据或 VM 还原到相同的位置。 出于此原因，MABS 支持对 VMware VM 备份使用三种恢复选项：

* **原始位置恢复 (OLR)** - 使用 OLR 可将受保护的 VM 还原到其原始位置。 仅当自备份以来未添加或删除磁盘时，才可以将 VM 还原到其原始位置。 如果已添加或删除磁盘，则必须使用备用位置恢复。

* **备用位置恢复 (ALR)** - 如果原始 VM 缺失，或者你不想要扰乱原始 VM，可将 VM 恢复到备用位置。 若要将 VM 恢复到备用位置，必须提供 ESXi 主机、资源池、文件夹以及存储数据存储和路径的位置。 为了帮助区分已还原的 VM 与原始 VM，MABS 会将“-Recovered”追加到 VM 名称中。

* **单个文件位置恢复 (ILR)** - 如果受保护的 VM 是 Windows Server VM，则可以使用 MABS 的 ILR 功能恢复该 VM 中的单个文件/文件夹。 若要恢复单个文件，请参阅本文稍后所述的过程。

## <a name="restore-a-recovery-point"></a>还原恢复点

1. 在 MABS 管理员控制台中，单击“恢复”视图。

2. 使用“浏览”窗格进行浏览或筛选，以找到想要恢复的 VM。 选择 VM 或文件夹后，“以下对象的恢复点”窗格将显示可用的恢复点。

    ![可用的恢复点](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. 在“以下对象的恢复点”字段中，使用日历和下拉菜单来选择恢复点的创建日期。  以粗体显示的日历日期包含可用的恢复点。

4. 在工具功能区中，单击“恢复”打开“恢复向导”。  

    ![恢复向导 - 复查恢复选择](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. 单击“下一步”转到“指定恢复选项”屏幕。  

6. 在“指定恢复选项”屏幕上，若要启用网络带宽限制，请单击“修改”。   若要禁用网络限制，请单击“下一步”。  对于 VMware VM，此向导屏幕上没有其他可用选项。 如果选择修改网络带宽限制，请在“限制”对话框中，选择“启用网络带宽用量限制”，以启用此功能。  启用后，配置“设置”和“工作计划”。  

7. 在“选择恢复类型”屏幕上，选择是要恢复到原始实例还是新位置，然后单击“下一步”。  

     * 如果选择“恢复到原始实例”，则不需要在向导中进行任何其他选择。  将使用原始实例的数据。

     * 如果选择“在任何主机上作为虚拟机恢复”，请在“指定目标”屏幕上，提供“ESXi 主机”、“资源池”、“文件夹”和“路径”的信息。    

      ![选择恢复类型](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. 在“摘要”屏幕上检查设置，然后单击“恢复”启动恢复过程。   “恢复状态”屏幕将显示恢复操作的进度。 

## <a name="restore-an-individual-file-from-a-vm"></a>从 VM 还原单个文件

可以从受保护 VM 的恢复点还原单个文件。 此功能仅适用于 Windows Server VM。 还原单个文件的步骤与还原整个 VM 类似，只是在启动恢复过程之前，需要浏览到 VMDK 并找到所需的文件。 若要从 Windows Server VM 恢复单个文件或选择文件：

1. 在 MABS 管理员控制台中，单击“恢复”视图。 

2. 使用“浏览”窗格进行浏览或筛选，以找到想要恢复的 VM。  选择 VM 或文件夹后，“以下对象的恢复点”窗格将显示可用的恢复点。

    ![可用的恢复点](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. 在“以下对象的恢复点:”窗格中，使用日历选择包含所需恢复点的日期。  根据备份策略的配置方式，日期中可以包含多个恢复点。 选择恢复点的创建日期后，请确保选择正确的“恢复时间”。  如果所选日期包含多个恢复点，请在“恢复时间”下拉菜单中选择所需的恢复点。 选择恢复点后，可恢复的项列表将显示在“路径:”窗格中。 

4. 若要查找想要恢复的文件，请在“路径”窗格中，双击“可恢复的项”列中的项以将其打开。   选择要恢复的文件或文件夹。 若要选择多个项，请在按住 **Ctrl** 键的同时选择每个项。 使用“路径”窗格搜索“可恢复的项”列中显示的文件或文件夹列表。   使用“搜索以下列表”不会在子文件夹中搜索。  若要在子文件夹中搜索，请双击该文件夹。 使用“向上”箭头按钮可从子文件夹切换到父文件夹。  可以选择多个项（文件和文件夹），但它们必须位于同一个父文件夹中。 不能在同一个恢复作业中恢复多个文件夹中的项。

5. 选择要恢复的项后，在管理员控制台工具功能区中，单击“恢复”打开“恢复向导”。   在恢复向导中，“复查恢复选择”屏幕将显示要恢复的选定项。 
    ![复查恢复选择](./media/restore-azure-backup-server-vmware/review-recovery.png)

6. 在“指定恢复选项”屏幕上，若要启用网络带宽限制，请单击“修改”。   若要禁用网络限制，请单击“下一步”。  对于 VMware VM，此向导屏幕上没有其他可用选项。 如果选择修改网络带宽限制，请在“限制”对话框中，选择“启用网络带宽用量限制”，以启用此功能。  启用后，配置“设置”和“工作计划”。  
7. 在“选择恢复类型”屏幕上，单击“下一步”。   只能将文件或文件夹恢复到网络文件夹。
8. 在“指定目标”屏幕上，单击“浏览”找到用于保存文件或文件夹的网络位置。   MABS 将创建一个文件夹，所有已恢复的项将复制到其中。 该文件夹的名称前缀为 MABS_day-month-year。 选择恢复的文件或文件夹的保存位置时，系统会提供该位置的详细信息（目标、目标路径和可用空间）。

       ![Specify location to recover files](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. 在“指定恢复选项”屏幕上，选择要应用的安全设置。  可以选择修改网络带宽用量限制，但默认已禁用限制。 “SAN 恢复”和“通知”也未启用。  
10. 在“摘要”屏幕上检查设置，然后单击“恢复”启动恢复过程。   “恢复状态”屏幕将显示恢复操作的进度。 

## <a name="next-steps"></a>后续步骤

若要排查在使用 Azure 备份服务器时出现的问题，请查看 [Azure 备份服务器的故障排除指南](./backup-azure-mabs-troubleshoot.md)。