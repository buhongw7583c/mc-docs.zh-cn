---
title: 创建外部 ASE
description: 了解如何创建其中包含应用的应用服务环境，或如何创建独立的（空）ASE。
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
origin.date: 06/13/2017
ms.date: 03/16/2020
ms.author: v-tawe
ms.custom: seodec18
ms.openlocfilehash: ec2f47eaeaa8f32808fcc7d7dca8cadabb96e507
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "79546970"
---
# <a name="create-an-external-app-service-environment"></a>创建外部应用服务环境

Azure 应用服务环境是指将 Azure App Service 部署到 Azure 虚拟网络 (VNet) 的子网中。

> [!NOTE]
> 每个应用服务环境都具有虚拟 IP (VIP)，可用于联系应用服务环境。

可通过两种方法部署应用服务环境 (ASE)：

- 使用外部 IP 地址上的 VIP，通常称为外部 ASE。
- 使用内部 IP 地址上的 VIP，通常称为 ILB ASE，因为内部终结点是内部负载均衡器 (ILB)。

本文介绍如何创建外部 ASE。 有关 ASE 的概述，请参阅[应用服务环境简介][Intro]。 若要了解如何创建 ILB ASE，请参阅[创建和使用 ILB ASE][MakeILBASE]。

## <a name="before-you-create-your-ase"></a>创建 ASE 前的准备工作

创建 ASE 后，不能更改以下项：

- 位置
- 订阅
- 资源组
- 使用的 VNet
- 使用的子网
- 子网大小

> [!NOTE]
> 选择 VNet 和指定子网时，请确保大小足够容纳未来的增长和扩展需求。 建议使用包含 256 个地址的大小 `/24`。
>

## <a name="three-ways-to-create-an-ase"></a>创建 ASE 的 3 种方法

可通过 3 种方法创建 ASE：

- 创建应用服务计划时  。 使用此方法，只需 1 步即可创建 ASE 和应用服务计划。
- 作为独立项操作  。 此方法可创建独立的 ASE，其中不包含任何内容。 此方法是创建 ASE 的更高级过程。 可由此通过 ILB 创建 ASE。
- 通过 Azure 资源管理器模板  。 此方法适用于高级用户。 有关详细信息，请参阅[从模板创建 ASE][MakeASEfromTemplate]。

外部 ASE 具有公共 VIP，这意味着到 ASE 中应用的所有 HTTP/HTTPS 流量都会命中一个可访问 Internet 的 IP 地址。 通过 ILB 创建的 ASE 具有 ASE 所用子网的 IP 地址。 托管在 ILB ASE 中的应用不直接向 Internet 公开。

## <a name="create-an-ase-and-an-app-service-plan-together"></a>同时创建 ASE 和应用服务计划

应用服务计划是一个应用容器。 在应用服务中创建应用时，需选择或创建应用服务计划。 应用服务环境保存应用服务计划，应用服务计划保存应用。

若要同时创建 ASE 和应用服务计划：

