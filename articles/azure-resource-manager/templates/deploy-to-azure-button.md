---
title: “部署到 Azure”按钮
description: 使用此按钮从 GitHub 存储库部署 Azure 资源管理器模板。
ms.topic: conceptual
origin.date: 02/07/2020
ms.date: 03/09/2020
ms.author: v-yeche
ms.openlocfilehash: 7fb3f1199ddaa9d501abe640c6d86ab0d5378531
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "78850491"
---
<!--Verified successfully-->
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>使用部署按钮从 GitHub 存储库部署模板

本文介绍了如何使用“部署到 Azure”  按钮从 GitHub 存储库部署模板。 可以直接将此按钮添加到你的 GitHub 存储库中的 README.md 文件，也可以将其添加到引用该存储库的网页。

## <a name="use-common-image"></a>使用常用图像

若要将此按钮添加到网页或存储库，请使用以下图像：

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

此图像显示为：

![“部署到 Azure”按钮](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>创建用于部署模板的 URL

若要为模板创建 URL，请从存储库中模板的原始 URL 开始：

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

然后，对其进行 URL 编码。 可以使用联机编码器，也可以运行一个命令。 以下 PowerShell 示例展示了如何对值进行 URL 编码。

```powershell
[uri]::EscapeDataString($url)
```

进行 URL 编码后，示例 URL 具有以下值。

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

每个链接都以相同的基 URL 开头：

```html
https://portal.azure.cn/#create/Microsoft.Template/uri/
```

将进行 URL 编码后的模板链接添加到基 URL 的末尾。

```html
https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

你已具有该链接的完整 URL。

## <a name="create-deploy-to-azure-button"></a>创建“部署到 Azure”按钮

最后，将链接和图像放在一起。

若要向 GitHub 存储库中的 README.md 文件或者向网页中添加带 Markdown 的按钮，请使用：

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

对于 HTML，请使用：

```html
<a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>部署模板

若要测试整个解决方案，请选择以下按钮：

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

门户会显示一个窗格，你可以在其中轻松地提供参数值。 这些参数预先填充了来自模板的默认值。

![使用门户进行部署](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>后续步骤

- 若要详细了解模板，请参阅[了解 Azure 资源管理器模板的结构和语法](template-syntax.md)。

<!-- Update_Description: new article about deploy to azure button -->
<!--NEW.date: 03/09/2020-->