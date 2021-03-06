---
title: 条件访问策略中的条件 - Azure Active Directory
description: Azure AD 条件访问策略中的条件是什么
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: v-junlch
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6fbc4f7150353bf3050a24cb51dae6ae8864efd
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "79291093"
---
# <a name="conditional-access-conditions"></a>条件访问：Conditions

在条件访问策略中，管理员可以利用风险、设备平台或位置等条件的信号来增强其策略决策。 

![定义条件访问策略并指定条件](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

可以结合多个条件来创建精细且具体的条件访问策略。

例如，在访问敏感应用程序时，管理员除了考虑其他控制措施（例如多重身份验证）以外，还可以在其访问决策中考虑到各个位置的登录风险信息。

## <a name="device-platforms"></a>设备平台

设备平台的特征根据设备上运行的操作系统而定。 Azure AD 使用设备（例如用户代理字符串）提供的信息来标识平台。 由于用户代理字符串可修改，因此此信息未经验证。 设备平台应与 Microsoft Intune 设备合规策略配合使用，或者作为块语句的一部分使用。 默认设置是将策略应用到所有设备平台。

Azure AD 条件访问支持以下设备平台：

- Android
- iOS
- Windows Phone
- Windows
- macOS

如果使用**其他客户端**条件来阻止旧身份验证，还可以设置设备平台条件。

## <a name="locations"></a>位置

将位置配置为条件时，组织可以选择包含或排除位置。 这些命名位置可以包含公共 IPv4 网络信息、国家或地区，甚至是不映射到特定国家或地区的未知区域。 只能将 IP 范围标记为受信任位置。

包含**任何位置**时，此选项将包含 Internet 上的任何 IP 地址，而不仅仅是包含配置的命名位置。 选择**任何位置**时，管理员可以选择排除**所有受信任的**或**选定的**位置。

例如，某些组织在其用户连接到受信任位置（例如其总部的实际位置）的网络时，可能会选择不要求执行多重身份验证。 管理员可以创建一个包含任何位置，但排除选定总部网络位置的策略。

有关位置的详细信息，可参阅 [Azure Active Directory 条件访问中的位置条件是什么](location-condition.md)一文。

## <a name="client-apps-preview"></a>客户端应用（预览）

条件访问策略默认应用到基于浏览器的应用程序，以及利用新式身份验证协议的应用程序。 除这些应用程序以外，管理员还可以选择包括 Exchange ActiveSync 客户端以及其他利用旧式协议的客户端。

- 浏览者
   - 这包括使用 SAML、WS 联合身份验证、OpenID Connect 等协议的基于 Web 的应用程序，或注册为 OAuth 机密客户端的服务。
- 移动应用和桌面客户端
   - 新式身份验证客户端
      - 此选项包括 Office 桌面和手机应用程序等应用程序。
   - Exchange ActiveSync 客户端
      - 默认情况下，这包括 Exchange ActiveSync (EAS) 协议的所有使用。 选择“仅将策略应用于受支持的平台”  会将平台限制为受支持的平台（如 iOS、Android 和 Windows）。
      - 当策略阻止使用 Exchange ActiveSync 时，受影响的用户将收到一封隔离电子邮件。 此电子邮件将提供受阻原因，并提供修正说明（如果可以修正）。
   - 其他客户端
      - 此选项包括使用那些不支持新式身份验证的基本/旧式身份验证协议的客户端。
         - 经身份验证的 SMTP - 由 POP 和 IMAP 客户端用来发送电子邮件。
         - 自动发现 - 由 Outlook 和 EAS 客户端用来查找和连接 Exchange Online 中的邮箱。
         - Exchange Online PowerShell - 用于通过远程 PowerShell 连接到 Exchange Online。 如果阻止 Exchange Online PowerShell 的基本身份验证，则需使用 Exchange Online PowerShell 模块进行连接。 有关说明，请参阅[使用多重身份验证连接到 Exchange Online PowerShell](https://docs.microsoft.com/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)。
         - Exchange Web 服务 (EWS) - Outlook、Outlook for Mac 和第三方应用使用的编程接口。
         - IMAP4 - 由 IMAP 电子邮件客户端使用。
         - 基于 HTTP 的 MAPI (MAPI/HTTP) - 由 Outlook 2010 及更高版本使用。
         - 脱机通讯簿 (OAB) - 通过 Outlook 下载并使用的地址列表集合的副本。
         - Outlook Anywhere（基于 HTTP 的 RPC）- 由 Outlook 2016 及更低版本使用。
         - Outlook 服务 - 由 Windows 10 的邮件和日历应用使用。
         - POP3 - 由 POP 电子邮件客户端使用。
         - Reporting Web Services - 用于在 Exchange Online 中检索报表数据。

需要使用托管设备、阻止旧式身份验证，以及阻止 Web 应用程序但允许移动应用或桌面应用时，通常会使用这些条件。

### <a name="supported-browsers"></a>支持的浏览器

此设置适用于所有浏览器。 但是，若要符合设备策略（如兼容设备要求），支持以下操作系统和浏览器：

| 操作系统 | 浏览器 |
| :-- | :-- |
| Windows 10 | Microsoft Edge、Internet Explorer、Chrome |
| Windows 8/8.1 | Internet Explorer、Chrome |
| Windows 7 | Internet Explorer、Chrome |
| iOS | Microsoft Edge、Intune Managed Browser、Safari |
| Android | Microsoft Edge、Intune Managed Browser、Chrome |
| Windows Phone | Microsoft Edge、Internet Explorer |
| Windows Server 2019 | Microsoft Edge、Internet Explorer、Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome、Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>为何我在浏览器中看到证书提示

在 Windows 7、iOS、Android 和 macOS 上，Azure AD 使用客户端证书来标识设备，该证书是在将设备注册到 Azure AD 时预配的。  用户首次通过浏览器登录时，系统会提示用户选择此证书。 用户必须在使用浏览器之前选择此证书。

#### <a name="chrome-support"></a>Chrome 支持

如需在 Windows 10 创意者更新（版本 1703）或更高版本中获得 Chrome 支持，请安装 Windows 10 Accounts 扩展  。 当条件访问策略需要特定于设备的详细信息时，此扩展是必需的。

若要自动将此扩展部署到 Chrome 浏览器，请创建以下注册表项：

|    |    |
| --- | --- |
| `Path` | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| 名称 | 1 |
| 类型 | REG_SZ (String) |
| 数据 | ppnbnpeolgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx |

对于 Windows 8.1 和 7  中的 Chrome 支持，请创建以下注册表项：

|    |    |
| --- | --- |
| `Path` | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| 名称 | 1 |
| 类型 | REG_SZ (String) |
| 数据 | {"pattern":"https://device.login.partner.microsoftonline.cn","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

这些浏览器支持设备身份验证，允许根据策略对设备进行识别和验证。 如果浏览器以专用模式运行，设备检查将失败。

### <a name="supported-mobile-applications-and-desktop-clients"></a>支持的移动应用程序和桌面客户端

组织可以选择**移动应用和桌面客户端**作为客户端应用。

此设置会影响通过以下移动应用和桌面客户端做出的访问尝试：

| 客户端应用 | 目标服务 | 平台 |
| --- | --- | --- |
| Dynamics CRM 应用 | Dynamics CRM | Windows 10、Windows 8.1、iOS 和 Android |
| “邮件/日历/联系人”应用、Outlook 2016、Outlook 2013（使用新式身份验证）| Office 365 Exchange Online | Windows 10 |
| 用于应用的 MFA 和位置策略。 不支持基于设备的策略。| 任何“我的应用”应用服务 | Android 和 iOS |
| Microsoft Teams 服务 - 控制支持 Microsoft Teams 及其所有客户端应用（Windows 桌面、iOS、Android、WP 和 Web 客户端）的所有服务 | Microsoft Teams | Windows 10、Windows 8.1、Windows 7、iOS、Android 和 macOS |
| Office 2016 应用、Office 2013（采用新式身份验证）、[OneDrive 同步客户端](https://docs.microsoft.com/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1、Windows 7 |
| Office 2016 应用、通用 Office 应用、Office 2013（采用新式身份验证）、[OneDrive 同步客户端](https://docs.microsoft.com/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016（仅限 Word、Excel、PowerPoint、OneNote）。 | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10、macOS |
| Office 移动应用 | Office 365 SharePoint Online | Android、iOS |
| Office Yammer 应用 | Office 365 Yammer | Windows 10、iOS、Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10、macOS |
| Outlook 2016 (Office for macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016、Outlook 2013（采用新式身份验证）、Skype for Business（采用新式身份验证） | Office 365 Exchange Online | Windows 8.1、Windows 7 |
| Outlook 移动应用 | Office 365 Exchange Online | Android、iOS |
| Power BI 应用 | Power BI 服务 | Windows 10、Windows 8.1、Windows 7、Android 和 iOS |
| Skype for Business | Office 365 Exchange Online| Android、iOS |
| Visual Studio Team Services 应用 | Visual Studio Team Services | Windows 10、Windows 8.1、Windows 7、iOS 和 Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync 客户端

- 组织在将策略分配给用户或组时，只能选择 Exchange ActiveSync 客户端。 选择“所有用户”、“所有来宾和外部用户”或“目录角色”会导致所有用户遭到阻止。   
- 创建分配给 Exchange ActiveSync 客户端的策略时，**Office 365 Exchange Online** 是唯一可分配给该策略的云应用程序。 
- 组织可以使用“设备平台”条件将此策略的范围缩小为特定的平台。 

如果分配给策略的访问控制使用“需要已批准的客户端应用”，则会将用户定向到相应的位置让他们安装并使用 Outlook 移动客户端。  如果需要**多重身份验证**，受影响的用户将被阻止，因为基本身份验证不支持多重身份验证。

有关详细信息，请参阅以下文章：

- [使用条件访问阻止旧式身份验证](block-legacy-authentication.md)
- [需要可以进行条件访问且已获批的客户端应用](app-based-conditional-access.md)

### <a name="other-clients"></a>其他客户端

通过选择“其他客户端”  ，可以指定一个条件，该条件会影响通过邮件协议（如 IMAP、MAPI、POP、SMTP）使用基本身份验证的应用和不使用新式身份验证的旧版 Office 应用。

## <a name="device-state-preview"></a>设备状态（预览）

设备状态条件可用于在组织的条件访问策略中，排除已加入混合 Azure AD 的设备和/或标记为符合 Microsoft Intune 合规策略的设备。

例如，访问“Azure 管理”云应用的“所有用户”，包括“所有设备状态”，排除“已加入混合 Azure AD 的设备”和“标记为合规的设备”，“访问控制”为“阻止”。        
   - 此示例将创建一个仅允许从已加入混合 Azure AD 的设备和/或标记为合规的设备访问 Azure 管理的策略。

## <a name="next-steps"></a>后续步骤

- [条件访问：授权](concept-conditional-access-grant.md)

- [条件访问常见策略](concept-conditional-access-policy-common.md)

