---
title: 调用 HTTP 和 HTTPS 终结点
description: 使用 Azure 逻辑应用将传出请求发送到 HTTP 和 HTTPS 终结点
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
origin.date: 07/05/2019
ms.date: 03/09/2020
ms.author: v-yeche
tags: connectors
ms.openlocfilehash: 0f82bfd4a2339fb1abfeb08be9f0d16b88a73146
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "78304656"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用将传出呼叫发送到 HTTP 或 HTTPS 终结点

使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和内置 HTTP 触发器或操作，可以创建自动化任务和工作流，定期将请求发送到任何 HTTP 或HTTPS 终结点。 若要改为接收和响应传入 HTTP 或 HTTPS 调用，请使用内置[请求触发器或响应操作](../connectors/connectors-native-reqres.md)。

例如，可按指定的计划检查该终结点，来监视网站的服务终结点。 当该终结点上发生特定的事件（例如，网站关闭）时，该事件会触发逻辑应用的工作流并运行指定的操作。

若要按定期计划检查或轮询某个终结点，可将 HTTP 触发器用作工作流中的第一个步骤。  每次检查时，该触发器会向该终结点发送调用或请求。  该终结点的响应确定了逻辑应用的工作流是否运行。 触发器将响应中的任何内容传递到逻辑应用中的操作。

可将 HTTP 操作用作工作流中的另一个步骤，用于调用所需的终结点。 该终结点的响应确定了工作流剩余操作的运行方式。

根据目标终结点的功能，HTTP 连接器支持传输层安全性 (TLS) 版本 1.0、1.1 和 1.2。 逻辑应用通过使用可能支持的最高版本与终结点协商。 因此，例如，如果终结点支持 1.2 版，则连接器首先使用 1.2 版。 否则，连接器将使用下一个受支持的最高版本。

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个 Azure 试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

* 要调用的目标终结点的 URL

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

* 要从中调用目标终结点的逻辑应用。 若要从 HTTP 触发器开始，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP 操作，请使用所需的任何触发器启动逻辑应用。 此示例的第一步是使用 HTTP 触发器。

## <a name="add-an-http-trigger"></a>添加 HTTP 触发器

此内置触发器对终结点的指定 URL 发出 HTTP 调用，并返回响应。

1. 登录 [Azure 门户](https://portal.azure.cn)。 在逻辑应用设计器中打开空白逻辑应用。

1. 在“选择操作”  下的搜索框中，输入“http”作为筛选器。 在“触发器”列表中，选择“HTTP”触发器。  

    ![选择 HTTP 触发器](./media/connectors-native-http/select-http-trigger.png)

    此示例将触发器重命名为“HTTP trigger”，使步骤的名称更具描述性。 此外，该示例稍后会添加 HTTP 操作，因此，两个名称必须唯一。

1. 提供要包含在目标终结点调用中的 [HTTP 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)的值。 设置重复周期，以确定触发器检查目标终结点的频率。

    ![输入 HTTP 触发器参数](./media/connectors-native-http/http-trigger-parameters.png)

    如果选择的身份验证类型不是“None”  ，则身份验证设置将根据你的选择而有所不同。 有关 HTTP 可用的身份验证类型的详细信息，请参阅以下主题：

    * [向出站调用添加身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
    * 使用托管标识对资源访问者进行身份验证
    
    <!--Not Available on [Authenticate access to resources with managed identities](../logic-apps/create-managed-service-identity.md)-->

1. 若要添加其他可用参数，请打开“添加新参数”列表，并选择所需的参数。 

1. 继续使用触发器激发时运行的操作生成逻辑应用的工作流。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上，选择“保存”  。

## <a name="add-an-http-action"></a>添加 HTTP 操作

此内置操作对终结点的指定 URL 发出 HTTP 调用，并返回响应。

1. 登录 [Azure 门户](https://portal.azure.cn)。 在逻辑应用设计器中打开逻辑应用。

    此示例的第一步是使用 HTTP 触发器。

1. 在要添加 HTTP 操作的步骤下，选择“新建步骤”。 

    若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号 ( **+** )，然后选择“添加操作”。 

1. 在“选择操作”  下的搜索框中，输入“http”作为筛选器。 在“操作”列表中，选择“HTTP”操作。  

    ![选择“HTTP”操作](./media/connectors-native-http/select-http-action.png)

    此示例将操作重命名为“HTTP action”，使步骤的名称更具描述性。

1. 提供要包含在目标终结点调用中的 [HTTP 操作参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)的值。

    ![输入 HTTP 操作参数](./media/connectors-native-http/http-action-parameters.png)

    如果选择的身份验证类型不是“None”  ，则身份验证设置将根据你的选择而有所不同。 有关 HTTP 可用的身份验证类型的详细信息，请参阅以下主题：

    * [向出站调用添加身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
    * 使用托管标识对资源访问者进行身份验证
    
    <!--Not Available on [Authenticate access to resources with managed identities](../logic-apps/create-managed-service-identity.md)-->

1. 若要添加其他可用参数，请打开“添加新参数”列表，并选择所需的参数。 

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上，选择“保存”  。

## <a name="content-with-multipartform-data-type"></a>具有多部分/表单数据类型的内容

若要处理 HTTP 请求中具有 `multipart/form-data` 类型的内容，可以使用此格式向 HTTP 请求的正文添加包含 `$content-type` 和 `$multipart` 属性的 JSON 对象。

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

例如，假设你有一个逻辑应用，它使用该站点的 API（支持 `multipart/form-data` 类型）向网站发送对 Excel 文件的 HTTP POST 请求。 下面是此操作的可能外观：

![多部分表单数据](./media/connectors-native-http/http-action-multipart.png)

以下是在基础工作流定义中显示 HTTP 操作的 JSON 定义的同一示例：

```json
{
   "HTTP_action": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>连接器参考

有关触发器和操作参数的详细信息，请参阅以下部分：

* [HTTP 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP 操作参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>输出详细信息

下面是有关 HTTP 触发器或操作的输出的详细信息，输出中将返回以下信息：

| 属性名称 | 类型 | 说明 |
|---------------|------|-------------|
| headers | 对象 (object) | 请求中的标头 |
| body | 对象 (object) | JSON 对象 | 包含请求中正文内容的对象 |
| 状态代码 | int | 请求中的状态代码 |
|||

| 状态代码 | 说明 |
|-------------|-------------|
| 200 | OK |
| 202 | 已接受 |
| 400 | 错误的请求 |
| 401 | 未授权 |
| 403 | 禁止 |
| 404 | 未找到 |
| 500 | 内部服务器错误。 发生未知错误。 |
|||

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)

<!-- Update_Description: update meta properties, wording update, update link -->