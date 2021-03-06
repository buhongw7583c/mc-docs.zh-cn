---
title: 在 Azure 事件网格中使用 Azure AD 进行安全的 WebHook 传递
description: 介绍如何将事件传递到受到 Azure Active Directory 通过 Azure 事件网格进行保护的 HTTPS 终结点
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
origin.date: 11/18/2019
ms.date: 12/23/2019
ms.author: v-yiso
ms.openlocfilehash: 5547fbcd7da7050fae4983898f9f35e96f3ded8b
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "75348565"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>将事件发布到受 Azure Active Directory 保护的终结点

本文介绍如何利用 Azure Active Directory 来保护事件订阅和 Webhook 终结点之间的连接。 有关 Azure AD 应用程序和服务主体的概述，请参阅 [Microsoft 标识平台 (v2.0) 概述](/active-directory/develop/v2-overview)。

本文使用 Azure 门户进行演示，但也可通过 CLI、PowerShell 或 SDK 来启用此功能。

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-and-azure-ad-application"></a>创建 Azure AD 应用程序

从为受保护的终结点创建 Azure AD 应用程序着手。 请参阅 https://docs.azure.cn/active-directory/develop/scenario-protected-web-api-overview 。
    - 将受保护的 API 配置为通过守护程序应用进行调用。
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>允许事件网格使用 Azure AD 应用程序

使用下面的 PowerShell 脚本在 Azure AD 应用程序中创建角色和服务主体。 需要来自 Azure AD 应用程序的租户 ID 和对象 ID：

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.azure.cn/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. 修改 PowerShell 脚本的 $myTenantId，以便使用 Azure AD 租户 ID。
1. 修改 PowerShell 脚本的 $myAzureADApplicationObjectId，以便使用 Azure AD 应用程序的对象 ID。
1. 运行修改的脚本。

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>配置事件订阅

在事件订阅的创建流中，选择终结点类型“Web Hook”。 给出终结点 URI 以后，请单击“创建事件订阅”边栏选项卡顶部的“其他功能”选项卡。

![选择终结点类型 webhook](./media/secure-webhook-delivery/select-webhook.png)

在“其他功能”选项卡中，勾选“使用 AAD 身份验证”框并配置租户 ID 和应用程序 ID：

* 从脚本输出中复制 Azure AD 租户 ID，将其输入 AAD 租户 ID 字段中。
* 从脚本输出中复制 Azure AD 应用程序 ID，将其输入 AAD 应用程序 ID 字段中。

    ![保护 Webhook 操作](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>后续步骤

* 有关监视事件传送的信息，请参阅[监视事件网格消息传送](monitor-event-delivery.md)。
* 有关身份验证密钥的详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。