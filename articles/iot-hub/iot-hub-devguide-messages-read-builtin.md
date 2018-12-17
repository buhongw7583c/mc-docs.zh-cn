---
title: 了解 Azure IoT 中心内置终结点 | Azure
description: 开发人员指南 - 介绍如何使用与事件中心兼容的内置终结点读取设备到云的消息。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
origin.date: 07/18/2018
ms.author: v-yiso
ms.date: 12/03/2018
ms.openlocfilehash: b4ca8f0522997afa1e92129416b78b29c5ff3993
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52675235"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>从内置终结点读取设备到云的消息

默认情况下，消息将路由到与[事件中心](/event-hubs/
)兼容的内置面向服务的终结点 (**messages/events**) 中。 目前仅在端口 5671 上使用 [AMQP](https://www.amqp.org/) 协议公开此终结点。 IoT 中心公开以下属性，以便用户控制内置的与事件中心兼容的消息传送终结点 **messages/events**。

| 属性            | 说明 |
| ------------------- | ----------- |
| **分区计数** | 在创建时设置此属性，以便为设备到云事件引入定义[分区][lnk-event-hub-partitions]数。 |
| **保留时间**  | 此属性指定 IoT 中心保留消息的时间（以天为单位）。 默认值为一天，但可以增加到七天。 |

IoT 中心还支持用户管理内置设备到云接收终结点上的使用者组。

如果使用[消息路由](iot-hub-devguide-messages-d2c.md)，并启用了[回退路由](iot-hub-devguide-messages-d2c.md#fallback-route)，则与任何路由上的查询不匹配的所有消息都会写入内置终结点。 如果禁用此回退路由，将删除与任何查询都不匹配的消息。

可以使用 [IoT 中心资源提供程序 REST API][lnk-resource-provider-apis] 以编程方式修改保留期时间，或通过 [Azure 门户][lnk-management-portal]进行修改。

IoT 中心向后端服务公开 **messages/events** 内置终结点，让后端服务读取中心收到的设备到云消息。 该终结点与事件中心兼容，因此可以使用事件中心服务支持的任何机制读取消息。

## <a name="read-from-the-built-in-endpoint"></a>从内置终结点读取信息

使用[适用于 .NET 的 Azure 服务总线 SDK][lnk-servicebus-sdk] 或[事件中心 - 事件处理器主机](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)时，可以将任何 IoT 中心连接字符串与正确的权限配合使用。 然后使用**消息/事件**作为事件中心名称。

使用无法识别 IoT 中心的 SDK（或产品集成）时，必须检索与事件中心兼容的终结点和与事件中心兼容的名称：

1. 登录 [Azure 门户][lnk-management-portal]，并导航到 IoT 中心。
2. 单击“内置终结点”。

3. “事件”部分包含以下值：“与事件中心兼容的终结点”、“与事件中心兼容的名称”、“分区”、“保留时间”和“使用者组”。

    ![设备到云的设置](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

IoT 中心 SDK 需要 IoT 中心终结点名称，即“终结点”下所示的 messages/events。

如果当前使用的 SDK 需要“主机名”或“命名空间”值，请从“事件中心兼容的终结点”中删除方案。 例如，如果与事件中心兼容的终结点为 **sb://iothub-ns-myiothub-1234.servicebus.chinacloudapi.cn/**，则**主机名**为 **iothub-ns-myiothub-1234.servicebus.chinacloudapi.cn**。 命名空间为 iothub-ns-myiothub-1234。

然后，可以使用具有 **ServiceConnect** 权限的任何共享访问策略连接到指定的事件中心。

如果需要通过使用以前的信息构建事件中心连接字符串，请使用以下模式：

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

可用于 IoT 中心公开的事件中心兼容终结点的 SDK 和集成包括以下列表中的项目：

* [Java 事件中心客户端](https://github.com/Azure/azure-event-hubs-java)。
* [Apache Storm Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md)。 可以在 GitHub 上查看 [Spout 源代码](https://github.com/apache/storm/tree/master/external/storm-eventhubs) 。
* [Apache Spark 集成](../hdinsight/spark/apache-spark-eventhub-streaming.md)。

## <a name="next-steps"></a>后续步骤

* 有关 IoT 中心终结点的详细信息，请参阅 [IoT 中心终结点](iot-hub-devguide-endpoints.md)。

* [快速入门](quickstart-send-telemetry-node.md)介绍如何从模拟设备发送设备到云的消息，以及如何从内置终结点读取消息。 

有关更多详细信息，请参阅[使用路由处理 IoT 中心设备到云的消息](tutorial-routing.md)教程。

* 如果想要将设备到云的消息路由到自定义终结点，请参阅[对设备到云的消息使用消息路由和自定义终结点](iot-hub-devguide-messages-read-custom.md)。

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: ./iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-endpoints]: ./iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: /event-hubs/
[lnk-management-portal]: https://portal.azure.cn
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: /event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph
[lnk-amqp]: https://www.amqp.org/