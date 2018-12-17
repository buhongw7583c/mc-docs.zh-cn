---
title: 设置源环境以便使用 Azure Site Recovery 从 VMware 复制到 Azure | Azure
description: 本文介绍如何设置本地环境，以便使用 Azure Site Recovery 将 VMware VM 复制到 Azure。
services: site-recovery
author: rockboyfor
manager: digimobile
ms.service: site-recovery
ms.topic: article
origin.date: 07/06/2018
ms.date: 07/23/2018
ms.author: v-yeche
ms.openlocfilehash: 34ad8aff9245cb3c491aef6b35fd4ffbbc358062
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52648913"
---
<a name="set-up-the-source-environment"></a>
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>设置源环境以便从 VMware 复制到 Azure

本文介绍如何设置源本地环境，以便将 VMware VM 复制到 Azure。 它包括以下相关步骤：选择复制方案、将本地计算机设置为 Site Recovery 配置服务器和自动发现本地 VM。 

## <a name="prerequisites"></a>先决条件

本文假设已完成以下操作：
- 在 [Azure 门户](http://portal.azure.cn)中[设置资源](tutorial-prepare-azure.md)。
- [设置本地 VMware](vmware-azure-tutorial-prepare-on-premises.md)，包括用于自动发现的专用帐户。

## <a name="choose-your-protection-goals"></a>选择保护目标

1. 在 Azure 门户中，浏览到“恢复服务保管库”边栏选项卡，并选择保管库。
2. 在保管库的资源菜单中，转到“开始使用” > “Site Recovery” > “步骤 1: 准备基础结构” > “保护目标”。

    ![选择目标](./media/vmware-azure-set-up-source/choose-goals.png)
3. 在“保护目标”中选择“到 Azure”，并选择“是，使用 VMware vSphere 虚拟机监控程序”。 。

    ![选择目标](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>设置配置服务器

可以通过开放虚拟化应用程序 (OVA) 模板将配置服务器设置为本地 VMware VM。 [详细了解](concepts-vmware-to-azure-architecture.md)将在 VMware VM 上安装的组件。

1. 了解配置服务器部署的[先决条件](vmware-azure-deploy-configuration-server.md#prerequisites)。
2. [检查容量数字](vmware-azure-deploy-configuration-server.md#capacity-planning)以便进行部署。
3. [下载](vmware-azure-deploy-configuration-server.md#download-the-template)并[导入](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) OVA 模板，设置运行配置服务器的本地 VMware VM。 通过模板提供的许可证是评估许可证，有效期为 180 天。 超过有效期之后，客户需要使用购买的许可证来激活 Windows。
4. 打开 VMware VM，并将其[注册](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services)到恢复服务保管库中。

## <a name="add-the-vmware-account-for-automatic-discovery"></a>添加用于自动发现的 VMware 帐户

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>连接到 VMware 服务器

为使 Azure Site Recovery 能够发现本地环境中运行的虚拟机，需要将 VMware vCenter 服务器或 vSphere ESXi 主机连接到 Site Recovery。

选择“+vCenter”开始连接 VMware vCenter 服务器或 VMware vSphere ESXi 主机。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="common-issues"></a>常见问题
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>后续步骤
在 Azure 中[设置目标环境](./vmware-azure-set-up-target.md)。

<!--Update_Description: update meta properties, wording update  -->