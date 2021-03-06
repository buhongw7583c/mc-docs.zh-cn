---
title: 快速入门：使用 Visual Studio 在 Azure 中创建第一个函数
description: 本快速入门介绍如何使用 Visual Studio 创建并发布 HTTP 触发的 Azure 函数。
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/30/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: e4dd2c8ae57968301c91c2d6e1b8d31b663e2e84
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "80581787"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>快速入门：使用 Visual Studio 在 Azure 中创建第一个函数

Azure Functions 用于在无服务器环境中运行代码，无需先创建 VM 或发布 Web 应用程序。

本快速入门介绍如何使用 Visual Studio 2019 在本地创建并测试 HTTP 触发的 C# 函数应用“hello world”，然后将其发布到 Azure。 

![浏览器中的函数 localhost 响应](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

本快速入门专为 Visual Studio 2019 设计。 

## <a name="prerequisites"></a>先决条件

若要完成本教程，请先安装 [Visual Studio 2019](/downloads/)。 请确保在安装过程中选择“Azure 开发”  工作负载。 若要改为使用 Visual Studio 2017 创建 Azure Functions 项目，则必须先安装[最新的 Azure Functions 工具](functions-develop-vs.md#check-your-tools-version)。

![安装包含“Azure 开发”工作负载的 Visual Studio](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

如果没有 Azure 订阅，可在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="create-a-function-app-project"></a>创建函数应用项目

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio 将创建一个项目和一个包含 HTTP 触发器函数类型样本代码的类。 `FunctionName` 方法属性设置函数的名称（默认情况下为 `Function1`）。 `HttpTrigger` 属性指定该函数将由某个 HTTP 请求触发。 样本代码发送 HTTP 响应，其中包含来自请求正文或查询字符串的值。

使用输入和输出绑定来扩展函数的功能，具体的做法是将适当的属性应用于方法。 有关详细信息，请参阅 [Azure Functions C# 开发人员参考](functions-dotnet-class-library.md)的[触发器和绑定](functions-dotnet-class-library.md#triggers-and-bindings)部分。

创建函数项目和 HTTP 触发的函数后，可以在本地计算机上对其进行测试。

## <a name="run-the-function-locally"></a>在本地运行函数

Visual Studio 与 Azure Functions Core Tools 集成，这样你便可使用完整的 Azure Functions 运行时在本地测试函数。  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

确认该函数可以在本地计算机上正确运行以后，即可将项目发布到 Azure。

## <a name="publish-the-project-to-azure"></a>将项目发布到 Azure

必须在 Azure 订阅中有一个函数应用，然后才能发布项目。 Visual Studio 发布会在你首次发布项目时为你创建一个函数应用。

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>在 Azure 中测试函数

1. 从“发布”配置文件页复制函数应用的基 URL。  将 URL 的 `localhost:port` 部分（用于在本地测试函数）替换为新的基 URL。 将查询字符串 `?name=<YOUR_NAME>` 追加到此 URL 并运行请求。

    调用 HTTP 触发的函数的 URL 采用以下格式：

    `http://<APP_NAME>.chinacloudsites.cn/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. 将 HTTP 请求的这个新 URL 粘贴到浏览器的地址栏中。 下图演示该函数在浏览器中返回的、对远程 GET 请求的响应：

    ![浏览器中的函数响应](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用了 Visual Studio 在 Azure 中创建和发布了一个带有简单的 HTTP 触发函数的 C# 函数应用。 

请转到下一篇文章，了解如何添加一个绑定到函数的 Azure 存储队列：
> [!div class="nextstepaction"]
> [将 Azure 存储队列绑定添加到函数](functions-add-output-binding-storage-queue-vs.md)

<!-- Update_Description: wording update -->