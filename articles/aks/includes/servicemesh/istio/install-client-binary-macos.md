---
author: rockboyfor
ms.topic: include
origin.date: 11/15/2019
ms.date: 03/09/2020
ms.author: v-yeche
ms.openlocfilehash: e21fce9b54b5c657c5c87a41020b2993adb46d60
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "79290700"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>下载并安装 Istio istioctl 客户端二进制文件

在 MacOS 上基于 bash 的 shell 中，使用 `curl` 下载 Istio 发行版，然后使用 `tar` 进行解压缩，如下所示：

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

`istioctl` 客户端二进制文件在客户端计算机上运行，用来与 Istio 服务网格交互。 在 MacOS 上基于 bash 的 shell 中使用以下命令安装 Istio `istioctl` 客户端二进制文件。 这些命令可将 `istioctl` 客户端二进制文件复制到 `PATH` 中的标准用户程序位置。

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

如果想要通过命令行完成 `istioctl` 客户端二进制文件，则按如下所示进行设置：

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- Update_Description: update meta properties, wording update, update link -->