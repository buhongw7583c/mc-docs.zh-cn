---
title: 下载用于 S2S VPN 连接的 VPN 设备配置脚本：Azure 资源管理器 | Microsoft Docs
description: 本文逐步讲解如何使用 Azure 资源管理器下载采用 Azure VPN 网关的 S2S VPN 连接的 VPN 设备配置脚本。
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 03/29/2018
ms.date: 05/08/2018
ms.author: v-junlch
ms.openlocfilehash: a72c1f70f428be4dff9fdd6708f8ca482f6526a9
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52661832"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>下载用于 S2S VPN 连接的 VPN 设备配置脚本

本文逐步讲解如何使用 Azure 资源管理器下载采用 Azure VPN 网关的 S2S VPN 连接的 VPN 设备配置脚本。 下图展示了概要工作流。

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

以下设备具有可用的脚本：

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about"></a>关于 VPN 设备配置脚本

跨界 VPN 连接包含一个 Azure VPN 网关、一个本地 VPN 设备，以及将两者相连接的 IPsec S2S VPN 隧道。 典型的工作流包括以下步骤：

1. 创建并配置 Azure VPN 网关（虚拟网络网关）
2. 创建并配置代表本地网络和 VPN 设备的 Azure 本地网络网关
3. 在 Azure VPN 网关与本地网络网关之间创建并配置 Azure VPN 连接
4. 配置本地网络网关代表的本地 VPN 设备，以便与 Azure VPN 网关建立实际 S2S VPN 隧道

可以使用 Azure [门户](vpn-gateway-howto-site-to-site-resource-manager-portal.md)、[PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) 或 [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md) 完成步骤 1 到 3。 最后一个步骤涉及到在 Azure 外部配置本地 VPN 设备。 使用此功能可下载 VPN 设备的配置脚本，其中已填写 Azure VPN 网关、虚拟网络、本地网络地址前缀和 VPN 连接属性等的相应值。 可以使用此脚本作为起点，或者通过配置控制台将它直接应用到本地 VPN 设备。

> [!IMPORTANT]
> * 每个 VPN 设备配置脚本的语法各不相同，在很大程度上取决于型号和固件版本。 请特别注意设备型号和版本信息是否与可用的模板相符。
> * 某些参数值在设备上必须唯一，在不访问设备的情况下无法确定。 Azure 生成的配置脚本会预先填充这些值，但你需要确保提供的值在设备上有效。 例如：
>    * 接口编号
>    * 访问控制列表编号
>    * 策略名称或编号，等等。
> * 在应用该脚本之前，请查找脚本中嵌入的关键字“**REPLACE**”，找到需要验证的参数。
> * 某些模板包含“**CLEANUP**”节，应用此节可以删除配置。 cleanup 节默认已注释掉。

## <a name="download-the-configuration-script-from-azure-portal"></a>从 Azure 门户下载配置脚本

创建 Azure VPN 网关、本地网络网关，以及连接两者的连接资源。 以下页面将引导你完成每个步骤：

- [在 Azure 门户中创建站点到站点连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

创建连接资源后，遵照以下说明下载 VPN 设备配置脚本：

1. 在浏览器中导航到 [Azure 门户](http://portal.azure.cn)，并根据需要使用 Azure 帐户登录。
2. 转到创建的连接资源。 依次单击“所有服务”、“网络”和“连接”可以找到所有连接资源的列表。

    ![connection-list](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. 单击要配置的连接。

    ![connection-overview](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. 单击“连接概述”页中以红色突出显示的“下载配置”链接，打开“下载配置”页。

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. 选择 VPN 设备的型号系列和固件版本，然后单击“下载配置”按钮。

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. 浏览器会提示是否保存下载的脚本（文本文件）。
7. 下载配置脚本后，使用文本编辑器将它打开，搜索关键字“REPLACE”来识别并检查可能需要替换的参数。

    ![edit-script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>使用 Azure PowerShell 下载配置脚本

也可以使用 Azure PowerShell 下载配置脚本，如以下示例中所示：

```powershell
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"
```

# <a name="list-the-available-vpn-device-models-and-versions"></a>列出可用的 VPN 设备型号和版本
Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# <a name="download-the-configuration-script-for-the-connection"></a>下载适用于连接的配置脚本
Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## Apply the configuration script to your VPN device

After you have downloaded and validated the configuration script, the next step is to apply the script to your VPN device. The actual procedure varies based on your VPN device makes and models. Consult the operation manuals or the instruction pages for your VPN devices.

## Next steps

Continue configuring your [Site-to-Site connection](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

<!-- Update_Description: wording update -->