---
title: 创建管理组来组织 Azure 资源 | Azure
description: 了解如何创建 Azure 管理组来管理多个资源。
author: rockboyfor
manager: digimobile
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 10/10/2018
ms.date: 10/29/2018
ms.author: v-yeche
ms.openlocfilehash: 0e8eb4f54cbef1eddffc1ec63731eaf67efe0fa9
ms.sourcegitcommit: 5f2849d5751cb634f1cdc04d581c32296e33ef1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2018
ms.locfileid: "53028482"
---
<!--Verify successfully-->
# <a name="create-management-groups-for-resource-organization-and-management"></a>创建用来组织和管理资源的管理组

管理组是一些容器，可以帮助你跨多个订阅管理访问权限、策略和符合性。 可以创建这些容器来构建可以与 [Azure Policy](../policy/overview.md) 和 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)配合使用的有效且高效的层次结构。 若要详细了解管理组，请参阅[使用 Azure 管理组整理资源](index.md)。

在目录中创建的第一个管理组可能需要最多 15 分钟才能完成。 一些进程会首次运行以在 Azure 中为目录设置管理组服务。 在进程完成后将显示通知。

## <a name="create-a-management-group"></a>创建管理组

可以使用门户、PowerShell 或 Azure CLI 创建管理组。 目前无法使用资源管理器模板创建管理组。

### <a name="create-in-portal"></a>在门户中创建

1. 登录到 [Azure 门户](http://portal.azure.cn)。

1. 选择“所有服务” > “管理组”。

1. 在主页上，选择“新建管理组”。

   ![主要组](./media/main.png)

1. 填写管理组 ID 字段。

   - “管理组 ID”是用来在此管理组上提交命令的目录唯一标识符。 此标识符在创建后不可编辑，因为它用来在整个 Azure 系统中标识此组。
   - 显示名称字段是在 Azure 门户中显示的名称。 创建管理组时，单独的显示名称是一个可选字段，并且可以随时更改。  

   ![创建](./media/create_context_menu.png)  

1. 选择“其他安全性验证” 。

### <a name="create-in-powershell"></a>在 PowerShell 中创建

在 PowerShell 中，使用 New-AzureRmManagementGroups cmdlet：

```PowerShell
New-AzureRmManagementGroup -GroupName 'Contoso'
```

**GroupName** 是要创建的唯一标识符。 此 ID 由其他命令用来引用此组，并且以后无法更改。

如果希望管理组在 Azure 门户中显示一个不同的名称，则通过字符串添加 **DisplayName** 参数。 例如，如果希望创建一个 GroupName 为 Contoso 且显示名称为“Contoso Group”的管理组，需要使用以下 cmdlet：

```PowerShell
New-AzureRmManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId 'ContosoTenant'
```

可以使用 **ParentId** 参数将此管理组创建到另一个管理组下。

### <a name="create-in-azure-cli"></a>在 Azure CLI 中创建

在 Azure CLI 中，可以使用 az account management-group create 命令。

```azurecli
az account management-group create --name 'Contoso'
```

## <a name="next-steps"></a>后续步骤

若要详细了解管理组，请参阅：

- [使用 Azure 管理组来组织资源](index.md)
- [如何更改、删除或管理管理组](manage.md)
- [安装 Azure Powershell 模块](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [查看 REST API 规范](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [安装 Azure CLI 扩展](https://docs.azure.cn/zh-cn/cli/extension?view=azure-cli-latest#az-extension-list-available)

<!-- Update_Description: update meta properties, wording update, update link -->