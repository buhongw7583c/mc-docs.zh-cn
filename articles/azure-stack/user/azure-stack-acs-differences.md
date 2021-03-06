---
title: Azure Stack Hub 存储差异和注意事项
description: 了解 Azure Stack Hub 存储与 Azure 存储之间的差异，以及 Azure Stack Hub 部署注意事项。
author: WenJason
ms.topic: conceptual
origin.date: 1/22/2020
ms.date: 02/24/2020
ms.author: v-jay
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: a573f5988b988383131291f1186d3c90cf8755ed
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "77540860"
---
# <a name="azure-stack-hub-storage-differences-and-considerations"></a>Azure Stack Hub 存储：差异和注意事项

Azure Stack Hub 存储是 Azure Stack Hub 中的一组存储云服务。 Azure Stack Hub 存储使用与 Azure 一致的语义来提供 Blob、表、队列和帐户管理功能。

本文汇总了 Azure Stack Hub 存储与 Azure 存储服务之间的已知差异。 它还列出了部署 Azure Stack Hub 时要考虑的事项。 若要了解全球 Azure 与 Azure Stack Hub 之间的大致差异，请参阅[重要注意事项](azure-stack-considerations.md)一文。

## <a name="cheat-sheet-storage-differences"></a>速查表：存储差异

| 功能 | Azure（中国） | Azure Stack Hub |
| --- | --- | --- |
|文件存储|支持基于云的 SMB 文件共享|尚不支持
|静态数据的 Azure 存储服务加密|256 位 AES 加密。 支持在密钥保管库中使用客户管理的密钥进行加密。|BitLocker 128 位 AES 加密。 不支持使用客户托管密钥进行加密。
|存储帐户类型|常规用途 V1、V2 和 Blob 存储帐户|仅常规用途 V1。
|复制选项|本地冗余存储、异地冗余存储和读取访问异地冗余存储|本地冗余存储。
|高级存储|提供高性能和低延迟存储。 仅支持高级存储帐户中的页 blob。|可预配，但无性能限制或保证。 不会在高级存储帐户中阻止使用块 blob、追加 blob、表和队列。
|托管磁盘|支持高级和标准版|使用版本 1808 或更高版本时支持。
|Blob 名称|1,024 个字符（2,048 字节）|880 个字符（1,760 字节）
|块 Blob 大小上限|4.75 TB（100 MB X 50,000 块）|4.75 TB（100 MB x 50,000 块），适用于 1802 更新或更高版本。 50,000 X 4 MB（约 195 GB），适用于以前的版本。
|页 Blob 快照复制|支持备份已附加到运行中 VM 的 Azure 非托管 VM 磁盘|尚不支持。
|页 Blob 增量快照复制|支持高级和标准 Azure 页 Blob|尚不支持。
|页 Blob 计费|唯一页面不管是在 Blob 中还是快照中都会产生费用。 在更新基 Blob 之前，不会对与 Blob 关联的快照额外收费。|收费针对基 Blob 及关联的快照。 会对每个单独的快照额外收费。
|适用于 Blob 存储的存储层|热存储层、冷存储层和存档存储层。|尚不支持。
|适用于 Blob 存储的软删除|公开发布|尚不支持。
|页 Blob 大小上限|8 TB|1 TB
|页 Blob 页面大小|512 字节|4 KB
|表分区键和行键大小|1,024 个字符（2,048 字节）|400 个字符（800 字节）
|Blob 快照|一个 blob 的最大快照数不受限制。|一个 blob 的最大快照数为 1,000。
|用于存储的 Azure AD 身份验证|处于预览状态|尚不支持。
|不可变 Blob|公开发布|尚不支持。
|针对存储的防火墙和虚拟网络规则|公开发布|尚不支持。|

存储指标也有一些差异：

* 存储指标中的事务数据不区分内部或外部网络带宽。
* 存储指标中的事务数据不包含虚拟机对所装载的驱动器的访问。

## <a name="api-version"></a>API 版本

使用 Azure Stack Hub 存储时支持以下版本：

Azure 存储服务 API：

1811 更新或更高版本：

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

以前的版本：

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Azure 存储服务管理 API：

1811 更新或更高版本：

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

以前的版本：

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="powershell-version"></a>PowerShell 版本

对于存储模块 PowerShell，请了解与 REST API 兼容的版本。 

| 模块 | 支持的版本 | 使用情况 |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure.Storage | [4.5.0](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0) | 管理 Azure Stack Hub 存储帐户中的 blob、队列和表 |
| AzureRM.Storage | [5.0.4](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.4) | 在 Azure Stack Hub 中创建和管理存储帐户 |


有关 Azure Stack Hub 支持的存储客户端库的详细信息，请参阅：[Azure Stack Hub 存储开发工具入门](azure-stack-storage-dev.md)。

## <a name="next-steps"></a>后续步骤

* [Azure Stack Hub 存储开发工具入门](azure-stack-storage-dev.md)
* [使用 Azure Stack Hub 存储的数据传输工具](azure-stack-storage-transfer.md)
* [Azure Stack Hub 存储简介](azure-stack-storage-overview.md)
