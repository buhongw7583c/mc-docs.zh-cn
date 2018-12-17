---
title: 修复 Azure Stack 的证书问题 | Microsoft Docs
description: 使用 Azure Stack 就绪性检查器查看和修复证书问题。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
origin.date: 05/08/2018
ms.date: 10/15/2018
ms.author: v-jay
ms.reviewer: ''
ms.openlocfilehash: 6f7afbd5cba702e5113e2a5f323a3fcb6b350a0a
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52666939"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>修复 Azure Stack PKI 证书的常见问题
可以通过本文中的信息来了解并解决 Azure Stack PKI 证书的常见问题。 可以使用 Azure Stack 就绪性检查器工具来[验证 Azure Stack PKI 证书](azure-stack-validate-pki-certs.md)，以便发现问题。 此工具检查的目的是确保证书满足有关 Azure Stack 部署和 Azure Stack 机密轮换的 PKI 要求，结果记录在 [report.json 文件](azure-stack-validation-report.md)中。  

## <a name="pfx-encryption"></a>PFX 加密
**失败** - PFX 加密不是 TripleDES-SHA1。   
**补救** - 使用 **TripleDES-SHA1** 加密导出 PFX 文件。 从证书管理单元导出或使用 Export-PFXCertificate 时，这是所有 Windows 10 客户端的默认设置。 

## <a name="read-pfx"></a>读取 PFX
**警告** - 密码仅保护证书中的私密信息。  
**修复** - 建议使用可选设置“启用证书隐私”来导出 PFX 文件。  

**失败** - PFX 文件无效。  
**修复** - 使用[准备用于部署的 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤重新导出证书。

## <a name="signature-algorithm"></a>签名算法
**失败** - 签名算法为 SHA1。    
**修复** - 执行“生成 Azure Stack 证书签名请求”中的步骤，使用签名算法 SHA256 重新生成证书签名请求 (CSR)。 然后向证书颁发机构重新提交 CSR，要求其重新颁发证书。

## <a name="private-key"></a>私钥
**失败** - 私钥缺失或者不包含本地计算机属性。  
**修复** - 在生成 CSR 的计算机中，使用“准备用于部署的 Azure Stack PKI 证书”中的步骤重新导出证书。 这些步骤包括从本地计算机证书存储进行导出。

## <a name="certificate-chain"></a>证书链
**失败** - 证书链不完整。  
**修复** - 证书应包含完整的证书链。  使用[准备用于部署的 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤重新导出证书，然后选择选项“包括证书路径中的所有证书(如果可能)”。

## <a name="dns-names"></a>DNS 名称
**失败** - 证书上的 DNSNameList 不包含 Azure Stack 服务终结点名称或有效的通配符匹配项。  通配符匹配项仅适用于 DNS 名称最左侧的命名空间。 例如，_*.region.domain.com_ 仅适用于 *portal.region.domain.com*，不适用于 _*.table.region.domain.com_。  
**修复** - 执行“生成 Azure Stack 证书签名请求”中的步骤，以便使用为 Azure Stack 终结点提供支持所需的正确 DNS 名称重新生成 CSR。 将 CSR 重新提交给证书颁发机构，然后执行[准备用于部署的 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤，以便从生成了 CSR 的计算机导出证书。  

## <a name="key-usage"></a>密钥用法
**失败** - 密钥用法缺少数字签名或密钥加密，或者增强型密钥用法缺少服务器身份验证或客户端身份验证。  
**修复** - 执行[生成 Azure Stack 证书签名请求](azure-stack-get-pki-certs.md)中的步骤，以便使用正确的密钥用法属性重新生成 CSR。  将 CSR 重新提交给证书颁发机构，确认证书模板未覆盖请求中的“密钥用法”。

## <a name="key-size"></a>密钥大小
**失败** - 密钥大小不到 2048    
**修复** - 执行[生成 Azure Stack 证书签名请求](azure-stack-get-pki-certs.md)中的步骤，以便使用正确的密钥长度 (2048) 重新生成 CSR，然后将 CSR 重新提交给证书颁发机构。

## <a name="chain-order"></a>链序
**失败** - 证书链的顺序不正确。  
**修复** - 使用[准备用于部署的 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤重新导出证书，然后选择选项“包括证书路径中的所有证书(如果可能)”。 确保仅选择分支证书进行导出。 

## <a name="other-certificates"></a>其他证书
**失败** - PFX 包包含的证书不是分支证书，也不是证书链的一部分。  
**修复** - 使用[准备用于部署的 Azure Stack PKI 证书](azure-stack-prepare-pki-certs.md)中的步骤重新导出证书，然后选择选项“包括证书路径中的所有证书(如果可能)”。 确保仅选择分支证书进行导出。

## <a name="fix-common-packaging-issues"></a>修复常见的打包问题
AzsReadinessChecker 可以通过导入和导出 PFX 文件来修复常见的打包问题，其中包括： 
 - *PFX 加密*不是 TripleDES-SHA1
 - 私钥缺少本地计算机属性。
 - 证书链不完整或错误。 （如果 PFX 包不包含证书链，则本地计算机必须包含。） 
 - *其他证书*。
但是，如果需要生成新的 CSR 并重新颁发证书，则 AzsReadinessChecker 无用。 

### <a name="prerequisites"></a>先决条件
在运行此工具的计算机上，必须满足以下先决条件： 
 - Windows 10 或 Windows Server 2016，具有 Internet 连接。
 - PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell cmd，然后查看主要版本和次要版本。

   > `$PSVersionTable.PSVersion`
 - 配置[适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 
 - 下载最新版本的 [Azure Stack 就绪性检查器](https://aka.ms/AzsReadinessChecker)工具。

### <a name="import-and-export-an-existing-pfx-file"></a>导入和导出现有的 PFX 文件
1. 在满足先决条件的计算机上，打开一个管理性的 PowerShell 提示符，然后运行以下命令来安装 AzsReadinessChecker  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. 从 PowerShell 提示符下，运行以下命令来设置 PFX 密码。 将 *PFXpassword* 替换为实际密码。 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. 从 PowerShell 提示符下，运行以下命令来导出新的 PFX 文件。
   - 对于 *-PfxPath*，请指定要处理的 PFX 文件的路径。  在以下示例中，路径为 *.\certificates\ssl.pfx*。
   - 对于 *-ExportPFXPath*，请指定要导出的 PFX 文件的位置和名称。  在以下示例中，路径为 *.\certificates\ssl_new.pfx*

   > `Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. 在工具完成相关操作后，查看成功后的输出：![结果](./media/azure-stack-remediate-certs/remediate-results.png)

## <a name="next-steps"></a>后续步骤
[详细了解 Azure Stack 安全性](azure-stack-rotate-secrets.md)