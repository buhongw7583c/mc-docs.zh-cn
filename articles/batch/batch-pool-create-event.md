---
title: Azure Batch 池创建事件
description: Batch 池创建事件（在池创建后发出）的参考。 日志内容将公开有关池的常规信息。
services: batch
author: lingliw
manager: digimobile
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 11/26/2018
ms.author: v-lingwu
ms.openlocfilehash: f8be652bed2c03fec2c0ac8ce5e777df1301dc2a
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "77497387"
---
# <a name="pool-create-event"></a>池创建事件

 池创建后，会发出此事件。 日志内容将公开有关池的常规信息。 请注意，如果池的目标大小大于 0 个计算节点，则池调整大小启动事件会在此事件之后立即发生。

 以下示例显示了使用 `CloudServiceConfiguration` 属性所创建池的池创建事件的正文。

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|元素|类型|说明|
|-------------|----------|-----------|
|`id`|String|池的 ID。|
|`displayName`|String|池的显示名称。|
|`vmSize`|String|池中虚拟机的大小。 池中所有虚拟机的大小相同。 <br/><br/> 有关云服务池（使用 cloudServiceConfiguration 创建的池）的虚拟机可用大小的信息，请参阅[云服务的大小](/cloud-services/cloud-services-sizes-specs)。 批处理支持除 `ExtraSmall` 以外的所有云服务 VM 大小。<br/><br/> 有关使用虚拟机市场中映像的池（使用 virtualMachineConfiguration 创建的池）的可用 VM 大小的信息，请参阅[虚拟机的大小](https://docs.azure.cn/zh-cn/virtual-machines/linux/sizes?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json) (Linux) 或[虚拟机的大小](https://docs.azure.cn/zh-cn/virtual-machines/windows/sizes?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) (Windows)。 Batch 支持除 `STANDARD_A0` 和高级存储大小（`STANDARD_GS`、`STANDARD_DS` 和 `STANDARD_DSV2` 系列）以外所有的 Azure VM 大小。|
|`imageType`|String|映像的部署方法。 支持的值为 `virtualMachineConfiguration` 或 `cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|复杂类型|池的云服务配置。|
|[`virtualMachineConfiguration`](#bk_vmconf)|复杂类型|池的虚拟机配置。|
|[`networkConfiguration`](#bk_netconf)|复杂类型|池的网络配置。|
|`resizeTimeout`|时间|为针对池的上一次调整大小操作指定的将计算节点分配到池的超时。  （创建池时的初始大小计为一次调整大小。）|
|`targetDedicatedNodes`|Int32|池请求的专用计算节点数。|
|`targetLowPriorityNodes`|Int32|池请求的低优先级计算节点数。|
|`enableAutoScale`|Bool|指定池大小是否随时间自动调整。|
|`enableInterNodeCommunication`|Bool|指定是否针对节点间的直接通信设置池。|
|`isAutoPool`|Bool|指定是否已通过作业的 AutoPool 机制创建池。|
|`maxTasksPerNode`|Int32|可在池中单个计算节点上并发运行的任务的最大数目。|
|`vmFillType`|String|定义批处理服务如何在池中不同的计算节点之间分配任务。 有效值为 Spread 或 Pack。|

<a name="bk_csconf"></a>
###  <a name="cloudserviceconfiguration"></a>cloudServiceConfiguration

|元素名称|类型|说明|
|------------------|----------|-----------|
|`osFamily`|String|要安装在池中虚拟机上的 Azure 来宾 OS 系列。<br /><br /> 可能的值包括：<br /><br /> **2**  OS 系列 2，等效于 Windows Server 2008 R2 SP1。<br /><br /> **3**  OS 系列 3，等效于Windows Server 2012。<br /><br /> **4**  OS 系列 4，等效于 Windows Server 2012 R2。<br /><br /> 有关详细信息，请参阅 [Azure 来宾 OS 版本](/cloud-services/cloud-services-guestos-update-matrix#releases)。|
|`targetOSVersion`|String|要安装在池中虚拟机上的 Azure 来宾 OS 版本。<br /><br /> 默认值为 **\*** ，用于指定特定系列的最新操作系统版本。<br /><br /> 有关其他允许的值的信息，请参阅 [Azure 来宾 OS 版本](/cloud-services/cloud-services-guestos-update-matrix#releases)。|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a> virtualMachineConfiguration

|元素名称|类型|说明|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|复杂类型|指定关于要使用的平台或市场映像的信息。|
|`nodeAgentId`|String|在计算节点上预配的批处理节点代理的 SKU。|
|[`windowsConfiguration`](#bk_winconf)|复杂类型|指定虚拟机上的 Windows 操作系统设置。 如果 imageReference 引用 Linux OS 映像，则不能指定此属性。|

###  <a name="imagereference"></a><a name="bk_imgref"></a> imageReference

|元素名称|类型|说明|
|------------------|----------|-----------|
|`publisher`|String|映像的发布者。|
|`offer`|String|映像的产品/服务。|
|`sku`|String|映像的 SKU。|
|`version`|String|映像的版本。|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a> windowsConfiguration

|元素名称|类型|说明|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Boolean|指示是否对虚拟机启用自动更新。 如果未指定此属性，则默认值为 true。|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a> networkConfiguration

|元素名称|类型|说明|
|------------------|--------------|----------|
|`subnetId`|String|指定创建池计算节点的子网的资源标识符。|

<!-- Update_Description: update metedata properties -->