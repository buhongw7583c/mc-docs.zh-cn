---
title: 监控 Azure Stack 硬件运行状况 |Microsoft Docs
description: 了解如何监控 Azure Stack 硬件组件的运行状况。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 11/21/2019
ms.date: 01/13/2020
ms.author: v-jay
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: fb98ae1156e4c62d8a3e00fab79243487a740cce
ms.sourcegitcommit: 166549d64bbe28b28819d6046c93ee041f1d3bd7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737982"
---
# <a name="monitor-azure-stack-hardware-components"></a>监控 Azure Stack 硬件组件

Azure Stack 的运行状况和监控系统已用于监控存储子系统的状态，并可根据需要引发警报。 在 1910 版本的 Azure Stack 中，运行状况和监控系统现在还可以为以下硬件组件引发警报：

- 系统风扇
- 系统温度
- 电源
- CPU
- 内存
- 启动驱动器

> [!NOTE]
> 在启用此功能之前，必须与硬件合作伙伴确认它们已就绪。 硬件合作伙伴还应提供在 BMC 中启用此功能的详细步骤。

## <a name="snmp-listener-scenario"></a>SNMP 侦听器场景

SNMP v3 侦听器正在 TCP 端口 162 上的所有三个 ERCS 实例上运行。 基板管理控制器 (BMC) 必须配置为向 Azure Stack 侦听器发送 SNMP 陷阱。 可以通过打开“区域属性”视图，从管理门户中获取三个 PEP Ip。

向侦听器发送陷阱要求进行身份验证，并且必须使用与访问基本 BMC 本身相同的凭据。

如果 TCP 端口 162 上的三个 ERCS 实例中的任何一个实例收到 SNMP 陷阱，将在内部匹配 OID，并引发警报。 Azure Stack 运行状况和监控系统仅接受硬件伙伴定义的 OID。 如果 OID 对于 Azure Stack 是未知的，则它不会与警报匹配。

替换发生故障的组件后，系统会将一个事件从 BMC 发送到指示状态更改的 SNMP 侦听器，并将在 Azure Stack 中自动关闭警报。

> [!NOTE]
> 在更换整个节点或主板后，现有警报不会自动关闭。 当 BMC 失去其配置时（例如恢复出厂设置），这同样适用。

## <a name="next-steps"></a>后续步骤

[防火墙集成](azure-stack-firewall.md)