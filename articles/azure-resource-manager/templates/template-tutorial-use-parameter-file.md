---
title: 教程 - 使用参数文件部署模板
description: 使用参数文件，其中包含的值可用于部署 Azure 资源管理器模板。
author: rockboyfor
origin.date: 10/04/2019
ms.date: 03/23/2020
ms.topic: tutorial
ms.author: v-yeche
ms.openlocfilehash: 4f67e24d2cd44bd5fb6a0923c73574f6f00c3294
ms.sourcegitcommit: 564739de7e63e19a172122856ebf1f2f7fb4bd2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82093522"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>教程：使用参数文件部署资源管理器模板

本教程介绍如何使用[参数文件](parameter-files.md)存储在部署过程中传递的值。 在以前的教程中，我们通过部署命令使用了内联参数。 此方法适用于测试模板，但在自动进行部署时，为环境传递一组值可能会更容易。 参数文件可以方便你针对特定的环境将参数值打包。 在本教程中，我们将针对开发和生产环境创建参数文件。 完成该过程需要大约 **12 分钟**。

## <a name="prerequisites"></a>先决条件

建议完成[有关标记的教程](template-tutorial-add-tags.md)，但这不是必需的。

必须已安装带有资源管理器工具扩展的 Visual Studio Code，以及 Azure PowerShell 或 Azure CLI。 有关详细信息，请参阅[模板工具](template-tutorial-create-first-template.md#get-tools)。

## <a name="review-template"></a>审阅模板

模板有许多可以在部署过程中提供的参数。 在上一教程的末尾，模板如下所示：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "type": "string",
      "minLength": 3,
      "maxLength": 11
    },
    "storageSKU": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS",
        "Standard_GZRS",
        "Standard_RAGZRS"
      ]
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "appServicePlanName": {
      "type": "string",
      "defaultValue": "exampleplan"
    },
    "webAppName": {
      "type": "string",
      "metadata": {
        "description": "Base name of the resource such as web app name and app service plan "
      },
      "minLength": 2
    },
    "linuxFxVersion": {
      "type": "string",
      "defaultValue": "php|7.0",
      "metadata": {
        "description": "The Runtime stack of current web app"
      }
    },
    "resourceTags": {
        "type": "object",
        "defaultValue": {
            "Environment": "Dev",
            "Project": "Tutorial"
        }
    }
  },
  "variables": {
    "uniqueStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
    "webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('uniqueStorageName')]",
      "location": "[parameters('location')]",
      "tags": "[parameters('resourceTags')]",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "StorageV2",
      "properties": {
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[parameters('appServicePlanName')]",
      "location": "[parameters('location')]",
      "tags": "[parameters('resourceTags')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2016-08-01",
      "name": "[variables('webAppPortalName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[parameters('appServicePlanName')]"
      ],
      "tags": "[parameters('resourceTags')]",
      "kind": "app",
      "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('appServicePlanName'))]",
        "siteConfig": {
          "linuxFxVersion": "[parameters('linuxFxVersion')]"
        }
      }
    }
  ],
  "outputs": {
    "storageEndpoint": {
      "type": "object",
      "value": "[reference(variables('uniqueStorageName')).primaryEndpoints]"
    }
  }
}
```

该模板可以正常工作，但现在，你希望能够轻松地管理为模板传入的参数。

## <a name="add-parameter-files"></a>添加参数文件

参数文件是 JSON 文件，其结构类似于模板。 在文件中，我们提供需要在部署过程中传入的参数值。

在 VS Code 中，创建包含以下内容的新文件。 使用名称 **azuredeploy.parameters.dev.json** 保存文件。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "devstore"
    },
    "storageSKU": {
      "value": "Standard_LRS"
    },
    "appServicePlanName": {
      "value": "devplan"
    },
    "webAppName": {
      "value": "devapp"
    },
    "resourceTags": {
      "value": {
        "Environment": "Dev",
        "Project": "Tutorial"
      }
    }
  }
}
```

此文件是用于开发环境的参数文件。 请注意，它将 Standard_LRS 用于存储帐户，使用 **dev** 前缀为资源命名，并将 **Environment** 标记设置为 **Dev**。

同样，创建包含以下内容的新文件。 使用名称 **azuredeploy.parameters.prod.json** 保存文件。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "contosodata"
    },
    "storageSKU": {
      "value": "Standard_GRS"
    },
    "appServicePlanName": {
      "value": "contosoplan"
    },
    "webAppName": {
      "value": "contosowebapp"
    },
    "resourceTags": {
      "value": {
        "Environment": "Production",
        "Project": "Tutorial"
      }
    }
  }
}
```

此文件是用于生产环境的参数文件。 请注意，它将 Standard_GRS 用于存储帐户，使用 **contoso** 前缀为资源命名，并将 **Environment** 标记设置为 **Production**。 在实际生产环境中，还需要将应用服务与非免费版 SKU 配合使用，但在本教程中，我们将继续使用该免费版 SKU。

## <a name="deploy-template"></a>部署模板

使用 Azure CLI 或 Azure PowerShell 来部署模板。

让我们创建两个新的资源组，对模板进行最终测试。 一个用于开发环境，一个用于生产环境。

首先，我们部署到开发环境。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "China East"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "China East"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.dev.json
```

---

现在，我们部署到生产环境。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "China North"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "China North"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.prod.json
```

---

## <a name="verify-deployment"></a>验证部署

可以通过在 Azure 门户中浏览资源组来验证部署。

1. 登录到 [Azure 门户](https://portal.azure.cn)。
1. 在左侧菜单中选择“资源组”。 
1. 可以看到在本教程中部署的两个新资源组。
1. 选择任一资源组，查看部署的资源。 请注意，这些资源与我们在参数文件中为该环境指定的值匹配。

## <a name="clean-up-resources"></a>清理资源

1. 在 Azure 门户上的左侧菜单中选择“资源组”  。
2. 在“按名称筛选”字段中输入资源组名称。  如果已完成此系列，则需删除三个资源组 - myResourceGroup、myResourceGroupDev 和 myResourceGroupProd。
3. 选择资源组名称。
4. 在顶部菜单中选择“删除资源组”。 

<!--Pending on ## Next steps-->

<!-- Update_Description: update meta properties, wording update, update link -->