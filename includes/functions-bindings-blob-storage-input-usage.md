---
title: include 文件
description: include 文件
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 02/17/2020
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 767ce65430403cf79ccfed185401ca021028eeaf
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "77494593"
---
可以将以下参数类型用于 blob 输入绑定：

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> function.json 中需有 "inout" 绑定 `direction` 或 C# 类库中需有  `FileAccess.ReadWrite`。

如果尝试绑定到某个存储 SDK 类型并收到错误消息，请确保已引用[正确的存储 SDK 版本](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)。

由于整个 Blob 内容都会加载到内存中，因此，只有当 Blob 较小时才建议绑定到 `string` 或 `Byte[]`。 平时，最好使用 `Stream` 或 `CloudBlockBlob` 类型。 有关详细信息，请参阅本文前文中的[并发和内存使用情况](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage)。

