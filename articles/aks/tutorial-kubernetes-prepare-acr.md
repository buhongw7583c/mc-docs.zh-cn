---
title: Azure 上的 Kubernetes 教程 - 创建容器注册表
description: 在本 Azure Kubernetes 服务 (AKS) 教程中，我们将创建一个 Azure 容器注册表实例，并上传一个示例应用程序容器映像。
services: container-service
author: rockboyfor
manager: digimobile
ms.service: container-service
ms.topic: tutorial
origin.date: 08/14/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: 4acc44c4a8c77401d863100644579e405eef8d46
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52676612"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>教程：部署和使用 Azure 容器注册表

Azure 容器注册表 (ACR) 是用于 Docker 容器映像的基于 Azure 的专用注册表。 使用专用容器注册表可以安全生成和部署应用程序与自定义代码。 在本教程的第二部分（共七个部分），你将部署 ACR 实例，并将一个容器映像推送到其中。 你将学习如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure 容器注册表 (ACR) 实例
> * 标记 ACR 的容器映像
> * 向 ACR 上传映像
> * 查看注册表中的映像

后续教程会将此 ACR 实例与 AKS 中的 Kubernetes 群集集成，并从映像部署应用程序。

## <a name="before-you-begin"></a>准备阶段

在[上一教程][aks-tutorial-prepare-app]中，已经为一个 Azure Voting 应用程序示例创建了容器映像。 如果尚未创建 Azure Voting 应用映像，请返回到[教程 1 - 创建容器映像][aks-tutorial-prepare-app]。

本教程需要运行 Azure CLI 2.0.44 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表

若要创建 Azure 容器注册表，首先需要一个资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create][az-group-create] 命令创建资源组。 以下示例在 *chinaeast* 区域中创建名为 *myResourceGroup* 的资源组：

```azurecli
az group create --name myResourceGroup --location chinaeast
```

使用 [az acr create][az-acr-create] 命令创建 Azure 容器注册表实例，并提供你自己的注册表名称。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 本教程的余下部分使用 `<acrName>` 作为容器注册表名称的占位符。 “基本”SKU 是用于开发目的的成本优化入口点，可在存储与吞吐量之间实现平衡。

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>登录到容器注册表

若要使用 ACR 实例，必须先登录。 使用 [az acr login][az-acr-login] 命令，并提供在上一步骤中为容器注册表指定的唯一名称。

```azurecli
az acr login --name <acrName>
```

完成后，该命令会返回“登录成功”消息。

## <a name="tag-a-container-image"></a>标记容器映像

若要查看当前本地映像的列表，请使用 [docker images][docker-images] 命令：

```
$ docker images

REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

若要使用 ACR 中的 *azure-vote-front* 容器映像，需要使用注册表的登录服务器地址来标记该映像。 在将容器映像推送到映像注册表时，使用此标记进行路由。

若要获取登录服务器地址，请使用 [az acr list][az-acr-list] 命令并查询 *loginServer*，如下所示：

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

现在，使用容器注册表的 *acrloginServer* 地址标记本地 *azure-vote-front* 映像。 若要指示映像版本，请将 *:v1* 添加到映像名称的末尾：

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

若要验证是否已应用标记，请再次运行 [docker images][docker-images]。 已使用 ACR 实例地址和版本号标记了某个映像。

```
$ docker images

REPOSITORY                                           TAG           IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest        eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry.azurecr.cn/azure-vote-front      v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest        a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask         788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>将映像推送到注册表

现在，可将 *azure-vote-front* 映像推送到 ACR 实例。 使用 [docker push][docker-push] 并提供自己的映像 *acrLoginServer* 地址，如下所示：

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

可能需要花费几分钟才能将映像推送到 ACR。

## <a name="list-images-in-registry"></a>列出注册表中的映像

若要返回已推送到 ACR 实例的映像列表，请使用 [az acr repository list][az-acr-repository-list] 命令。 提供自己的 `<acrName>`，如下所示：

```azurecli
az acr repository list --name <acrName> --output table
```

以下示例输出列出了注册表中提供的 *azure-vote-front* 映像：

```
Result
----------------
azure-vote-front
```

若要查看特定映像的标记，请使用 [az acr repository show-tags][az-acr-repository-show-tags] 命令，如下所示：

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

以下示例输出显示了在上一步骤中标记的 *v1* 映像：

```
Result
--------
v1
```

现在，你的容器映像已存储在专用 Azure 容器注册表实例中。 在下一篇教程中，此映像将从 ACR 部署到 Kubernetes 群集。

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建一个 Azure 容器注册表，并推送了一个在 AKS 群集中使用的映像。 你已了解如何：

> [!div class="checklist"]
> * 创建 Azure 容器注册表 (ACR) 实例
> * 标记 ACR 的容器映像
> * 向 ACR 上传映像
> * 查看注册表中的映像

请继续学习下一篇教程，了解如何在 Azure 中部署 Kubernetes 群集。

> [!div class="nextstepaction"]
> [部署 Kubernetes 群集][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: https://docs.azure.cn/zh-cn/cli/acr?view=azure-cli-latest#create
[az-acr-list]: https://docs.azure.cn/zh-cn/cli/acr?view=azure-cli-latest#list
[az-acr-login]: https://docs.azure.cn/zh-cn/cli/acr?view=azure-cli-latest#az-acr-login
[az-acr-list]: https://docs.azure.cn/zh-cn/cli/acr?view=azure-cli-latest#az-acr-list
[az-acr-repository-list]: https://docs.azure.cn/zh-cn/cli/acr/repository?view=azure-cli-latest#list
[az-acr-repository-show-tags]: https://docs.azure.cn/zh-cn/cli/acr/repository?view=azure-cli-latest#show-tags
[az-group-create]: https://docs.azure.cn/zh-cn/cli/group?view=azure-cli-latest#az-group-create
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli
?view=azure-cli-latest
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md