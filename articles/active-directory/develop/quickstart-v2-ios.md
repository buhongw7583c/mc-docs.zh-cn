---
title: Microsoft 标识平台 iOS 和 macOS 快速入门 | Azure
description: 了解如何在 iOS 或 macOS 应用程序中将用户登录并查询 Microsoft Graph。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: v-junlch
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: 4be9182b96f1b6c80df443bcbfa0f6d1e3efe61a
ms.sourcegitcommit: a4a2521da9b29714aa6b511fc6ba48279b5777c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82126481"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>快速入门：从 iOS 或 macOS 应用将用户登录并调用 Microsoft Graph API

本快速入门包含一个代码示例，该示例演示了本机 iOS 或 macOS 应用程序如何使用 Microsoft 标识平台登录工作和学校帐户、获取访问令牌并调用 Microsoft Graph API。

本快速入门适用于 iOS 和 macOS 应用。 某些步骤只是 iOS 应用所需的。 这些步骤会指出它们仅适用于 iOS。

![显示本快速入门生成的示例应用的工作原理](./media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **先决条件**
> * XCode 10+
> * iOS 10+
> * macOS 10.12+

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>注册并下载快速入门应用
> 可以使用两个选项来启动快速入门应用程序：
> * [快速][选项 1：注册并自动配置应用，然后下载代码示例](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [手动][选项 2：注册并手动配置应用程序和代码示例](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>选项 1：注册并自动配置应用，然后下载代码示例
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
> 若要注册应用，请执行以下操作：
> 1. 转到新的 [Azure 门户 - 应用注册](https://portal.azure.cn/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs)窗格。
> 1. 输入应用程序的名称并选择“注册”  。
> 1. 遵照说明下载内容，并只需单击一下自动配置新应用程序。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>选项 2：注册并手动配置应用程序和代码示例
>
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
> 若要手动注册应用程序并将应用的注册信息添加到解决方案，请执行以下步骤：
>
> 1. 导航到面向开发人员的 Microsoft 标识平台的[应用注册](https://portal.azure.cn/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)页。
> 1. 选择“新注册”。 
> 1. “注册应用程序”页出现后，请输入应用程序的注册信息： 
>      - 在“名称”  部分输入一个当应用用户登录应用或进行应用许可时会显示给应用用户的有意义的应用程序名称。
>      - 跳过此页上的其他配置。
>      - 选择 `Register`。
> 1. 在“管理”部分选择 `Authentication` > `Add Platform` > `iOS`。 
>      - 输入应用程序的***捆绑标识符***。 捆绑标识符只是一个用于唯一标识应用程序的唯一字符串，例如 `com.<yourname>.identitysample.MSALMacOS`。 记下所用的值。
>      - 请注意，iOS 配置也适用于 macOS 应用程序。
> 1. 选择 `Configure` 并保存“MSAL 配置”详细信息，供稍后在本快速入门中使用。
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>步骤 1：配置应用程序
> 若要正常运行本快速入门中的代码示例，需要添加与 Auth 代理兼容的重定向 URI。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [执行此更改]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](./media/quickstart-v2-ios/green-check.png) 应用程序已使用这些属性进行了配置

#### <a name="step-2-download-the-sample-project"></a>步骤 2：下载示例项目

- [下载用于 iOS 的代码示例](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [下载用于 macOS 的代码示例](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>步骤 3：安装依赖项

在终端窗口中导航到已下载代码示例所在的文件夹，然后运行 `pod install` 以安装最新的 MSAL 库。

#### <a name="step-4-configure-your-project"></a>步骤 4：配置项目

> [!div renderon="docs"]
> 如果选择了上面的“选项 1”，则可跳过这些步骤。

> [!div renderon="portal" class="sxs-lookup"]
> 1. 解压缩 zip 文件并在 XCode 中打开该项目。
> 1. 编辑 **ViewController.swift** 并将以“let kClientID”开头的行替换为以下代码片段。 记住将 `kClientID` 的值更新为客户端 ID，该 ID 是你在本快速入门的前面部分通过门户注册应用时保存的：
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. 编辑 **ViewController.swift** 并将以“let kAuthority”开头的行替换为以下代码片段：
>    ```swift
>    let kAuthority = "Enter_the_Authority_Endpoint_Host_HereEnter_the_Tenant_Info_Here"
>    ```
> 1. 编辑 **ViewController.swift** 并将以“let kGraphEndpoint”开头的行替换为以下代码片段：
>    ```swift
>    let kGraphEndpoint = "Enter_the_MS_Graph_Endpoint_Host_Here"
>    ```
> 1. 打开项目设置。 在“标识”部分  ，输入以前在门户中输入的**捆绑标识符**。
> 1. （仅适用于 iOS）右键单击 **Info.plist**，然后选择“打开为”   > “源代码”。 
> 1. （仅适用于 iOS）在 dict 根节点下，将 `CFBundleURLSchemes` 替换为你在门户中输入的***捆绑 ID***。
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>    ```
> 1. 生成并运行应用！
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
>
> 1. 解压缩 zip 文件并在 XCode 中打开该项目。
> 1. 编辑 **ViewController.swift** 并将以“let kClientID”开头的行替换为以下代码片段。 记住将 `kClientID` 的值更新为客户端 ID，该 ID 是你在本快速入门的前面部分通过门户注册应用时保存的：
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. 如果为 [Azure AD 国家云](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints)生成应用，请将以“let kGraphEndpoint”和“let kAuthority”开头的行替换为正确的终结点。 若要进行全局访问，请使用默认值：
>     ```objective-c
>     let kGraphEndpoint = "https://microsoftgraph.chinacloudapi.cn/"
>     let kAuthority = "https://login.partner.microsoftonline.cn/common"
>     ```
> 1. [此处](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints)阐述了其他终结点。 例如，若要使用 Azure AD 德国云运行本快速入门，请使用以下代码：
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. 打开项目设置。 在“标识”部分  ，输入以前在门户中输入的**捆绑标识符**。
> 1. （仅适用于 iOS）右键单击 **Info.plist**，然后选择“打开为”   > “源代码”。 
> 1. （仅适用于 iOS）在 dict 根节点下，将 `Enter_the_bundle_Id_Here` 替换为你在门户中使用的***捆绑 ID***。
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. 生成并运行应用！

## <a name="more-information"></a>更多信息

阅读以下各部分来详细了解本快速入门。

### <a name="get-msal"></a>获取 MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) 是一个库，用于用户登录和请求令牌，此类令牌用于访问受 Microsoft 标识平台保护的 API。 可以使用以下过程将 MSAL 添加到应用程序中：

```
$ vi Podfile

```
将以下代码添加到 podfile（使用项目的目标）：

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

运行 CocoaPods 安装命令：

```pod install```

### <a name="initialize-msal"></a>初始化 MSAL

可以通过添加以下代码，为 MSAL 添加引用：

```swift
import MSAL
```

然后，使用以下代码对 MSAL 进行初始化：

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)

let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |其中： ||
> |---------|---------|
> | `clientId` | 在 *portal.azure.cn* 中注册的应用程序的应用程序 ID |
> | `authority` | Microsoft 标识平台终结点。 在大多数情况下，这将是 *https<span/>://login.partner.microsoftonline.cn/common* |
> | `redirectUri` | 应用程序的重定向 URI。 可以传递“nil”以使用默认值，或传递自定义的重定向 URI。 |

### <a name="for-ios-only-additional-app-requirements"></a>（仅适用于 iOS）其他应用要求

应用还必须在 `AppDelegate` 中包含以下内容。 这样就可以在你进行身份验证时让 MSAL SDK 处理来自身份验证代理应用的令牌响应。

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> 在 iOS 13+ 上，如果采用 `UISceneDelegate` 而不是 `UIApplicationDelegate`，请改将此代码置于 `scene:openURLContexts:` 回调中（请参阅 [Apple 的文档](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)）。
> 如果支持兼容旧版 iOS 的 UISceneDelegate 和 UIApplicationDelegate，则需将 MSAL 回调置于两个位置。

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {

        guard let urlContext = URLContexts.first else {
            return
        }

        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication

        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

最后，应用必须在 ***Info.plist*** 中有一个与 `CFBundleURLTypes` 一起的 `LSApplicationQueriesSchemes` 条目。 该示例包含了此内容。

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>登录用户与请求令牌

MSAL 有两种用来获取令牌的方法：`acquireToken` 和 `acquireTokenSilent`。

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken：以交互方式获取令牌

在某些情况下，用户必须与 Microsoft 标识平台交互。 对于这种情况，最终用户可能需要选择其帐户并输入其凭据，或许可应用的权限。 例如，

* 用户首次登录应用程序
* 用户在重置其密码时需输入其凭据。
* 当应用程序首次请求资源的访问权限时
* 需要 MFA 或其他条件访问策略时

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |其中：||
> |---------|---------|
> | `scopes` | 包含所请求的范围（即针对 Microsoft Graph 的 `["https://microsoftgraph.chinacloudapi.cn/user.read"]` 或针对自定义 Web API (`api://<Application ID>/access_as_user`) 的 `[ "<Application ID URL>/scope" ]`） |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent：以无提示方式获取访问令牌

应用不应该要求其用户每次请求令牌时都要登录。 如果用户已登录，则此方法允许应用以无提示方式请求令牌。

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |其中： ||
> |---------|---------|
> | `scopes` | 包含所请求的范围（即针对 Microsoft Graph 的 `["https://microsoftgraph.chinacloudapi.cn/user.read"]` 或针对自定义 Web API (`api://<Application ID>/access_as_user`) 的 `[ "<Application ID URL>/scope" ]`） |
> | `account` | 正在请求其令牌的帐户。 本快速入门介绍单帐户应用程序。 如果要构建多帐户应用，则需要定义相关逻辑，以使用 `accountsFromDeviceForParameters:completionBlock:` 并传递正确的 `accountIdentifier` 来标识用于令牌请求的帐户 |

## <a name="next-steps"></a>后续步骤

试用 iOS 和 macOS 教程，了解有关如何构建应用程序的完整分步指南，包括本快速入门的完整说明。

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>了解如何创建本快速入门中使用的应用程序

> [!div class="nextstepaction"]
> [调用 Graph API iOS 教程](tutorial-v2-ios.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

