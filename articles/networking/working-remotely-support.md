---
title: 使用 Azure 网络服务远程进行工作
description: 本页介绍如何使用可用的 Azure 网络服务实现远程工作，以及如何缓解因远程工作者增多而出现的流量问题。
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
origin.date: 03/26/2020
ms.date: 03/30/2020
ms.author: v-tawe
ms.openlocfilehash: 1b4343ed050603a2c871a0ea2e4226b0a36548f7
ms.sourcegitcommit: 5fb45da006859215edc8211481f13174aa43dbeb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80634688"
---
# <a name="working-remotely-using-azure-networking-services"></a>使用 Azure 网络服务远程进行工作

>[!NOTE]
> 本文介绍如何利用 Azure 网络服务、Microsoft 网络和 Azure 合作伙伴生态系统远程进行工作，以及如何缓解因 COVID-19（新冠病毒）危机而面临的网络问题。

本文将介绍可供组织用来为用户设置远程访问，或者在使用高峰期通过附加容量补充现有解决方案的选项。 网络架构师面临着以下挑战：

- 解决由于网络使用率提高而导致的问题。
- 为公司和客户的更多员工提供安全可靠的连接。
- 在全球范围内提供与远程位置的连接。

在远程工作者负载高峰期，并非所有网络都会出现拥塞（例如，专用 WAN 和企业核心网络就不会出现拥塞）。 通常只有家庭宽带网络以及企业本地网络的 VPN 网关才会报告瓶颈。

网络规划人员可以通过记住不同的流量类型需要不同的网络处理优先级，以及通过使用某种智能负载重定向/分配方法，来帮助缓解瓶颈并减轻网络拥塞问题。 例如，医生与患者之间的实时远程医疗交互流量的重要性就很高，对网络延迟/波动的敏感性也很高。 而如果相同的流量用于存储之间的复制，则它对延迟就不敏感。 对于前一种流量，必须通过服务质量较高的最佳网络路径进行路由；而对于后一种流量，可以接受通过次优路径进行路由。

>[!NOTE] 
>本文的最后部分列出了几篇文章的链接，其中介绍了如何利用不同的 Azure 网络功能和生态系统来为远程工作用户提供支持。


## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>分享我们的最佳做法 - Azure 网络旨在提供弹性和高可用性

Azure 已设计为能够承受资源利用率的骤然变化，并可以在使用高峰期提供很大的帮助。 Azure 的网络已设计为提供高可用性，能够承受不同类型的故障：从单一网络要素的故障，到整个区域的故障。

Azure 网络已设计为符合各种要求，可为不同类型的网络流量（包括针对 Skype 和 Teams、CDN、实时大数据分析、Azure 存储、必应及 Xbox 的延迟敏感型多媒体流量）提供最佳性能。 为了针对不同类型的流量提供最佳性能，Azure 网络会将发往其资源的，或者要通过其资源传输的所有流量吸引到尽量靠近流量来源的位置。

>[!NOTE] 
>借助下面所述的 Azure 网络功能可以利用 Azure 网络的流量吸引行为来提供更好的客户网络体验。 Azure 网络的流量吸引行为有助于从第一英里/最后一英里的网络（这些网络在使用高峰期可能会遇到拥塞）中尽快减轻流量负担。
>

## <a name="enable-employees-to-work-remotely"></a>使员工能够远程工作

Azure VPN 网关支持点到站点 (P2S) 和站点到站点 (S2S) VPN 连接。 使用 Azure VPN 网关可以缩放员工的连接，使其能够安全访问 Azure 部署的资源和本地资源。 有关详细信息，请参阅[如何使用户能够远程工作](../vpn-gateway/work-remotely-support.md)。 

如果使用安全套接字隧道协议 (SSTP)，并发连接数将限制为 128。 若要获得更多的连接，我们建议转换到 OpenVPN 或 IKEv2。 有关详细信息，请参阅[从 SSTP 转换到 OpenVPN 协议或 IKEv2](../vpn-gateway/ikev2-openvpn-from-sstp.md)。

