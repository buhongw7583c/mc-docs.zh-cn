---
title: 获取意向，C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: 此 C# 快速入门使用可用的公共 LUIS 应用从会话文本中确定用户的意向。
services: cognitive-services
author: lingliw
manager: digimobile
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 04/19/19
ms.author: v-lingwu
ms.openlocfilehash: 01a082cd79cf9fcba3bddc65275553d8bc443ab3
ms.sourcegitcommit: bf4c3c25756ae4bf67efbccca3ec9712b346f871
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65555498"
---
# <a name="quickstart-get-intent-using-c"></a>快速入门：使用 C# 获取意向

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>先决条件

* [Visual Studio Community 2017 edition](https://visualstudio.microsoft.com/vs/community/)
* C# 编程语言（包括在 VS Community 2017 中）
* 公共应用 ID：df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>获取 LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>使用浏览器获取意向

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>以编程方式获取意向

使用 C# 来查询预测终结点 GET [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) 以获取与在前面部分中在浏览器窗口看到的结果相同的结果。 

1. 在 Visual Studio 中创建新的控制台应用程序。 

    ![在 Visual Studio 中创建新的控制台应用程序](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. 在 Visual Studio 项目中，在解决方案资源管理器中，选择“添加引用”，然后从“程序集”选项卡中选择“System.Web”。

    ![选择“添加”引用，然后从“程序集”选项卡中选择 System.Web](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. 将 Program.cs 改写为以下代码：
    
```
using System;
using System.Net.Http;
using System.Web;

/*

    You can use the authoring key instead of the endpoint key. 
    The authoring key allows 1000 endpoint queries a month.

*/

namespace ConsoleLuisEndpointSample
{
    class Program
    {
        static void Main(string[] args)
        {
            MakeRequest();
            Console.WriteLine("Hit ENTER to exit...");
            Console.ReadLine();
        }

        static async void MakeRequest()
        {
            var client = new HttpClient();
            var queryString = HttpUtility.ParseQueryString(string.Empty);

            // This app ID is for a public sample app that recognizes requests to turn on and turn off lights
            var luisAppId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";
            var endpointKey = "YOUR_KEY";

            // The request header contains your subscription key
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", endpointKey);

            // The "q" parameter contains the utterance to send to LUIS
            queryString["q"] = "turn on the left light";

            // These optional request parameters are set to their default values
            queryString["timezoneOffset"] = "0";
            queryString["verbose"] = "false";
            queryString["spellCheck"] = "false";
            queryString["staging"] = "false";

            var endpointUri = "https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/" + luisAppId + "?" + queryString;
            var response = await client.GetAsync(endpointUri);

            var strResponseContent = await response.Content.ReadAsStringAsync();
            
            // Display the JSON result from LUIS
            Console.WriteLine(strResponseContent.ToString());
        }
    }
}
```

4. 将 `YOUR_KEY` 的值替换为你自己的 LUIS 密钥。

5. 生成并运行控制台应用程序。 其中会显示前面在浏览器窗口中显示的相同 JSON。

    ![控制台窗口显示 LUIS 中的 JSON 结果](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>LUIS 密钥

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，关闭 Visual Studio 项目并从文件系统中删除项目目录。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 C# 添加话语并进行训练](luis-get-started-cs-add-utterance.md)



