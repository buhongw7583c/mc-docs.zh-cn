---
title: 创建基于路由的 VPN 网关：门户
titleSuffix: Azure VPN Gateway
description: 使用 Azure 门户创建基于路由的 VPN 网关
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: article
origin.date: 03/03/2020
ms.date: 04/06/2020
ms.author: v-jay
ms.openlocfilehash: 3e84c334bb37fdef16b55be054d2bad15e892a65
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "80634627"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>使用 Azure 门户创建基于路由的 VPN 网关

本文可帮助你使用 Azure 门户快速创建基于路由的 Azure VPN 网关。  创建与本地网络的 VPN 连接时使用 VPN 网关。 还可以使用 VPN 网关连接 VNet。 

本文中的步骤将创建 VNet、子网、网关子网和基于路由的 VPN 网关（虚拟网络网关）。 完成网关创建后，可以创建连接。 执行这些步骤需要 Azure 订阅。 如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial/?WT.mc_id=A261C142F)。

## <a name="create-a-virtual-network"></a><a name="vnet"></a>创建虚拟网络

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>配置和创建网关

在此步骤中，为 VNet 创建虚拟网络网关。 创建网关通常需要 45 分钟或更长的时间，具体取决于所选的网关 SKU。

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>基本网关 SKU 不支持 IKEv2 或 RADIUS 身份验证。 如果计划将 Mac 客户端连接到虚拟网络，请不要使用基本 SKU。

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>查看 VPN 网关

1. 创建网关后，请在门户中导航到 VNet1。 VPN 网关将作为已连接的设备显示在概述页上。

   ![连接的设备](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "已连接的设备")

2. 在设备列表中，单击 **VNet1GW** 可查看详细信息。

   ![查看 VPN 网关](./media/create-routebased-vpn-gateway-portal/view-gateway.png "查看 VPN 网关")

## <a name="next-steps"></a>后续步骤

完成创建网关后，可以创建虚拟网络与另一个 VNet 之间的连接。 或者，创建虚拟网络与本地位置之间的连接。

> [!div class="nextstepaction"]
> [创建站点到站点连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [创建点到站点连接](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [创建与另一个 VNet 的连接](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
