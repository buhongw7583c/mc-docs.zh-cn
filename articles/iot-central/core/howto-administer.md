---
title: 更改 Azure IoT Central 应用程序设置 | Microsoft Docs
description: 本文介绍管理员如何通过更改应用程序名称和 URL 来管理 Azure IoT Central 应用程序，以及如何上传映像和删除应用程序
author: viv-liu
ms.author: viviali
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b2dca8e7286d859128a8cf89a01ae85bb4ab66d9
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "80343251"
---
# <a name="change-iot-central-application-settings"></a>更改 IoT Central 应用程序设置



本文为管理员介绍如何通过更改应用程序名称和 URL 以及上传图像来管理应用程序，以及在 Azure IoT Central 应用程序中删除应用程序。

只有 Azure IoT Central 应用程序的“管理员”  角色才能访问和使用“管理”  部分。 如果你创建了 Azure IoT Central 应用程序，则会自动分配到该应用程序的“管理员”  角色。

## <a name="change-application-name-and-url"></a>更改应用程序名称和 URL

在“应用程序设置”页中，可以更改应用程序的名称和 URL，然后选择“保存”。  

![“应用程序设置”页](media/howto-administer/image0-a.png)

如果管理员为应用程序创建了自定义主题，此页将包含一个用于在 UI 中隐藏“应用程序名称”的选项。  如果自定义主题中的应用程序徽标包含应用程序名称，则此选项非常有用。 有关详细信息，请参阅[自定义 Azure IoT Central UI](./howto-customize-ui.md)。

> [!Note]
> 如果更改了 URL，旧 URL 可由其他 Azure IoT Central 客户使用。 如果出现此情况，你再也不能使用旧 URL。 更改 URL 后，旧 URL 不再有效，因此需要告知用户要使用的新 URL。

## <a name="delete-an-application"></a>删除应用程序

使用“删除”按钮可以永久删除 IoT Central 应用程序。  此操作会永久删除与该应用程序关联的所有数据。

> [!Note]
> 若要删除某个应用程序，还必须有权删除在创建应用程序时所选的 Azure 订阅中的资源。 有关详细信息，请参阅[使用基于角色的访问控制来管理对 Azure 订阅资源的访问权限](/active-directory/role-based-access-control-configure)。

## <a name="manage-programmatically"></a>以编程方式管理

IoT Central Azure 资源管理器 SDK 程序包适用于 Node、Python、C#、Ruby、Java 和 Go。 可以使用这些包来创建、列出、更新或删除 IoT Central 应用程序。 这些包包含用于管理身份验证和错误处理的帮助程序。

可以在 [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples) 中找到有关如何使用 Azure 资源管理器 SDK 的示例。

有关详细信息，请参阅以下 GitHub 存储库和包：

| 语言 | 存储库 | 程序包 |
| ---------| ---------- | ------- |
| 节点 | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>后续步骤

了解如何管理 Azure IoT Central 应用程序后，建议接下来了解如何在 Azure IoT Central 中[管理用户和角色](howto-manage-users-roles.md)。
