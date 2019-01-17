---
title: Azure PowerShell 脚本示例 - 将应用连接到存储帐户 | Azure Docs
description: Azure PowerShell 脚本示例 - 将应用服务应用连接到存储帐户
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
origin.date: 03/20/2017
ms.date: 01/21/2019
ms.author: v-biyu
ms.custom: mvc
ms.openlocfilehash: 8100d860ac1cbd0d8d037b678d14d5811d48f6bc
ms.sourcegitcommit: 90d5f59427ffa599e8ec005ef06e634e5e843d1e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54083835"
---
# <a name="connect-an-app-service-app-to-a-storage-account"></a>将应用服务应用连接到存储帐户

在此方案中，将了解如何创建 Azure 存储帐户和应用服务应用。 然后，将使用应用设置将存储帐户链接到应用。

必要时，请使用 [Azure PowerShell 指南](https://docs.microsoft.com/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Login-AzureRmAccount -EnvironmentName AzureChinaCloud` 创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

```powershell

# Generates a Random Value
$Random=(New-Guid).ToString().Substring(0,8)

# Variables
$ResourceGroup="MyResourceGroup$Random"
$AppName="webappwithStorage$Random"
$StorageName="webappstorage$Random"
$Location="China North"

# Create a Resource Group
New-AzureRMResourceGroup -Name $ResourceGroup -Location $Location

# Create an App Service Plan
New-AzureRMAppservicePlan -Name WebAppwithStoragePlan -ResourceGroupName $ResourceGroup -Location $Location -Tier Basic

# Create a Web App in the App Service Plan
New-AzureRMWebApp -Name $AppName -ResourceGroupName $ResourceGroup -Location $Location -AppServicePlan WebAppwithStoragePlan

# Create Storage Account
New-AzureRMStorageAccount -Name $StorageName -ResourceGroupName $ResourceGroup -Location $Location -SkuName Standard_LRS

# Get Connection String for Storage Account
$StorageKey=(Get-AzureRMStorageAccountKey -ResourceGroupName $ResourceGroup -Name $StorageName).Value[0]

# Assign Connection String to App Setting 
Set-AzureRMWebApp -ConnectionStrings @{ MyStorageConnStr = @{ Type="Custom"; Value="DefaultEndpointsProtocol=https;AccountName=$StorageName;AccountKey=$StorageKey;" } } -Name $AppName -ResourceGroupName $ResourceGroup
```

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、应用服务应用以及所有相关资源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 注释 |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/module/azurerm.websites/new-azurermappserviceplan) | 创建应用服务计划。 |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/module/azurerm.websites/new-azurermwebapp) | 创建 Web 应用。 |
| [New-AzureRMStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/new-azurermstorageaccount) | 创建存储帐户。 |
| [Get-AzureRMStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | 获取 Azure 存储帐户的访问密钥。 |
| [Set-AzureRmWebApp](https://docs.microsoft.com/powershell/module/azurerm.websites/set-azurermwebapp) | 修改 Web 应用的配置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务的其他 Azure Powershell 示例。