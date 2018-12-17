---
title: include 文件
description: include 文件
services: virtual-machines-linux
author: rockboyfor
ms.service: virtual-machines-linux
ms.topic: include
origin.date: 11/08/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: fe54c1e16560c68caf1a782ceeec3f65f9367228
ms.sourcegitcommit: 59db70ef3ed61538666fd1071dcf8d03864f10a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52676155"
---
## <a name="supported-distributions-and-drivers"></a>支持的分发和驱动程序

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA 驱动程序

仅下表列出的 Linux 发行版支持适用于 NCv3 系列 VM 的 NVIDIA CUDA 驱动程序。 本文发布时，CUDA 驱动程序信息为最新版本。 有关最新 CUDA 驱动程序，请访问 [NVIDIA](https://developer.nvidia.com/cuda-zone) 网站。 确保安装或升级到最新 CUDA 驱动程序分发软件包。 

<!-- Not Available on NC, NCv2, and ND-series-->
<!-- Not Available on (optional for NV-series)-->


<!-- Not Availale on  [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md)-->

| 分发 | 驱动程序 |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> 基于 CentOS 的 7.3 或 7.4 HPC、基于 CentOS 的 7.4 HPC | NVIDIA CUDA 10.0，驱动程序分支为 R410 |

<!-- Not Available on Red Hat Enterprise Linux 7.3 or 7.4-->

<!-- Not Available on ### NVIDIA GRID drivers-->

<!-- Not Available on NV-series-->
<!-- Not Available on [Red Hat Knowledgebase article](https://access.redhat.com/articles/1067)-->
<!-- Update_Description: update meta properties, wording update -->