---
title: Azure VPN 网关：为 P2S VPN 客户端播发自定义路由
description: 向点到站点客户端播发自定义路由的步骤
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: article
origin.date: 11/11/2019
ms.date: 12/02/2019
ms.author: v-jay
ms.openlocfilehash: 07a611f4b2d6ea61804b090bd3893c80d311485a
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "74552975"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>为 P2S VPN 客户端播发自定义路由

你可能想要向所有点到站点 VPN 客户端播发自定义路由。 例如，当你在 VNet 中启用了存储终结点并希望远程用户能够通过 VPN 连接访问这些存储帐户时。 可以向所有远程用户播发存储终结点的 IP 地址，以使到存储帐户的流量通过 VPN 隧道，而不是公共 Internet。

![Azure VPN 网关多站点连接示例](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>播发自定义路由

若要播发自定义路由，请使用 `Set-AzVirtualNetworkGateway cmdlet`。 以下示例演示如何播发 [Contoso 存储帐户表](https://contoso.table.core.chinacloudapi.cn)的 IP。

1. Ping contoso.table.core.chinacloudapi.cn  并记录 IP 地址。 例如：

    ```cmd
    C:\>ping contoso.table.core.chinacloudapi.cn
    Pinging table.by4prdstr05a.store.core.chinacloudapi.cn [13.88.144.250] with 32 bytes of data:
    ```

2. 运行以下 PowerShell 命令：

    ```azurepowershell
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. 若要添加多个自定义路由，请使用逗号和空格分隔地址。 例如：

    ```azurepowershell
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>查看自定义路由

使用以下示例查看自定义路由：

  ```azurepowershell
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="to-delete-custom-routes"></a>删除自定义路由

使用以下示例删除自定义路由：

  ```azurepowershell
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>后续步骤

有关其他 P2S 路由信息，请参阅[关于点到站点路由](vpn-gateway-about-point-to-site-routing.md)。
