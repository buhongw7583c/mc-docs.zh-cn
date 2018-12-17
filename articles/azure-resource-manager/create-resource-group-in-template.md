---
title: 在 Azure 资源管理器模板中创建资源组 | Azure
description: 介绍如何在 Azure 资源管理器模板中创建新的资源组。
services: azure-resource-manager
documentationcenter: na
author: rockboyfor
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 10/10/2018
ms.date: 11/19/2018
ms.author: v-yeche
ms.openlocfilehash: 3c84c3b03ec84bdbe0f646474b3a3ca12a8033d7
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52651964"
---
<!--Verify sucessfully-->
# <a name="create-resource-groups-in-azure-resource-manager-templates"></a>在 Azure 资源管理器模板中创建资源组

若要在 Azure 资源管理器模板中创建资源组，请为该资源组定义包含名称和位置的 Microsoft.Resources/resourceGroups 资源。 将模板部署到 Azure 订阅。 有关订阅级别部署的详细信息，请参阅[将资源部署到 Azure 订阅](deploy-to-subscription.md)。

此外，还可以将资源部署到同一模板中的资源组。

本文使用 Azure CLI 和 PowerShell 来部署模板。

## <a name="create-empty-resource-group"></a>创建空的资源组

以下示例创建空的资源组。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

若要使用 Azure CLI 部署此模板，请使用：

```azurecli
az deployment create \
  -n demoEmptyRG \
  -l chinaeast \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoRG rgLocation=chinaeast
```

若要使用 PowerShell 部署此模板，请使用：

```PowerShell
New-AzureRmDeployment `
  -Name demoEmptyRG `
  -Location chinaeast `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demogroup `
  -rgLocation chinaeast
```

## <a name="create-several-resource-groups"></a>创建多个资源组

结合使用 [copy 元素](resource-group-create-multiple.md)与资源组来创建多个资源组。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

若要使用 Azure CLI 部署此模板并创建三个资源组，请使用：

```azurecli
az deployment create \
  -n demoCopyRG \
  -l chinaeast \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json \
  --parameters rgNamePrefix=demoRG rgLocation=chinaeast instanceCount=3
```

若要使用 PowerShell 部署此模板，请使用：

```PowerShell
New-AzureRmDeployment `
  -Name demoCopyRG `
  -Location chinaeast `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json `
  -rgNamePrefix demogroup `
  -rgLocation chinaeast `
  -instanceCount 3
```

## <a name="create-resource-group-and-deploy-resource"></a>创建资源组并部署资源

若要创建资源组并向其部署资源，请使用嵌套模板。 嵌套模板定义要部署到资源组的资源。 将嵌套模板设置为依赖于资源组，确保资源组存在，然后再部署资源。

以下示例将创建一个资源组，并向该资源组部署存储帐户。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

若要使用 Azure CLI 部署此模板，请使用：

```azurecli
az deployment create \
  -n demoRGStorage \
  -l chinaeast \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json \
  --parameters rgName=rgStorage rgLocation=chinaeast storagePrefix=storage
```

若要使用 PowerShell 部署此模板，请使用：

```PowerShell
New-AzureRmDeployment `
  -Name demoRGStorage `
  -Location chinaeast `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json `
  -rgName rgStorage `
  -rgLocation chinaeast `
  -storagePrefix storage
```

## <a name="next-steps"></a>后续步骤
* 若要了解订阅级别部署，请参阅[将资源部署到 Azure 订阅](deploy-to-subscription.md)。
* 若要了解如何在部署期间排查依赖项故障，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](resource-manager-common-deployment-errors.md)。
* 若要了解有关创建 Azure Resource Manager模板的信息，请参阅[创作模板](resource-group-authoring-templates.md)。 
* 有关模板中的可用函数列表，请参阅[模板函数](resource-group-template-functions.md)。

<!-- Update_Description: update meta properties, wording update -->