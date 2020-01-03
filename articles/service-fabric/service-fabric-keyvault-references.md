---
title: Azure Service Fabric - 使用 Service Fabric 应用程序 KeyVault 引用 | Azure
description: 本文介绍如何使用应用程序机密的 Service Fabric KeyVaultReference 支持。
services: service-fabric
author: rockboyfor
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
origin.date: 09/20/2019
ms.date: 12/09/2019
ms.author: v-yeche
ms.openlocfilehash: 7319a609765e65a456ec1bc6454b0e0560e6e3f5
ms.sourcegitcommit: 4a09701b1cbc1d9ccee46d282e592aec26998bff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75348557"
---
# <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Service Fabric 应用程序的 KeyVaultReference 支持（预览版）

构建云应用程序时，一个常见的难题是如何安全存储应用程序所需的机密。 例如，你可能想要将容器存储库凭据存储在 keyvault 中，并在应用程序清单中引用它。 Service Fabric KeyVaultReference 使用 Service Fabric 托管标识，并为引用 keyvault 机密提供方便。 本文的余下内容将详细介绍如何使用 Service Fabric KeyVaultReference，并提供一些典型用法。

## <a name="prerequisites"></a>先决条件

- 应用程序的托管标识 (MIT)
    
    Service Fabric KeyVaultReference 支持使用应用程序的托管标识，因此，计划使用 KeyVaultReferences 的应用程序应使用托管标识。 可遵循[此文档](concepts-managed-identity.md)为应用程序启用托管标识。

- 中心机密存储 (CSS)。

    中心机密存储 (CSS) 是 Service Fabric 的已加密本地机密缓存，一旦提取 KeyVaultReference，就会将其缓存在 CSS 中。

    将以下内容添加到群集配置中的 `fabricSettings` 下，即可为 KeyVaultReference 支持启用所需的所有功能。

    ```json
    "fabricSettings": 
    [
        ...
        {
            "name":  "CentralSecretService",
            "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
            ]
        },
        {
            "name":  "ManagedIdentityTokenService",
            "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
            ]
        }
    ]
    ```

    > [!NOTE] 
    > 建议对 CSS 使用单独的加密证书。 可将此证书添加到“CentralSecretService”节下。

    ```json
    {
        "name": "EncryptionCertificateThumbprint",
        "value": "<EncryptionCertificateThumbprint for CSS>"
    }
    ```

- 向应用程序的托管标识授予对 keyvault 的访问权限

    参考[此文档](how-to-grant-access-other-resources.md)来了解如何向托管标识授予对 keyvault 的访问权限。 另请注意，如果使用系统分配的托管标识，则只会在部署应用程序之后才创建托管标识。

## <a name="keyvault-secret-as-application-parameter"></a>用作应用程序参数的 Keyvault 机密
假设应用程序需要读取存储在 keyvault 中的后端数据库密码，Service Fabric KeyVaultReference 支持可以轻松实现此目的。 以下示例使用 Service Fabric KeyVaultReference 支持从 keyvault 中读取 `DBPassword` 机密。

- 将一个节添加到 settings.xml

    定义类型为 `KeyVaultReference`、值为 `<KeyVaultURL>` 的 `DBPassword` 参数

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.cn/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- 在 `<ConfigPackagePolicies>` 中引用 ApplicationManifest.xml 中的新节

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->

            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->

            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->

        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- 在应用程序中使用 KeyVaultReference

    服务实例化时，Service Fabric 将使用应用程序的托管标识来解析 KeyVaultReference 参数。 `<Section  Name=dbsecrets>` 下面列出的每个参数将是 EnvironmentVariable SecretPath 指向的文件夹下的某个文件。 以下 C# 代码片段演示如何在应用程序中读取 DBPassword。

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > 对于容器方案，可以使用 MountPoint 来控制 `secrets` 的装载位置。

## <a name="keyvault-secret-as-environment-variable"></a>用作环境变量的 Keyvault 机密

Service Fabric 环境变量现在支持 KeyVaultReference 类型。以下示例演示如何将环境变量绑定到 KeyVault 中存储的机密。

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.cn/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>用作容器存储库密码的 Keyvault 机密
KeyVaultReference 是容器 RepositoryCredentials 支持的类型。以下示例演示如何使用 keyvault 引用作为容器存储库密码。
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.cn/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>常见问题
- 需要为 KeyVaultReference 支持启用托管标识，如果在未启用托管标识的情况下使用 KeyVaultReference，应用程序激活将会失败。

- 如果使用系统分配的标识，则只会在部署应用程序之后才创建该标识，而这会造成循环依赖关系。 部署应用程序后，可向系统分配的标识授予对 keyvault 的访问权限。 可按名称 {cluster}/{application name}/{servicename} 查找系统分配的标识。

- keyvault 需与 Service Fabric 群集位于同一订阅中。 

## <a name="next-steps"></a>后续步骤

* [Azure KeyVault 文档](/key-vault/)

<!-- Update_Description: new article about service fabric keyvault references -->
<!--NEW.date: 12/09/2019-->