---
title: 参数化 Azure 数据工厂中的链接服务
description: 了解如何参数化 Azure 数据工厂中的链接服务，并在运行时传递动态值。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
origin.date: 12/18/2018
ms.date: 01/06/2020
author: WenJason
ms.author: v-jay
manager: digimobile
ms.openlocfilehash: 64e6f0c94f0f2ee8be943c0fde1f5fabfa9cd34c
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "75624197"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>参数化 Azure 数据工厂中的链接服务

现在可以参数化链接服务并在运行时传递动态值。 例如，如果要连接到同一 Azure SQL 数据库服务器上的不同数据库，则现在可以在链接服务定义中参数化数据库名称。 这可以避免必须为 Azure SQL 数据库服务器上的每个数据库创建链接服务。 也可以参数化链接服务定义中的其他属性 - 例如，  用户名。

可以使用 Azure 门户中的数据工厂 UI 或编程接口来参数化链接服务。

> [!TIP]
> 我们建议不要参数化密码或机密。 而应将所有连接字符串都存储在 Azure Key Vault 中，并参数化*机密名称*。

有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>支持的数据存储

目前，Azure 门户中的数据工厂 UI 支持以下数据存储的链接服务参数化。 对于所有其他数据存储，可以通过选择“连接”选项卡上的**代码**图标并使用 JSON 编辑器来参数化链接的服务  。
- Azure SQL 数据库
- Azure SQL 数据仓库
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>数据工厂 UI

![将动态内容添加到“链接服务”定义](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![新建参数](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.chinacloudapi.cn,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
