---
title: Azure Stack 1902 更新 | Microsoft Docs
description: 了解 Azure Stack 集成系统的 1902 更新，包括新增功能、已知问题和更新下载位置。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimonbile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/07/2019
ms.date: 03/18/2019
ms.author: v-jay
ms.reviewer: adepue
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 96425edb65d775e41f8c6677a9ad7964efeeae72
ms.sourcegitcommit: 66e360fe2577c9b7ddd96ff78e0ede36c3593b99
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/15/2019
ms.locfileid: "57988574"
---
# <a name="azure-stack-1902-update"></a>Azure Stack 1902 更新

*适用于：Azure Stack 集成系统*

本文介绍 1902 更新包的内容。 该更新包含此版 Azure Stack 的改进、修复和新功能。 本文还描述了此版本中的已知问题，并包含一个用于下载该更新的链接。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1902 更新内部版本号为 **1.1902.0.69**。

## <a name="hotfixes"></a>修补程序

Azure Stack 定期发布修补程序。 将 Azure Stack 更新到 1902 之前，请务必先安装 1901 的[最新 Azure Stack 修补程序](#azure-stack-hotfixes)。

Azure Stack 修补程序仅适用于 Azure Stack 集成系统；请勿尝试在 ASDK 上安装修补程序。

> [!TIP]  
> 订阅下述 *RSS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新信息：
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack 修补程序

- **1809**：[KB 4481548 – Azure Stack 修补程序 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**：当前没有修补程序可用。
- **1901**：当前没有修补程序可用。
- **1902**：当前没有修补程序可用。

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
- 在更新到 1902 之前，请先安装 1901 的[最新 Azure Stack 修补程序](#azure-stack-hotfixes)（如果有）。

- 在开始安装此更新之前，请使用以下参数运行 [Test-AzureStack](azure-stack-diagnostic-test.md)，以验证 Azure Stack 的状态并解决发现的所有操作问题，包括所有警告和故障。 另外，请查看活动警报，并解决所有需要采取措施的警报。

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- 通过 System Center Operations Manager (SCOM) 管理 Azure Stack 时，请务必在应用 1902 之前将[适用于 Azure Stack 的管理包](https://www.microsoft.com/download/details.aspx?id=55184)更新到版本 10.0.3.11。

- 从版本 1902 开始，Azure Stack 更新包的格式已从 **.bin/.exe/.xml** 更改为 **.zip/.xml**。 使用联网 Azure Stack 缩放单元的客户将在门户中看到“有可用更新”消息。 未建立连接的客户现在只需下载并导入包含相应 .xml 的 .zip 文件即可。

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="changes"></a>更改

- 1902 版本在 Azure Stack 管理员门户上引入了新的用户界面用于创建计划、套餐、配额和附加计划。 有关详细信息（包括屏幕截图），请参阅[创建计划、套餐和配额](azure-stack-create-plan.md)。

## <a name="common-vulnerabilities-and-exposures"></a>常见漏洞和风险

此更新安装以下安全更新：  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)


有关这些漏洞的详细信息，请单击上述链接，或者查看 Microsoft 知识库文章 [4487006](https://support.microsoft.com/en-us/help/4487006)。

## <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 运行 [Test-AzureStack](azure-stack-diagnostic-test.md) 时，会显示基板管理控制器 (BMC) 中的一条警告消息。 可以放心地忽略此警告。

- <!-- 2468613 - IS --> 在安装此更新的过程中，可能会看到标题为 `Error – Template for FaultType UserAccounts.New is missing.` 的警报。可以放心忽略这些警报。 完成此更新的安装后，这些警报会自动关闭。

## <a name="post-update-steps"></a>更新后步骤

- 安装此更新之后，请安装所有适用的修补程序。 有关详细信息，请参阅[修补程序](#hotfixes)以及我们的[服务策略](azure-stack-servicing-policy.md)。  

- 检索静态数据加密密钥，并将其安全存储在 Azure Stack 部署的外部。 请遵照[有关如何检索密钥的说明](azure-stack-security-bitlocker.md)操作。

## <a name="known-issues-post-installation"></a>已知问题（安装后）

下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户

<!-- 2930820 - IS ASDK --> 
- 在管理员门户和用户门户中，如果搜索“Docker”，则此项无法正确返回。 它在 Azure Stack 中不可用。 如果尝试创建它，则会显示一个边栏选项卡，其中包含表明存在错误的内容。 

<!-- 2931230 – IS  ASDK --> 
- 即使从用户订阅中删除计划，也无法删除作为附加计划添加到用户订阅的计划。 该计划将一直保留，直到引用附加计划的订阅也被删除。 

<!-- TBD - IS ASDK --> 
- 不应使用版本 1804 中引入的两种管理订阅类型。 这两种订阅类型为“计量订阅”和“消耗订阅”。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

<!-- 3557860 - IS ASDK --> 
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

<!-- 1663805 - IS ASDK --> 
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是[使用 PowerShell 验证权限](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan)。

<!-- ### Health and monitoring -->

### <a name="compute"></a>计算

- 创建新的 Windows 虚拟机 (VM) 时，可能会显示以下错误：

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   如果在 VM 上启用了启动诊断，但删除了启动诊断存储帐户，则会发生该错误。 若要解决此问题，请使用以前所用的同一名称重新创建存储帐户。

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- 虚拟机规模集 (VMSS) 创建体验提供基于 CentOS 的 7.2 作为部署选项。 由于该映像在 Azure Stack 上不可用，因此请为部署选择另一操作系统，或者使用一个 Azure 资源管理器模板，指定另一个已在部署之前由操作员从市场下载的 CentOS 映像。  

<!-- TBD - IS ASDK --> 
- 应用 1902 更新后，在部署包含托管磁盘的 VM 时可能会遇到以下问题：

   - 如果订阅是在 1808 更新之前创建的，则部署具有托管磁盘的 VM 可能会失败并出现内部错误消息。 若要解决此错误，请针对每个订阅执行以下步骤：
      1. 在租户门户中转到“订阅”，找到相应订阅。 依次选择“资源提供程序”、“Microsoft.Compute”、“重新注册”。
      2. 在同一订阅下，转到“访问控制(标识和访问管理)”，验证“Azure Stack - 托管磁盘”是否已列出。
   - 如果已配置多租户环境，在与来宾目录相关联的订阅中部署 VM 可能会失败并出现内部错误消息。 若要解决错误，请执行[此文章](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)中的步骤来重新配置每个来宾目录。

- 如果使用创建时已启用 SSH 授权的 Ubuntu 18.04 VM，则无法使用 SSH 密钥登录。 若要解决此问题，请在预配后使用针对 Linux 扩展的 VM 访问权限来实现 SSH 密钥，或者使用基于密码的身份验证。

- 在版本 1902 中，ERCS 基础结构 VM 所需的内存已从 8 GB 增大至 12 GB。 在 ASDK 中，这会导致所需内存增大 4 GB。 在 Azure Stack 集成系统安装中，所需内存会增大 12 GB。

   为了确保修补升级过程尽可能地减少租户停机时间，请在“容量”边栏选项卡中确认 Azure Stack 阵列是否可提供 12 GB 以上的可用空间。 成功安装更新后，“容量”边栏选项卡中会反映内存增大。

### <a name="networking"></a>网络  

<!-- 3239127 - IS, ASDK -->
- 在 Azure Stack 门户中，对于已附加到 VM 实例的网络适配器，在更改与其绑定的 IP 配置的静态 IP 地址时，会看到一条警告消息，其中指出 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`。

    可以放心忽略此消息；即使 VM 实例未重启，IP 地址也会更改。

<!-- 3632798 - IS, ASDK -->
- 在门户中，如果添加入站安全规则并选择“服务标记”作为源，“服务标记”列表中会显示多个不适用于 Azure Stack 的选项。 在 Azure Stack 中有效的选项仅限以下几个：

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  在 Azure Stack 中，不支持将其他选项用作源标记。 同样，如果添加出站安全规则并选择“服务标记”作为目标，则显示与“源标记”相同的选项列表。 仅有的有效选项与“源标记”的有效选项相同，如以上列表中所述。

- 网络安全组 (NSG) 无法像在全球 Azure 中一样在 Azure Stack 中运行。 在 Azure 中，可以在一个 NSG 规则中设置多个端口（使用门户、PowerShell 和资源管理器模板）。 但是，在 Azure Stack 中，无法通过门户在一个 NSG 规则中设置多个端口。 若要解决此问题，请使用资源管理器模板或 PowerShell 设置这些附加的规则。

<!-- 3203799 - IS, ASDK -->
- 目前，无论实例大小是什么，Azure Stack 都不支持将 4 个以上的网络接口 (NIC) 附加到 VM 实例。

- 我们已识别到以下问题：发往内部负载均衡器 (ILB) 的超过 1450 字节的数据包将被丢弃。 该问题的原因是主机上的 MTU 设置过小，无法容纳遍历角色的 VXLAN 封装数据包，自版本 1901 开始，该角色已移到主机。 在下面这种情况下，我们也发现此问题会自行显现：

  - SQL 查询发往内部负载均衡器 (ILB) 后面的 SQL Always On，并且超过 660 字节。

  在同一虚拟网络但不同子网中的 VM 与 ILB 之间进行通信时，将发生此问题。 在 ASDK 主机上权限提升的命令提示符下运行以下命令即可解决此问题：

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>应用服务

<!-- 2352906 - IS ASDK --> 
- 在订阅中创建第一个 Azure 函数之前，必须先注册存储资源提供程序。


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>下载更新

可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1902 更新包。 

只有在联网场景中，Azure Stack 部署才会定期检查安全的终结点，并在已发布云更新的情况下自动通知你。 有关详细信息，请参阅[管理 Azure Stack 的更新](azure-stack-updates.md#using-the-update-tile-to-manage-updates)。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。  