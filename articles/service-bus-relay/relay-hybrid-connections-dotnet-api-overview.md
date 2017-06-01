---
title: "Azure 中继 .NET 标准 API 概述 | Azure"
description: "中继 .NET 标准 API 概述"
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: jotaub
ms.translationtype: Human Translation
ms.sourcegitcommit: 457fc748a9a2d66d7a2906b988e127b09ee11e18
ms.openlocfilehash: 0d351d292bd328cc0af0e579ce8da5da0bc7446a
ms.contentlocale: zh-cn
ms.lasthandoff: 05/05/2017

---

# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Azure 中继混合连接 .NET 标准 API 概述
本文汇总了一些重要的 Azure 中继混合连接 .NET 标准[客户端 API](https://docs.microsoft.com/dotnet/api/microsoft.azure.relay)。
  
## <a name="relay-connection-string-builder"></a>中继连接字符串生成器
[RelayConnectionStringBuilder][RelayConnectionStringBuilder] 类将对特定于中继混合连接的连接字符串进行格式化。 该类可用于验证连接字符串的格式或从头开始生成连接字符串。 有关示例，请参阅以下内容。

```csharp
var endpoint = "{Relay namespace}";
var entityPath = "{Name of the Hybrid Connection}";
var sharedAccessKeyName = "{SAS key name}";
var sharedAccessKey = "{SAS key value}";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

还可以将连接字符串直接传递给 `RelayConnectionStringBuilder` 方法。 这样便能验证连接字符串的格式是否有效，并且如果任一参数无效，构造函数将引发 `ArgumentException`。

```csharp
var myConnectionString = "{RelayConnectionString}";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>混合连接流
无论使用的是 [HybridConnectionClient][HCClient]，还是 [HybridConnectionListener][HCListener]，[HybridConnectionStream][HCStream] 类这一主要对象均用于从 Azure 中继终结点发送和接收数据。

### <a name="getting-a-hybrid-connection-stream"></a>获取混合连接流

#### <a name="listener"></a>侦听器
使用 [HybridConnectionListener][HCListener] 可以获取 `HybridConnectionStream`，如下所示：

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>客户端
使用 [HybridConnectionClient][HCClient]，可以获取 `HybridConnectionStream`，如下所示：

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>接收数据
[HybridConnectionStream][HCStream] 类允许进行双向通信。 在大多数使用情况下，都需要持续地从流接收信息。 如果正在从流读取文本，则还需使用 [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx)，以便于分析数据。 例如，可以将数据读取为文本，而不能读取为 `byte[]`。

以下代码可从流中读取各行文本，直到请求取消为止。

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>发送数据
建立连接后，即可将消息发送到中继终结点。 由于连接对象继承 [Stream](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx)，因此以 `byte[]` 形式发送数据。 以下示例介绍如何执行此操作：

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

但是，如果要直接发送文本，而无需每次都对字符串进行编码，则可以使用 [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) 对象包装 `hybridConnectionStream` 对象。

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 中继的详细信息，请访问以下链接：

* [Microsoft.Azure.Relay reference](https://docs.microsoft.com/dotnet/api/microsoft.azure.relay)
* [什么是 Azure 中继？](./relay-what-is-it.md)
* [可用的中继 API](./relay-api-overview.md)

[RelayConnectionStringBuilder]: https://docs.microsoft.com/dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: https://docs.microsoft.com/dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: https://docs.microsoft.com/dotnet/api/microsoft.azure.relay.hybridconnectionlistener