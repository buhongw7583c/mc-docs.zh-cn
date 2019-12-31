---
title: 在 Azure 数据资源管理器中使用后继数据库功能附加数据库
description: 了解如何在 Azure 数据资源管理器中使用后继数据库功能附加数据库。
author: orspod
ms.author: v-tawe
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
origin.date: 11/07/2019
ms.date: 12/16/2019
ms.openlocfilehash: 7251c2b912f4ea415eba966debc752c72d808a40
ms.sourcegitcommit: 4a09701b1cbc1d9ccee46d282e592aec26998bff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75335989"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>在 Azure 数据资源管理器中使用后继数据库来附加数据库

使用**后继数据库**功能可将另一群集中的数据库附加到 Azure 数据资源管理器群集。 **后继数据库**以只读模式附加，因此可以查看其数据，并针对已引入**先导数据库**的数据运行查询。  后继数据库会同步先导数据库中的更改。 由于同步，需要经过几秒到几分钟的延迟之后，才会提供数据。 具体的延迟时长取决于先导数据库元数据的总体大小。 先导数据库和后继数据库使用相同的存储帐户来提取数据。 存储由先导数据库拥有。 后继数据库无需引入数据即可查看数据。 由于附加的数据库是只读的数据库，因此无法修改数据库中除[缓存策略](#configure-caching-policy)、[主体](#manage-principals)和[权限](#manage-permissions)以外的其他数据、表和策略。 无法删除附加的数据库。 这些数据库必须先由先导数据库或后继数据库分离，然后才能将其删除。 

使用后继功能将数据库附加到不同群集是在组织与团队之间共享数据的基础结构。 此功能可用于隔离计算资源，以防止将生产环境用于非生产用例。 后继功能还可用于将 Azure 数据资源管理器群集的成本关联到对数据运行查询的一方。

## <a name="which-databases-are-followed"></a>哪些数据库是后继数据库？

* 一个群集可以后继先导群集中的一个数据库、多个数据库或所有数据库。 
* 单个群集可以后继多个先导群集中的数据库。 
* 一个群集可以同时包含后继数据库和先导数据库

## <a name="prerequisites"></a>先决条件

1. 如果没有 Azure 订阅，请在开始前[创建一个试用帐户](https://www.azure.cn/pricing/1rmb-trial/)。
1. 为先导和后继数据库[创建群集和数据库](/data-explorer/create-cluster-database-portal)。
1. 使用[引入概述](/data-explorer/ingest-data-overview)中所述的多种方法之一将[数据引入](/data-explorer/ingest-sample-data)先导数据库。

## <a name="attach-a-database"></a>附加数据库

可以使用多种方法来附加数据库。 本文介绍如何使用 C# 或 Azure 资源管理器模板附加数据库。 若要附加数据库，必须对先导群集和后继群集拥有权限。 有关权限的详细信息，请参阅[管理权限](#manage-permissions)。

### <a name="attach-a-database-using-c"></a>使用 C# 附加数据库

**所需的 NuGet**

* 安装 [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安装[用于身份验证的 Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)。


```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "adc";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "China East 2";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板附加数据库

本部分介绍如何使用 [Azure 资源管理器模板](../azure-resource-manager/resource-group-overview.md)附加数据库。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the follower cluster."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the leader cluster to create."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('followerClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]"
        },
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>部署模板 

可以[使用 Azure 门户](https://portal.azure.cn)或 PowerShell 部署 Azure 资源管理器模板。

   ![模板部署](media/follower/template-deployment.png)


|**设置**  |**说明**  |
|---------|---------|
|后继群集名称     |  后继群集的名称       |
|附加的数据库配置名称    |    附加的数据库配置对象的名称。 该名称必须在群集级别唯一。     |
|数据库名称     |      要后继的数据库的名称。 若要后继所有先导数据库，请使用“*”。   |
|先导群集资源 ID    |   先导群集的资源 ID。      |
|默认主体修改类型    |   默认的主体修改类型。 可以是 `Union`、`Replace` 或 `None`。 有关默认主体修改类型的详细信息，请参阅[主体修改类型控制命令](https://docs.microsoft.com/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind)。      |
|位置   |   所有资源的位置。 先导和后继数据库必须位于同一位置。       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>验证是否已成功附加数据库

若要验证是否已成功附加数据库，请在 [Azure 门户](https://portal.azure.cn)中找到附加的数据库。 

1. 导航到后继群集并选择“数据库” 
1. 在数据库列表中搜索新的只读数据库。

    ![只读的后继数据库](media/follower/read-only-follower-database.png)

也可使用以下命令：

1. 导航到先导群集并选择“数据库” 
2. 检查相关数据库是否标记为“与其他人共享” > “是”  

    ![读取和写入附加的数据库](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>使用 C# 分离后继数据库 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>从后继群集中分离已附加的后继数据库

后继群集可按如下所示分离任何附加的数据库：

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "adc";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>从先导群集中分离已附加的后继数据库

先导群集可按如下所示分离任何附加的数据库：

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "adc",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="manage-principals-permissions-and-caching-policy"></a>管理主体、权限和缓存策略

### <a name="manage-principals"></a>管理主体

附加数据库时，请指定**默认的主体修改类型**。 默认设置会保留[已授权主体](https://docs.microsoft.com/azure/kusto/management/access-control/index#authorization)的先导数据库集合

|**种类** |**说明**  |
|---------|---------|
|**联合**     |   附加的数据库主体始终包括原始数据库主体，以及添加到后继数据库的其他新主体。      |
|**将**   |    不会从原始数据库继承主体。 必须为附加的数据库创建新主体。     |
|**无**   |   附加的数据库主体只包括原始数据库的主体，而不包括其他主体。      |

有关使用控制命令配置已授权主体的详细信息，请参阅[用于管理后继群集的控制命令](https://docs.microsoft.com/azure/kusto/management/cluster-follower)。

### <a name="manage-permissions"></a>管理权限

所有只读数据库类型的权限管理方式都是相同的。 请参阅[在 Azure 门户中管理权限](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)。

### <a name="configure-caching-policy"></a>配置缓存策略

后继数据库管理员可以修改附加数据库或者该数据库在托管群集上的任何表的[缓存策略](https://docs.microsoft.com/azure/kusto/management/cache-policy)。 默认设置是保留数据库和表级缓存策略的先导数据库集合。 例如，可对先导数据库使用一个 30 天缓存策略以运行每月报告，并对后继数据库使用一个 3 天缓存策略，以仅查询最近的数据进行故障排除。 有关使用控制命令对后继数据库或表配置缓存策略的详细信息，请参阅[用于管理后继群集的控制命令](https://docs.microsoft.com/azure/kusto/management/cluster-follower)。

## <a name="limitations"></a>限制

<!-- * [Streaming ingestion](data-explorer/ingest-data-streaming) can't be used on a database that is being followed. -->

* 后继和先导群集必须位于同一区域。
* 在分离已附加到其他群集的数据库之前，无法删除该数据库。
* 在分离包含已附加到其他群集的数据库的群集之前，无法删除该群集。
* 无法停止附加了后继数据库或先导数据库的群集。 

## <a name="next-steps"></a>后续步骤

* 有关后继群集配置的信息，请参阅[用于管理后继群集的控制命令](https://docs.microsoft.com/azure/kusto/management/cluster-follower)。