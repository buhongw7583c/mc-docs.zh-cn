---
title: 快速入门：从 Azure IoT 中心控制设备 (Node.js) | Microsoft Docs
description: 本快速入门会运行两个示例 Node.js 应用程序。 一个为后端应用程序，可远程控制连接到中心的设备。 另一个应用程序可模拟连接到中心的可受远程控制的设备。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: ns
origin.date: 06/19/2018
ms.date: 08/06/2018
ms.author: v-yiso
ms.openlocfilehash: 3857b69d67d86c496e7880e613308eb411c32899
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52674359"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-nodejs"></a>快速入门：控制连接到 IoT 中心的设备 (Node.js)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT 中心是一项 Azure 服务，可将大量遥测数据从 IoT 设备引入云，并从云管理设备。 在本快速入门中，会使用直接方法来控制连接到 IoT 中心的模拟设备。 可使用直接方法远程更改连接到 IoT 中心的设备的行为。

本快速入门使用两个预先编写的 Node.js 应用程序：

* 从后端应用程序调用的可响应直接方法的模拟设备应用程序。 为了接收直接方法调用，此应用程序会连接到 IoT 中心上特定于设备的终结点。
* 后端应用程序，可在模拟设备上调用直接方法。 为了在设备上调用直接方法，此应用程序会连接到 IoT 中心上的服务端终结点。


如果没有 Azure 订阅，请在开始前创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial)。

## <a name="prerequisites"></a>先决条件

本快速入门中运行的两个示例应用程序是使用 Node.js 编写的。 开发计算机上需要有 Node.js v4.x.x 或更高版本。

可从 [nodejs.org](https://nodejs.org) 为下载适用于多个平台的 Node.js。

可以使用以下命令验证开发计算机上 Node.js 当前的版本：

```cmd/sh
node --version
```

如果尚未进行此操作，请从 https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip 下载示例 Node.js 项目并提取 ZIP 存档。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

如果已完成上一[快速入门：将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-node.md)，则可跳过此步骤。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>注册设备

如果已完成上一[快速入门：将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-node.md)，则可跳过此步骤。

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本快速入门中，请使用 Azure CLI 来注册模拟设备。

1. 运行以下命令，以添加 IoT 中心 CLI 扩展并创建设备标识。 

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

   **MyNodeDevice**：这是为注册的设备提供的名称。 请按显示的方法使用 MyNodeDevice。 如果为设备选择不同名称，则可能还需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

2. 运行以下命令，获取刚注册设备的_设备连接字符串_：

    **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyNodeDevice \
      --output table
    ```

    记下如下所示的设备连接字符串：

   `HostName={YourIoTHubName}.azure-devices.cn;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。

3. 还需一个服务连接字符串，以便后端应用程序能够连接到 IoT 中心并检索消息。 以下命令检索 IoT 中心的服务连接字符串：

    **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。
    ```azurecli
    az iot hub show-connection-string \
      --hub-name YourIoTHubName --output table
    ```

    记下如下所示的服务连接字符串：

   `HostName={YourIoTHubName}.azure-devices.cn;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。 服务连接字符串与设备连接字符串不同。

## <a name="listen-for-direct-method-calls"></a>侦听直接方法调用

模拟设备应用程序会连接到 IoT 中心上特定于设备的终结点，发送模拟遥测数据，并侦听中心的直接方法调用。 在本快速入门中，中心的直接方法调用告知设备对其发送遥测的间隔进行更改。 执行直接方法后，模拟设备会将确认发送回中心。

1. 在本地终端窗口中，导航到示例 Node.js 项目的根文件夹。 然后导航到 **iot-hub\Quickstarts\simulated-device-2** 文件夹。

2. 在所选文本编辑器中打开 SimulatedDevice.js 文件。

    将 `connectionString` 变量的值替换为之前记下的设备连接字符串。 然后将更改保存到 SimulatedDevice.js 文件。

3. 在本地终端窗口中，运行以下命令以安装所需的库，并运行模拟设备应用程序：

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    以下屏幕截图显示了模拟设备应用程序将遥测数据发送到 IoT 中心后的输出：

    ![运行模拟设备](./media/quickstart-control-device-node/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>调用直接方法

后端应用程序会连接到 IoT 中心上的服务端终结点。 应用程序通过 IoT 中心对设备进行直接方法调用，并侦听确认。 IoT 中心后端应用程序通常在云中运行。

1. 在另一个本地终端窗口中，导航到示例 Node.js 项目的根文件夹。 然后导航到 **iot-hub\Quickstarts\back-end-application** 文件夹。

2. 在所选文本编辑器中打开 BackEndApplication.js 文件。

    将 `connectionString` 变量的值替换为以前记下的服务连接字符串。 然后将更改保存到 BackEndApplication.js 文件。

3. 在本地终端窗口中运行以下命令，以安装所需的库并运行后端应用程序：

    ```cmd/sh
    npm install
    node BackEndApplication.js
    ```

    以下屏幕截图显示了应用程序对设备进行直接方法调用并接收确认后的输出：

    ![运行后端应用程序](media/quickstart-control-device-node/BackEndApplication.png)

    运行后端应用程序后，在运行模拟设备的控制台窗口中会出现一条消息，且其发送消息的速率也会发生变化：

    ![模拟客户端的变化](media/quickstart-control-device-node/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]


## <a name="next-steps"></a>后续步骤

在本快速入门中，已从后端应用程序调用了设备上的直接方法，并在模拟设备应用程序中响应了直接方法调用。

若要了解如何将设备到云的消息路由到云中的不同目标，请继续学习下一教程。

> [!div class="nextstepaction"]
> [教程：将遥测路由到不同的终结点进行处理](tutorial-routing.md)