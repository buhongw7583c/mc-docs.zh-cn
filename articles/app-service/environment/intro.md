---
title: 介绍
description: 了解如何通过 Azure 应用服务环境在一个完全隔离的专用环境中缩放、保护和优化应用。
author: ccompy
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.topic: overview
origin.date: 04/19/2018
ms.date: 03/30/2020
ms.author: v-tawe
ms.custom: mvc, seodec18
ms.openlocfilehash: 0229759aea4fdf523d10eb1fb623b231b9fcf084
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "80522078"
---
# <a name="introduction-to-the-app-service-environments"></a>应用服务环境简介 #
 
## <a name="overview"></a>概述 ##

Azure 应用服务环境是一项 Azure 应用服务功能，可提供完全隔离和专用的环境，以便高度安全地运行应用服务应用。 此功能可以托管：

<!-- Linux web apps/containers not available -->
<!-- * Linux web apps -->
<!-- * Docker containers -->

* Windows Web 应用
* 移动应用
* 函数

应用服务环境 (ASE) 适用于有以下要求的应用程序工作负荷：

* 极高的缩放性。
* 隔离和安全网络访问。
* 高内存利用率。

客户可以在单个 Azure 区域或多个 Azure 区域创建多个 ASE。 这种灵活性使得 ASE 非常适合用于水平缩放无状态应用程序层，以支持高 RPS 工作负荷。

ASE 可在隔离后只运行单个客户的应用程序，并可始终部署到虚拟网络中。 客户可以对入站和出站应用程序网络流量进行精细控制。 应用程序可以通过 VPN 建立到本地公司资源的高速安全连接。

<!-- channel9 not available -->

* ASE 附带自己的定价层，了解独立产品/服务如何有助于驱动超大规模和安全性。
* 应用服务环境 v2 提供了一个环境来保护网络子网中的应用，并提供你自己的 Azure 应用服务专用部署。
* 可使用多个 ASE 进行水平缩放。 有关详细信息，请参阅[如何设置异地分布式应用布局](app-service-app-service-environment-geo-distributed-scale.md)。
* 可使用 ASE 配置安全体系结构，如“AzureCon 深入探讨”中所示。
* 在 ASE 中运行的应用的访问权限可能受到 Web 应用程序防火墙 (WAF) 等上游设备的管制。 有关详细信息，请参阅 [Web 应用程序防火墙 (WAF)][AppGW]。
* 可以使用区域固定将应用服务环境部署到可用性区域 (AZ) 中。  有关更多详细信息，请参阅[应用服务环境对可用性区域的支持][ASEAZ]。

## <a name="dedicated-environment"></a>专用环境 ##

ASE 专用于单个订阅，可托管 100 个应用服务计划实例。 其范围可涵盖单个应用服务计划中的 100 个实例，也可以是 100 个单实例应用服务计划，或者两者之间的任何实例。

ASE 由前端和辅助角色组成。 前端负责处理 HTTP/HTTPS 终止以及 ASE 中应用请求的自动负载均衡。 前端作为应用服务计划自动添加在 ASE 中，并且可以扩展。

辅助角色是托管客户应用的角色。 辅助角色有 3 种固定大小：

* 一个 vCPU/3.5 GB RAM
* 两个 vCPU/7 GB RAM
* 四个 vCPU/14 GB RAM

客户无需管理前端和辅助角色。 客户扩展其应用服务计划时，会自动添加所有基础结构。 在 ASE 中创建或缩放应用服务计划时，将在适当的情况下添加或删除所需的基础结构。

ASE 每月会产生统一的基础结构使用费，该费率不会随 ASE 的大小变化而改变。 此外，每个应用服务计划 vCPU 也会产生费用。 ASE 中托管的所有应用都在“隔离”定价 SKU 中。 有关 ASE 定价的信息，请参阅[应用服务定价][Pricing]页并查看 ASE 的可用选项。

## <a name="virtual-network-support"></a>虚拟网络支持 ##

ASE 功能直接将 Azure 应用服务部署到客户的 Azure 资源管理器虚拟网络。 若要了解有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络常见问题解答](https://docs.azure.cn/virtual-network/virtual-networks-faq)。 ASE 始终存在于虚拟网络之中，更准确地说，是在虚拟网络的子网内。 可使用虚拟网络的安全功能为应用控制入站和出站网络通信。

ASE 既可以是面向 Internet 的（使用公共 IP 地址），也可以是面向内部的（只使用 Azure 内部负载均衡器 (ILB) 地址）。

[网络安全组][NSGs]将入站网络通信限制为 ASE 所在的子网。 可以在上游设备和服务（例如 WAF 和网络 SaaS 提供程序）后使用 NSG 来运行应用。

应用还经常需要访问公司资源，例如内部数据库和 Web 服务。 如果在包含本地网络的 VPN 连接的虚拟网络中部署 ASE，ASE 中的应用可以访问本地资源。 无论 VPN 是[站点到站点](https://docs.azure.cn/vpn-gateway/vpn-gateway-multi-site) VPN，还是 [Azure ExpressRoute](https://www.azure.cn/home/features/expressroute/) VPN，都可以使用此功能。

有关如何在虚拟网络和本地网络中使用 ASE 的详细信息，请参阅[应用服务环境网络注意事项][ASENetwork]。

<!-- > [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player] -->

<!-- ## App Service Environment v1 ## -->

<!-- [App Service Environments v2]: https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment -->
<!-- [Isolated offering]: https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment -->
<!-- [ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md -->
<!-- [Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/ -->

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://www.azure.cn/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/waf-overview.md
[ASEAZ]: https://azure.github.io/AppService/2019/12/12/App-Service-Environment-Support-for-Availability-Zones.html
