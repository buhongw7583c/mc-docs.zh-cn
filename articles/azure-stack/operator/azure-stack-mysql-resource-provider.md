---
title: 将 MySQL 数据库用作 Azure Stack Hub 上的 PaaS
description: 了解如何在 Azure Stack Hub 上部署 MySQL 资源提供程序，并提供 MySQL 数据库即服务。
author: WenJason
ms.topic: article
origin.date: 1/22/2020
ms.date: 02/24/2020
ms.author: v-jay
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 216f343d7d2ea0852196f761deb875f3dfe9d363
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "77540624"
---
# <a name="use-mysql-databases-on-azure-stack-hub"></a>在 Azure Stack Hub 上使用 MySQL 数据库

使用 MySQL 资源提供程序在 [Azure Stack Hub](azure-stack-overview.md) 上提供 MySQL 数据库。 部署资源提供程序并将其连接到一个或多个 MySQL 服务器实例后，可以创建以下项：

* 用于云原生应用的 MySQL 数据库。
* 用于 Web 应用程序的 MySQL 数据库。  

安装 MySQL 资源提供程序之前，请考虑以下几个限制：

- 用户只能创建和管理单个数据库。 最终用户无法访问数据库服务器实例。 这可能会限制与需要访问 master、Temp DB 或动态管理数据库的本地数据库应用程序的兼容性。
- 你的 Azure Stack Hub 操作员负责部署、更新、保护、配置和维护 MySQL 数据库服务器和主机。 RP 服务不提供任何主机和数据库服务器实例管理功能。 
- 不同订阅中不同用户的数据库可以位于同一个数据库服务器实例上。 RP 不提供隔离不同主机或数据库服务器实例上的数据库的任何机制。
- RP 不提供关于数据库的租户使用情况的任何报告。

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL 资源提供程序适配器体系结构

资源提供程序具有以下组件：

* **MySQL 资源提供程序适配器虚拟机 (VM)** ，这是运行提供程序服务的 Windows Server VM。
* **资源提供程序**，它处理请求并访问数据库资源。
* **托管 MySQL 服务器的服务器**，为称作宿主服务器的数据库提供容量。 你可以自己创建 MySQL 实例，或提供对外部 MySQL 实例的访问权限。 [Azure Stack Hub 快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)中有一个示例模板，可以用来：

  * 创建 MySQL 服务器。
  * 从 Azure 市场下载并部署 MySQL 服务器。

> [!NOTE]
> 必须通过租户订阅创建安装在 Azure Stack Hub 集成系统上的宿主服务器， 而不能通过默认提供商订阅创建。 必须通过用户门户或者使用相应的登录名通过 PowerShell 会话来创建这些服务器。 所有宿主服务器都是可计费的 VM，并且必须具有许可证。 服务管理员可以是租户订阅的所有者。

### <a name="required-privileges"></a>所需的特权

系统帐户必须拥有以下特权：

* **数据库：** 创建、删除
* **登录名：** 创建、设置、删除、授予、吊销  

## <a name="next-steps"></a>后续步骤

[部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)
