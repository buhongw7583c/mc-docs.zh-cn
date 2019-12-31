---
title: 在 Azure IoT Central 应用程序中管理用户和角色 | Microsoft Docs
description: 本文为管理员介绍如何在 Azure IoT Central 应用程序中管理用户和角色
author: lmasieri
ms.author: v-yiso
origin.date: 10/22/2019
ms.date: 12/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: df81fbb846addbd55c579db926795fe87ad1ac5d
ms.sourcegitcommit: 6ffa4d50cee80c7c0944e215ca917a248f2a4bcd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74883392"
---
# <a name="manage-users-and-roles-in-your-iot-central-application-preview-features"></a>在 IoT Central 应用程序中管理用户和角色（预览版功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本文介绍管理员如何在 Azure IoT Central 应用程序中添加、编辑和删除用户。 本文还将介绍如何在 Azure IoT Central 应用程序中管理角色。

只有 Azure IoT Central 应用程序的“管理员”  角色才能访问和使用“管理”  部分。 如果你创建了 Azure IoT Central 应用程序，则会自动添加到该应用程序的“管理员”角色。 

## <a name="add-users"></a>添加用户

每个用户必须有一个用户帐户才能登录和访问 Azure IoT Central 应用程序。 Azure IoT Central 支持 Microsoft 帐户 (MSA) 和 Azure Active Directory (Azure AD) 帐户。 Azure IoT Central 目前不支持 Azure Active Directory 组。

