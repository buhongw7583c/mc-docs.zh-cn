---
title: 将 Kubernetes 添加到 Azure Stack 市场 | Microsoft Docs
description: 了解如何将 Kubernetes 添加到 Azure Stack 市场。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 10/08/2018
ms.date: 11/12/2018
ms.author: v-jay
ms.reviewer: waltero
ms.openlocfilehash: 553f02333908f47559580be814a60650be3d5573
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52660763"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>将 Kubernetes 添加到 Azure Stack 市场

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!note]  
> Azure Stack 上的 Kubernetes 现为预览版。

可以将 Kubernetes 作为市场项提供给用户。 用户可以通过单个协调的操作部署 Kubernetes。

下面的文章着眼于使用 Azure 资源管理器模板为独立的 Kubernetes 群集部署和预配资源。 Kubernetes 群集市场项 0.3.0 需要 Azure Stack 版本 1808。 在开始之前，请检查 Azure Stack 和全球 Azure 租户设置。 收集关于 Azure Stack 的必需信息。 将所需资源添加到租户和 Azure Stack 市场。 群集依赖于 Ubuntu 服务器、自定义脚本以及要放置在市场中的 Kubernetes 项。

## <a name="create-a-plan-an-offer-and-a-subscription"></a>创建计划、套餐和订阅

为 Kubernetes 市场项创建计划、套餐和订阅。 也可以使用现有计划和套餐。

1. 登录到[管理门户](https://adminportal.local.azurestack.external)。

2. 创建一个计划作为基本计划。 有关说明，请参阅[在 Azure Stack 中创建计划](azure-stack-create-plan.md)。

3. 创建套餐。 有关说明，请参阅[在 Azure Stack 中创建套餐](azure-stack-create-offer.md)。

4. 选择“套餐”，并找到你创建的套餐。

5. 选择“套餐”边栏选项卡中的“概述”。

6. 选择“更改状态”。 选择“公共”。

7. 选择“+ 创建资源” > “套餐和计划” > “订阅”来创建新订阅。

    a. 输入**显示名称**。

    b. 输入**用户**。 请使用与你的租户关联的 Azure AD 帐户。

    c. **提供商说明**

    d. 将“目录租户”设置为你的 Azure Stack 的 Azure AD 租户。 

    e. 选择“套餐”。 选择你创建的套餐的名称。 记下订阅 ID。

## <a name="add-an-ubuntu-server-image"></a>添加 Ubuntu 服务器映像

将以下 Ubuntu 服务器映像添加到市场：

1. 登录到[管理门户](https://adminportal.local.azurestack.external)。

2. 选择“所有服务”，然后在“管理”类别下，选择“市场管理”。

3. 选择“+ 从 Azure 添加”。

4. 输入 `UbuntuServer`。

5. 使用以下配置文件选择最新版本的服务器：
    - **发布者**：Canonical
    - **套餐**：UbuntuServer
    - **SKU**：16.04-LTS

6. 选择“下载”。

## <a name="add-a-custom-script-for-linux"></a>添加适用于 Linux 的自定义脚本

从市场中添加 Kubernetes：

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

2. 选择“所有服务”，然后在“管理”类别下，选择“市场管理”。

3. 选择“+ 从 Azure 添加”。

4. 输入 `Custom Script for Linux`。

5. 选择具有以下配置文件的服务器：
    - **套餐**：适用于 Linux 2.0 的自定义脚本
    - **版本**：2.0.6
    - **发布者**：Microsoft Corp

    > [!Note]  
    > 可能会列出适用于 Linux 的自定义脚本的多个版本。 你需要添加匹配的版本。 Kubernetes 需要该项的确切版本。

6. 选择“下载”。


## <a name="add-kubernetes-to-the-marketplace"></a>将 Kubernetes 添加到市场

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

2. 选择“所有服务”，然后在“管理”类别下，选择“市场管理”。

3. 选择“+ 从 Azure 添加”。

4. 输入 `Kubernetes`。

5. 选择 `Kubernetes Cluster`。

6. 选择“下载”。

    > [!note]  
    > 市场项可能需要花费五分钟才会出现在市场中。

    ![kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>更新或删除 Kubernetes 

更新 Kubernetes 项时，需要删除市场中的项。 然后，可以按照本文中的说明将 Kubernetes 添加到市场。

若要删除 Kubernetes 项，请执行以下操作：

1. 以操作员身份使用 PowerShell 连接到 Azure Stack。 有关说明，请参阅[以操作员身份使用 PowerShell 连接到 Azure Stack](/azure-stack/azure-stack-powershell-configure-admin)。

2. 在库中查找当前的 Kubernetes 群集项。

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. 记下当前项的名称，例如 `Microsoft.AzureStackKubernetesCluster.0.2.0`

4. 使用以下 PowerShell cmdlet 删除项：

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[在 Azure Stack 中提供服务概述](azure-stack-offer-services-overview.md)
<!-- Update_Description: update metedata properties -->