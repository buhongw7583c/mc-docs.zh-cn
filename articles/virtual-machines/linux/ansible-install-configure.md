---
title: 在 Azure 虚拟机上安装 Ansible
description: 了解如何在 Ubuntu、CentOS 和 SLES 上安装和配置 Ansible 以管理 Azure 资源
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: rockboyfor
manager: digimobile
ms.author: v-yeche
ms.topic: quickstart
origin.date: 08/21/2018
ms.date: 10/29/2018
ms.openlocfilehash: 536aa2ff049710c02fa94304f91277e23cdcb309
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52675396"
---
<!-- Verify successfully-->
# <a name="install-ansible-on-azure-virtual-machines"></a>在 Azure 虚拟机上安装 Ansible

使用 Ansible 可以在环境中自动部署和配置资源。 可以在 Azure 中使用 Ansible 管理虚拟机 (VM)，管理其他任意资源也一样。 本文详细介绍如何为某些最常见的 Linux 发行版安装 Ansible 和所需的 Azure Python SDK 模块。 通过调整安装的程序包以适应特定的平台，在其他发行版上安装 Ansible。 若要安全地创建 Azure 资源，还需了解如何创建和定义用于 Ansible 的凭据。
<!-- Not Avaialbe on [Features and tools for Bash in the Azure Cloud Shell](../../cloud-shell/features.md#tools)-->

## <a name="prerequisites"></a>先决条件

- **Azure 订阅** - 如果没有 Azure 订阅，请创建一个[试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。

- **对 Linux 或 Linux 虚拟机的访问权限** - 如果没有 Linux 计算机，请创建 [Linux 虚拟机](/virtual-network/quick-create-cli)。

- **Azure 服务主体**：遵循[使用 Azure CLI 2.0 创建 Azure 服务主体](https://docs.azure.cn/zh-cn/cli/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal)一文的“创建服务主体”部分中的指导。 记下 **appId**、**displayName**、**password** 和 **tenant** 的值。

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>在 Azure Linux 虚拟机上安装 Ansible

登录到 Linux 计算机，然后选择下述发行版之一，了解安装 Ansible 的步骤：

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

在终端或 Bash 窗口中输入以下命令，为 Azure Python SDK 模块和 Ansible 安装所需的程序包：

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

按[创建 Azure 凭据](#create-azure-credentials)部分概述的说明进行操作。

<a name="ubuntu1604-lts"></a>
### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

在终端或 Bash 窗口中输入以下命令，为 Azure Python SDK 模块和 Ansible 安装所需的程序包：

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

按[创建 Azure 凭据](#create-azure-credentials)部分概述的说明进行操作。

### <a name="sles-12-sp2"></a>SLES 12 SP2

在终端或 Bash 窗口中输入以下命令，为 Azure Python SDK 模块和 Ansible 安装所需的程序包：

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

按[创建 Azure 凭据](#create-azure-credentials)部分概述的说明进行操作。

## <a name="create-azure-credentials"></a>创建 Azure 凭据

可以组合使用订阅 ID 以及在创建服务主体时返回的信息，以下述两种方式之一配置 Ansible 凭据：

- [创建 Ansible 凭据文件](#file-credentials)
- [使用 Ansible 环境变量](#env-credentials)

如果要使用 Ansible Tower 或 Jenkins 等工具，则需使用将服务主体值声明为环境变量的选项。

<a name="file-credentials"></a>
### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/>创建 Ansible 凭据文件

本部分介绍了如何创建本地凭据文件，以便向 Ansible 提供凭据。 有关如何定义 Ansible 凭据的详细信息，请参阅[为 Azure 模块提供凭据](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)。

对于开发环境，请按以下步骤在主机虚拟机上创建 Ansible 的凭据文件：

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

将以下行插入到凭据文件中 - 将占位符替换为创建服务主体过程中获得的信息。

```bash
[default]
subscription_id=<your-subscription_id>
client_id=<security-principal-appid>
secret=<security-principal-password>
tenant=<security-principal-tenant>
cloud_environment=AzureChinaCloud
```

<!-- Notice: Add cloud_environment=AzureChinaCloud to set the correct environment-->

保存并关闭该文件。

<a name="env-credentials"></a>
### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>使用 Ansible 环境变量

本部分介绍了如何通过将 Ansible 凭据导出为环境变量来配置它们。

在终端或 Bash 窗口中，输入以下命令：

```bash
export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
export AZURE_CLIENT_ID=<security-principal-appid>
export AZURE_SECRET=<security-principal-password>
export AZURE_TENANT=<security-principal-tenant>
export AZURE_CLOUD_ENVIRONMENT=AzureChinaCloud
```

<!-- Notice: export AZURE_CLOUD_ENVIRONMENT=AzureChinaCloud-->

## <a name="verify-the-configuration"></a>验证配置
若要验证配置是否成功，可以现在就使用 Ansible 创建资源组。

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [使用 Ansible 在 Azure 中创建 Linux 虚拟机](./ansible-create-vm.md)

<!-- Update_Description: new articles on ansible install configure -->
<!--ms.date: 10/22/2018-->