1. 在 [Azure 门户](https://portal.azure.cn/)中，选择“创建资源”   > “Web + 移动”   > “Web 应用”  。

    ![创建 Web 应用][1]

2. 选择订阅。 在相同订阅中创建应用和 ASE。

3. 选择或创建资源组。 使用资源组，可以单位形式管理相关的 Azure 资源。 为应用建立基于角色的访问控制 (RBAC) 规则时，资源组也可发挥作用。 有关详细信息，请参阅 [Azure 资源管理器概述][ARMOverview]。

<!-- need to update, current no OS need to select -->
<!-- 4. Select your OS (Windows, Linux, or Docker). -->

5. 选择应用服务计划，然后选择“新建”  。 

    <!-- Linux web apps and Windows web apps cannot be in the same App Service Plan, but can be in the same App Service Environment. -->

    ![新建应用服务计划][2]

6. 在“位置”下拉列表中，选择要创建 ASE 的区域  。 如果选择现有 ASE，则不会新建 ASE。 应用服务计划将在所选 ASE 中进行创建。 

7. 选择“定价层”，然后选择其中一个“独立”定价 SKU   。 如果选择独立 SKU 卡和非 ASE 的位置，则会在该位置新建一个 ASE  。 若要开始创建 ASE 的过程，请选择“选择”  。 独立 SKU 仅能与 ASE 结合使用  。 此外，仅可在独立 ASE 中使用任何其他定价 SKU  。 

    ![定价层选择][3]

8. 输入你的 ASE 的名称。 此名称在应用的可寻址名称中使用。 如果 ASE 名称为 _appsvcenvdemo_，则域名为 *.appsvcenvdemo.p.chinacloudsites.cn*。 如果创建名为 *mytestapp* 的应用，则可通过 mytestapp.appsvcenvdemo.p.chinacloudsites.cn 访问它。 不能在名称中使用空格。 如果使用大写字符，则域名为该名称的全小写形式。

    ![新建应用服务计划名称][4]

9. 指定 Azure 虚拟网络详细信息。 选择“新建”或“选择现有”   。 只有所选区域中具有 VNet 时，才可使用选择现有 VNet 选项。 如果选择“新建”，需输入 VNet 的名称  。 随即创建带有该名称的新资源管理器 VNet。 它使用所选区域中的地址空间 `192.168.250.0/23`。 如果选择“选择现有”，则需要： 

    a. 选择 VNet 地址块（若有多个）。

    b. 输入新的子网名称。

    c. 选择子网的大小。 请记住选择足够的大小，以容纳 ASE 的未来增长  。 建议使用 `/24`，其地址长度为 128 位且能够容纳最大尺寸的 ASE。 例如，建议不要使用 `/28`，因为仅有 16 位地址可用。 基础结构至少使用 7 个地址，Azure 网络使用另外 5 个地址。 在 `/28` 子网中，对于外部 ASE，你最多可以缩放 4 个应用服务计划，而对于 ILB ASE，只能缩放 3 个应用服务计划。

    d. 选择子网 IP 范围。

10. 选择“创建”以创建 ASE  。 此过程还会创建应用服务计划和应用。 ASE、应用服务计划和应用都位于同一订阅和同一资源组中。 如果 ASE 需要单独的资源组，或者你需要 ILB ASE，请按照以下步骤自动创建 ASE。

<!-- linux web apps & container is not available -->
## <a name="create-an-ase-by-itself"></a>自动创建 ASE

如果创建独立的 ASE，则其中不含任何内容。 空的 ASE 存在基础结构，每月仍会产生费用。 按照这些步骤通过 ILB 创建 ASE，或在其自身的资源组中创建 ASE。 创建 ASE 后，可使用常规过程在其中创建应用。 选择新 ASE 作为位置。

1. 在 Azure 市场中搜索“应用服务环境”，或者选择“创建资源” > “Web 移动” > “应用服务环境”     。 

1. 输入 ASE 的名称。 此名称用于在 ASE 中创建的应用。 如果该名称为 mynewdemoase，则子域名为 .mynewdemoase.p.chinacloudsites.cn   。 如果创建名为 mytestapp 的应用，则可在 mytestapp.mynewdemoase.p.chinacloudsites.cn 中访问它  。 不能在名称中使用空格。 如果使用大写字符，则域名为该名称的全小写形式。 如果使用 ILB，则不在子域中使用 ASE 名称，但会在 ASE 创建过程中显式声明该名称。

    ![ASE 命名][5]

1. 选择订阅。 此订阅也是 ASE 中所有应用使用的订阅。 不能将 ASE 放入位于其他订阅中的 VNet。

1. 选择或指定新的资源组。 用于 ASE 的资源组必须与用于 VNet 的资源组相同。 如果选择现有 VNet，则 ASE 的资源组选择会更新，以反映 VNet 的资源组。 如果使用资源管理器模板，则可使用不同于 VNet 资源组的资源组来创建 ASE  。 若要从模板创建 ASE，请参阅[从模板创建应用服务环境][MakeASEfromTemplate]。

    ![资源组选择][6]

1. 选择 VNet 和位置。 可选择创建新的 VNet，也可选择现有 VNet： 

    * 如果选择新的 VNet，则可指定名称和位置。 
    
    * 新 VNet 的地址范围为 192.168.250.0/23，并拥有名为“默认”的子网。 子网定义为 192.168.250.0/24。 仅可选择一个资源管理器 VNet。 “VIP 类型”选择决定 ASE 能否从 Internet（外部）直接访问或是否使用 ILB  。 若要深入了解这些选项，请参阅[在应用服务环境中创建和使用内部负载均衡器][MakeILBASE]。 

      * 如果对“VIP 类型”选择“外部”，则可选择为实现基于 IP 的 SSL 而创建系统时所用的外部 IP 地址数   。 
    
      * 如果对“VIP 类型”选择“内部”，则需指定 ASE 要使用的域   。 可将 ASE 部署到使用公用或专用地址范围的 VNet。 若要使用具有公用地址范围的 VNet，需要提前创建 VNet。 
    
    * 如果选择现有 VNet，需要在 ASE 创建期间创建新的子网。 不能在门户中使用预先创建的子网。  如果使用资源管理器模板，则可创建具有现有子网的 ASE。 若要从模板创建 ASE，请参阅[从模板创建应用服务环境][MakeASEfromTemplate]。

<!-- ## App Service Environment v1 ->

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



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
