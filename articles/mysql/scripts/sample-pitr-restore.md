---
title: Azure CLI 脚本 - 将 Azure Database for MySQL 服务器还原到前一个时间点。
description: 此示例 CLI 脚本可将 Azure Database for MySQL 服务器还原到前一个时间点。
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 06/16/2018
ms.openlocfilehash: 7d6ffca943d1879c231c09528bdfc6756182d856
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52649380"
---
# <a name="restore-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 还原 Azure Database for MySQL 服务器

> [!NOTE]
> 将要查看的是 Azure Database for MySQL 的新服务。 若要查看经典 MySQL Database for Azure 的文档，请访问[此页](https://docs.azure.cn/zh-cn/mysql/)。

此示例 CLI 脚本可将单个 Azure Database for MySQL 服务器还原到前一个时间点。 本示例需要运行 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本
在此示例脚本中，更改突出显示的行，以自定义管理员用户名和密码。 将 az monitor 命令中使用的订阅 ID 替换为自己的订阅 ID。

```cli
#!/bin/bash

# Add the Azure CLI extension 
az extension add --name rdbms

# Create a resource group
az group create \
--name myresourcegroup \
--location chinaeast2

# Create a MySQL server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mysql server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location chinaeast2 \
--admin-user myadmin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2 \

# Restore a server from backup to a new server
az mysql server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2018-02-11T13:10:00Z" \
--source-server mydemoserver
```

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```cli
#!/bin/bash
az group delete --name myresource
```

## <a name="script-explanation"></a>脚本说明
此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| **命令** | **说明** |
|---|---|
| [az group create](/cli/group#create) | 创建用于存储所有资源的资源组。 |
| [az mysql server create](/cli/mysql/server#create) | 创建用于托管数据库的 MySQL 服务器。 |
| [az mysql server restore](/cli/mysql/server#restore) | 从备份还原服务器。 |
| [az group delete](/cli/group#delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli)。
- 尝试其他脚本：[Azure Database for MySQL 的 Azure CLI 示例](../sample-scripts-azure-cli.md)