有关详细信息，请参阅 [Microsoft 帐户帮助](https://support.microsoft.com/products/microsoft-account?category=manage-account)和[快速入门：将新用户添加到 Azure Active Directory](/active-directory/add-users-azure-active-directory)。

1. 若要将用户添加到 IoT Central 应用程序，请转到“用户”页中的“管理”部分。  
    
    > [!div class="mx-imgBorder"]
    >![管理用户](media/howto-manage-users-roles/manage-users-pnp.png)

1. 若要添加用户，在“用户”  页上，选择“+ 添加用户”  。

1. 从“角色”  下拉菜单中为用户选择一个角色。 在本文的[管理角色](#manage-roles)部分详细了解角色。

    > [!div class="mx-imgBorder"]
    >![添加用户和选择角色](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > 对于充当自定义角色的用户，如果该角色授予该用户添加其他用户的权限，则该用户只能将其他用户添加到权限与自身角色相同或更低的角色。

### <a name="edit-the-roles-that-are-assigned-to-users"></a>编辑分配给用户的角色

分配角色后无法对其进行更改。 若要更改分配给某个用户的角色，请删除该用户，然后使用不同的角色再次添加该用户。

> [!NOTE]
> 分配的角色特定于 IoT Central 应用程序，不能从 Azure 门户进行管理。

## <a name="delete-users"></a>删除用户

若要删除用户，选择“用户”  页上的一个或多个复选框。 然后选择“删除”  。

## <a name="manage-roles"></a>管理角色

使用角色可以控制组织中的哪些人可以在 IoT Central 中执行各种任务。 可将三种内置角色分配给应用程序的用户。 如果需要更精细的控制，还可以[创建自定义角色](#create-a-custom-role)。

> [!div class="mx-imgBorder"]
> ![管理角色选择](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>管理员

充当“管理员”角色的用户可以管理和控制应用程序的每个部分，包括计费。 

创建应用程序的用户会自动分配到“管理员”  角色。 必须始终有一个用户充当“管理员”角色。 

### <a name="builder"></a>构建者

充当“构建者”角色的用户可以管理应用的每个部分，但不能在“管理”或“连续数据导出”选项卡上进行更改。 

### <a name="operator"></a>运算符

充当“操作员”角色的用户可以监视设备的运行状况和状态。  他们不能对设备模板进行更改，也不能管理应用程序。 操作员可以添加和删除设备、管理设备集，以及运行分析和作业。 

## <a name="create-a-custom-role"></a>创建自定义角色

如果解决方案需要更精细的访问控制，可以创建拥有自定义权限集的自定义角色。 若要创建自定义角色，请在应用程序的“管理”部分导航到“角色”页。   然后选择“+ 新建角色”，并添加该角色的名称和说明。  选择角色所需的权限，然后选择“保存”。 

可将用户添加到自定义角色，如同将用户添加到内置角色一样。

> [!div class="mx-imgBorder"]
> ![构建自定义角色](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>自定义角色选项

定义自定义角色时，如果某个用户是该角色的成员，请选择要授予该用户的权限集。 某些权限依赖于其他权限。 例如，如果将“更新应用程序仪表板”权限添加到某个角色，则会自动添加“查看应用程序仪表板”权限。   下表汇总了在创建自定义角色时可以使用的权限及其依赖项。

#### <a name="managing-devices"></a>管理设备

**设备模板权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无     |
| 管理 | 查看 <br/> 其他依赖项：查看设备实例  |
| 完全控制 | 查看、管理 <br/> 其他依赖项：查看设备实例 |

**设备实例权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无 <br/> 其他依赖项：查看设备模板和设备组 |
| 更新 | 查看 <br/> 其他依赖项：查看设备模板和设备组  |
| 创建 | 查看 <br/> 其他依赖项：查看设备模板和设备组  |
| Delete | 查看 <br/> 其他依赖项：查看设备模板和设备组  |
| 执行命令 | 更新、查看 <br/> 其他依赖项：查看设备模板和设备组  |
| 完全控制 | 查看、更新、创建、删除、执行命令 <br/> 其他依赖项：查看设备模板和设备组  |

**设备组权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无 <br/> 其他依赖项：查看设备模板和设备实例 |
| 更新 | 查看 <br/> 其他依赖项：查看设备模板和设备实例   |
| 创建 | 查看、更新 <br/> 其他依赖项：查看设备模板和设备实例   |
| Delete | 查看 <br/> 其他依赖项：查看设备模板和设备实例   |
| 完全控制 | 查看、更新、创建、删除 <br/> 其他依赖项：查看设备模板和设备实例 |

**设备连接管理权限**

| Name | 依赖项 |
| ---- | -------- |
| 读取实例 | 无 <br/> 其他依赖项：查看设备模板、设备组和设备实例 |
| 管理实例 | 无 |
| 全局读取 | 无   |
| 全局管理 | 全局读取 |
| 完全控制 | 读取实例、管理实例、全局读取、全局管理。 <br/> 其他依赖项：查看设备模板、设备组和设备实例 |

**作业权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无 <br/> 其他依赖项：查看设备模板、设备实例和设备组 |
| 更新 | 查看 <br/> 其他依赖项：查看设备模板、设备实例和设备组 |
| 创建 | 查看、更新 <br/> 其他依赖项：查看设备模板、设备实例和设备组 |
| Delete | 查看 <br/> 其他依赖项：查看设备模板、设备实例和设备组 |
| 执行 | 查看 <br/> 其他依赖项：查看设备模板、设备实例和设备组；更新设备实例；对设备实例执行命令 |
| 完全控制 | 查看、更新、创建、删除、执行 <br/> 其他依赖项：查看设备模板、设备实例和设备组；更新设备实例；对设备实例执行命令 |

**规则权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无 <br/> 其他依赖项：查看设备模板 |
| 更新 | 查看 <br/> 其他依赖项：查看设备模板 |
| 创建 | 查看、更新 <br/> 其他依赖项：查看设备模板 |
| Delete | 查看 <br/> 其他依赖项：查看设备模板 |
| 完全控制 | 查看、更新、创建、删除 <br/> 其他依赖项：查看设备模板 |

#### <a name="managing-the-app"></a>管理应用

**应用程序设置权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无     |
| 更新 | 查看   |
| 复制 | 查看 <br/> 其他依赖项：查看设备模板、设备实例、设备组、仪表板、数据导出、品牌、帮助链接、自定义角色、规则 |
| Delete | 查看   |
| 完全控制 | 查看、更新、复制、删除 <br/> 其他依赖项：查看设备模板、设备组、应用程序仪表板、数据导出、品牌、帮助链接、自定义角色、规则 |

**应用程序模板导出权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无     |
| 导出 | 查看 <br/> 其他依赖项：查看设备模板、设备实例、设备组、仪表板、数据导出、品牌、帮助链接、自定义角色、规则 |
| 完全控制 | 查看、导出 <br/> 其他依赖项：查看设备模板、设备组、应用程序仪表板、数据导出、品牌、帮助链接、自定义角色、规则 |

**计费权限**

| Name | 依赖项 |
| ---- | -------- |
| 管理 | 无     |
| 完全控制 | 管理 |

#### <a name="managing-users-and-roles"></a>管理用户和角色

**自定义角色权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无 |
| 更新 | 查看 |
| 创建 | 查看、更新 |
| Delete | 查看 |
| 完全控制 | 查看、更新、创建、删除 |

**用户管理权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无 <br/> 其他依赖项：查看自定义角色 |
| 添加 | 查看 <br/> 其他依赖项：查看自定义角色 |
| Delete | 查看 <br/> 其他依赖项：查看自定义角色 |
| 完全控制 | 查看、添加、删除 <br/> 其他依赖项：查看自定义角色 |

> [!NOTE]
> 对于充当自定义角色的用户，如果该角色授予该用户添加其他用户的权限，则该用户只能将其他用户添加到权限与自身角色相同或更低的角色。

#### <a name="customizing-the-app"></a>自定义应用

**应用程序仪表板权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无     |
| 更新 | 查看   |
| 创建 | 查看、更新 |
| Delete | 查看   |
| 完全控制 | 查看、更新、创建、删除 |

**个人仪表板权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无     |
| 更新 | 查看   |
| 创建 | 查看、更新   |
| Delete | 查看   |
| 完全控制 | 查看、更新、创建、删除 |

**品牌、网站图标和颜色权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无     |
| 更新 | 查看   |
| 完全控制 | 查看、更新 |

**帮助链接权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无     |
| 更新 | 查看   |
| 完全控制 | 查看、更新 |

#### <a name="extending-the-app"></a>扩展应用

**数据导出权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无     |
| 更新 | 查看   |
| 创建 | 查看、更新  |
| Delete | 查看   |
| 完全控制 | 查看、更新、创建、删除 |

**API 令牌权限**

| Name | 依赖项 |
| ---- | -------- |
| 查看 | 无     |
| 创建 | 查看   |
| Delete | 查看   |
| 完全控制 | 查看、创建、删除 |

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中管理用户和角色后，建议接下来了解如何[管理帐单](howto-view-bill.md)。