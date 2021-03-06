---
title: 在 Azure Stack Hub 中创建存储帐户
titleSuffix: Azure Stack Hub
description: 了解如何在 Azure Stack Hub 中创建存储帐户。
author: WenJason
ms.topic: conceptual
origin.date: 1/22/2020
ms.date: 02/24/2020
ms.author: v-jay
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 2328eff7e51cc6acfa3e55d7cb38c3fb9ac31ce8
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "77540365"
---
# <a name="create-storage-accounts-in-azure-stack-hub"></a>在 Azure Stack Hub 中创建存储帐户

Azure Stack Hub 中的存储帐户包括 Blob 和表服务，以及存储数据对象的唯一命名空间。 默认情况下，只有你，即存储帐户所有者，才能使用帐户中的数据。

1. 在 Azure Stack Hub POC 计算机上，以`https://adminportal.local.azurestack.external`管理员[身份登录到 ](../asdk/asdk-connect.md)，然后单击“+ 创建资源” **“数据 + 存储”** “存储帐户”。 >    >  

   ![在 Azure Stack Hub 管理员门户中创建存储帐户](media/azure-stack-provision-storage-account/image01.png)

2. 在“创建存储帐户”  边栏选项卡中，键入存储帐户的名称。 创建新**资源组**或选择现有的资源组，然后单击“创建”以创建存储帐户。 

   ![在 Azure Stack Hub 管理员门户中查看存储帐户](media/azure-stack-provision-storage-account/image02.png)

3. 若要查看新存储帐户，请单击“所有资源”，然后搜索该存储帐户并单击其名称。 

    ![Azure Stack Hub 管理员门户中的存储帐户名称](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>后续步骤

- [使用 Azure 资源管理器模板](../user/azure-stack-arm-templates.md)
- [了解 Azure 存储帐户](/storage/common/storage-create-storage-account)
- [下载与 Azure 一致的 Azure Stack Hub 存储验证指南](https://aka.ms/azurestacktp1doc)