<!-- To access your resources deployed in Azure, remote developers could use Azure Bastion solution, instead of VPN connection to get secure shell access (RDP or SSH) without requiring public IPs on the VMs being accessed. For more information, see [Work remotely using Azure Bastion](https://go.microsoft.com/fwlink/?linkid=2123939). -->

若要聚合大规模 VPN 连接，以支持不同本地全局位置和不同区域性中心辐射型虚拟网络中的资源之间的任意点到任意点连接，并优化多个家庭宽带网络的利用率，可以使用 Azure 虚拟 WAN。 有关详细信息，请参阅[正在奋力满足在家工作的需求？Azure 虚拟 WAN 可以提供帮助](../virtual-wan/work-remotely-support.md)。

支持远程工作的另一种方式是部署 Azure 虚拟网络中托管的、通过 Azure 防火墙保护的虚拟桌面基础结构 (VDI)。 例如，Windows 虚拟桌面 (WVD) 是在 Azure 中运行的桌面和应用虚拟化服务。 使用 Windows 虚拟桌面，可以在 Azure 订阅中设置可缩放的灵活环境，而无需运行任何额外的网关服务器。 你只负责虚拟网络中的 WVD 虚拟机。 有关详细信息，请参阅 [Azure 防火墙远程工作支持](../firewall/remote-work-support.md)。 

Azure 还有众多的生态系统合作伙伴。 Azure 上的合作伙伴网络虚拟设备也能够帮助缩放 VPN 连接。 有关详细信息，请参阅[用于远程工作的网络虚拟设备 (NVA) 的注意事项](../vpn-gateway/nva-work-remotely-support.md)。

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>扩展员工的连接以访问全球分布的资源

员工可以借助以下 Azure 服务访问全球分布的资源。 资源可能位于任何 Azure 区域或本地网络中，甚至可能位于其他公有云或私有云中。 

- Azure 虚拟网络对等互连  ：如果将资源部署在多个 Azure 区域，并且/或者使用多个虚拟网络聚合远程工作员工的连接，则可以使用虚拟网络对等互连在多个 Azure 虚拟网络之间建立连接。 有关详细信息，请参阅[虚拟网络对等互连][VNet-peer]。

- 基于 Azure VPN 的解决方案  ：对于通过 P2S 或 S2S VPN 连接到 Azure 的远程工作员工，你可以通过在本地网络与 Azure VPN 网关之间配置 S2S VPN，来使其能够访问本地网络。 有关详细信息，请参阅[创建站点到站点连接][S2S]。

- **ExpressRoute**：使用 ExpressRoute 专用对等互连，可以在 Azure 部署与本地基础结构或共置设施中的基础结构之间启用专用连接。 通过 Microsoft 对等互连使用 ExpressRoute 还可以从本地网络访问 Microsoft 中的公共终结点。 ExpressRoute 连接不通过公共 Internet 。 与通过 Internet 建立的典型连接相比，ExpressRoute 提供的连接更安全，可靠性更高，吞吐量更高，并且延迟更低且稳定。 有关详细信息，请参阅 [ExpressRoute 概述][ExR]。 利用已成为我们 [ExpressRoute 合作伙伴生态系统][ExR-eco]一部分的现有网络提供商，可以帮助减少与 Microsoft 建立高带宽连接所需的时间。 

- **Azure 虚拟 WAN**：Azure 虚拟 WAN 可以实现 VPN 连接与 ExpressRoute 线路之间的无缝互操作。 如前所述，Azure 虚拟 WAN 还支持不同本地全局位置和不同区域性中心辐射型虚拟网络中的资源之间的任意点到任意点连接。

## <a name="scale-customer-connectivity-to-frontend-resources"></a>缩放与前端资源之间的客户连接

当上网的人越来越多时，许多企业网站遇到的客户流量会增大。 Azure 应用程序网关可以帮助应对这种前端工作负荷增大的问题。 有关详细信息，请参阅[应用程序网关高流量支持](../application-gateway/high-traffic-support.md)。

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Microsoft 对多云流量的支持

对于其他公有云中的部署，Microsoft 可提供全球连接。 Azure 虚拟 WAN、VPN 或 ExpressRoute 在此方面都可以发挥作用。 若要扩展从 Azure 到其他云的连接，可以在两个云之间配置 S2S VPN。 还可以使用 ExpressRoute 建立从 Azure 到其他公有云的连接。 Oracle 云是 ExpressRoute 合作伙伴生态系统的一部分。 属于 ExpressRoute 伙伴生态系统的一部分的大多数服务提供商还能提供与其他公有云之间的专用连接。 利用这些服务提供商，可以通过 ExpressRoute 在 Azure 与其他云中的部署之间建立专用连接。

## <a name="next-steps"></a>后续步骤

以下文章介绍了如何使用不同的 Azure 网络功能来缩放用户的连接，以使用户能够远程工作：

| **文章** | **上次更新时间** |
| --- | --- |
| [如何使用户能够远程工作](../vpn-gateway/work-remotely-support.md) | 2020 年 3 月 23 日 |
| [正在奋力满足在家工作的需求？Azure 虚拟 WAN 可以提供帮助](../virtual-wan/work-remotely-support.md) | 2020 年 3 月 23 日 |
| [应用程序网关高流量支持](../application-gateway/high-traffic-support.md) | 2020 年 3 月 23 日 |
| [用于远程工作的网络虚拟设备 (NVA) 的注意事项](../vpn-gateway/nva-work-remotely-support.md)| 2020 年 3 月 23 日 |
| [从 SSTP 转换到 OpenVPN 协议或 IKEv2](../vpn-gateway/ikev2-openvpn-from-sstp.md) | 2020 年 3 月 23 日 |
| [使用 Azure ExpressRoute 创建混合连接以支持远程用户](../expressroute/work-remotely-support.md) | 2020 年 3 月 23 日 |
| [Azure 防火墙远程工作支持](../firewall/remote-work-support.md)|2020 年 3 月 25 日|

<!--Link References-->
[VNet-peer]: https://docs.azure.cn/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.azure.cn/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.azure.cn/expressroute/expressroute-introduction
[ExR-eco]: https://docs.azure.cn/expressroute/expressroute-locations

<!-- [ExR-D]: https://docs.azure.cn/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.azure.cn/virtual-machines/workloads/oracle/configure-azure-oci-networking -->