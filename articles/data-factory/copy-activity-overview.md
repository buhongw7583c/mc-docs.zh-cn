---
title: Azure 数据工厂中的复制活动
description: 了解 Azure 数据工厂中的复制活动。 可以使用复制活动将数据从支持的源数据存储复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
origin.date: 03/11/2020
ms.date: 03/23/2020
ms.author: v-jay
ms.openlocfilehash: e5a4a97b61bc4465ccff48753c08021141cea8f5
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "79497354"
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure 数据工厂中的复制活动

在 Azure 数据工厂中，可以使用复制活动在本地与云数据存储之间复制数据。 复制数据后，可以使用其他活动进一步转换和分析数据。 还可使用复制活动发布有关商业智能 (BI) 和应用程序消耗的转换和分析结果。

![复制活动的角色](media/copy-activity-overview/copy-activity.png)

复制活动在[集成运行时](concepts-integration-runtime.md)上执行。 对于不同的数据复制方案，可以使用不同类型的集成运行时：

* 在可以使用任何 IP 通过 Internet 公开访问的两个数据存储之间复制数据时，可对复制活动使用 Azure 集成运行时。 此集成运行时较为安全可靠、可缩放并[全局可用](concepts-integration-runtime.md#integration-runtime-location)。
* 向/从本地数据存储或位于具有访问控制的网络（如 Azure 虚拟网络）中的数据存储复制数据时，需要安装自承载集成运行时。

集成运行时需要与每个源数据存储和接收器数据存储相关联。 有关复制活动如何确定要使用的集成运行时的信息，请参阅[确定要使用哪个 IR](concepts-integration-runtime.md#determining-which-ir-to-use)。

若要将数据从源复制到接收器，运行复制活动的服务将执行以下步骤：

1. 读取源数据存储中的数据。
2. 执行序列化/反序列化、压缩/解压缩、列映射等。 此服务基于输入数据集、输出数据集和复制活动的配置执行这些操作。
3. 将数据写入接收器/目标数据存储。

![“复制活动”概述](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>支持的数据存储和格式

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>支持的文件格式

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

可以使用复制活动在两个基于文件的数据存储之间按原样复制文件，在这种情况下，无需任何序列化或反序列化即可高效复制数据。 此外，还可以分析或生成给定格式的文件。例如，可以执行以下操作：

* 从本地 SQL Server 数据库复制数据，并将数据以 Parquet 格式写入 Azure Data Lake Storage Gen2。
* 从本地文件系统中复制文本 (CSV) 格式文件，并将其以 Avro 格式写入 Azure Blob 存储。
* 从本地文件系统复制压缩文件，动态解压缩，然后将提取的文件写入 Azure Data Lake Storage Gen2。
* 从 Azure Blob 存储复制 Gzip 压缩文本 (CSV) 格式的数据，并将其写入 Azure SQL 数据库。
* 需要序列化/反序列化或压缩/解压缩的其他许多活动。

## <a name="supported-regions"></a>支持的区域

支持复制活动的服务已在 [Azure Integration Runtime 位置](concepts-integration-runtime.md#integration-runtime-location)中所列的区域和地理位置全球发布。 全局可用拓扑可确保高效的数据移动，此类移动通常避免跨区域跃点。 请参阅[各区域推出的产品](https://azure.microsoft.com/global-infrastructure/services/?regions=china-non-regional,china-east,china-east-2,china-north,china-north-2&products=all)，检查数据工厂和数据移动是否可在特定的区域中使用。

## <a name="configuration"></a>配置

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

通常，若要使用 Azure 数据工厂中的复制活动，需要执行以下操作：

1. **创建用于源数据存储和接收器数据存储的链接服务。** 在本文的[支持的数据存储和格式](#supported-data-stores-and-formats)部分可以找到支持的连接器列表。 有关配置信息和支持的属性，请参阅连接器文章的“链接服务属性”部分。 
2. **为源和接收器创建数据集。** 有关配置信息和支持的属性，请参阅源和接收器连接器文章的“数据集属性”部分。
3. **创建包含复制活动的管道。** 接下来的部分将提供示例。

### <a name="syntax"></a>语法

以下复制活动模板包含受支持属性的完整列表。 指定适合你的方案的属性。

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>语法详细信息

| 属性 | 说明 | 必需？ |
|:--- |:--- |:--- |
| type | 对于复制活动，请设置为 `Copy` | 是 |
| inputs | 指定创建的指向源数据的数据集。 复制活动仅支持单个输入。 | 是 |
| outputs | 指定创建的指向接收器数据的数据集。 复制活动仅支持单个输出。 | 是 |
| typeProperties | 指定用于配置复制活动的属性。 | 是 |
| source | 指定复制源类型以及用于检索数据的相应属性。<br/>有关详细信息，请参阅[受支持的数据存储和格式](#supported-data-stores-and-formats)中所列的连接器文章中的“复制活动属性”部分。 | 是 |
| 接收器 | 指定复制接收器类型以及用于写入数据的相应属性。<br/>有关详细信息，请参阅[受支持的数据存储和格式](#supported-data-stores-and-formats)中所列的连接器文章中的“复制活动属性”部分。 | 是 |
| 转换器 | 指定从源到接收器的显式列映射。 当默认复制行为无法满足需求时，此属性适用。<br/>有关详细信息，请参阅[复制活动中的架构映射](copy-activity-schema-and-type-mapping.md)。 | 否 |
| dataIntegrationUnits | 指定一个度量值，用于表示 [Azure Integration Runtime](concepts-integration-runtime.md) 在复制数据时的算力。 这些单位以前称为云数据移动单位 (DMU)。 <br/>有关详细信息，请参阅[数据集成单位](copy-activity-performance.md#data-integration-units)。 | 否 |
| parallelCopies | 指定从源读取数据和向接收器写入数据时想要复制活动使用的并行度。<br/>有关详细信息，请参阅[并行复制](copy-activity-performance.md#parallel-copy)。 | 否 |
| 保护区 | 指定在数据复制期间是否保留元数据/ACL。 <br/>有关详细信息，请参阅[保留元数据](copy-activity-preserve-metadata.md)。 |否 |
| enableStaging<br/>stagingSettings | 指定是否将临时数据分阶段存储在 Blob 存储中，而不是将数据直接从源复制到接收器。<br/>有关有用的方案和配置详细信息，请参阅[分阶段复制](copy-activity-performance.md#staged-copy)。 | 否 |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| 选择将数据从源复制到接收器时如何处理不兼容的行。<br/>有关详细信息，请参阅[容错](copy-activity-fault-tolerance.md)。 | 否 |

## <a name="monitoring"></a>监视

可通过视觉和编程方式监视在 Azure 数据工厂中运行的复制活动。 有关详细信息，请参阅[监视复制活动](copy-activity-monitoring.md)。

## <a name="incremental-copy"></a>增量复制

数据工厂可让你以递增方式将增量数据从源数据存储复制到接收器数据存储。 有关详细信息，请参阅[教程：以增量方式复制数据](tutorial-incremental-copy-overview.md)。

## <a name="performance-and-tuning"></a>性能和优化

[复制活动监视](copy-activity-monitoring.md)体验向你显示每个活动运行的复制性能统计信息。 [复制活动性能和可伸缩性指南](copy-activity-performance.md)中描述了哪些关键因素会影响通过 Azure 数据工厂中的复制活动移动数据时的性能。 其中还列出了在测试期间观测到的性能值，并介绍了如何优化复制活动的性能。

## <a name="resume-from-last-failed-run"></a>从上次失败的运行恢复

在基于文件的存储之间以二进制格式按原样复制大量文件，并选择将文件夹/文件层次结构从源保存到接收器（例如，将数据从 Amazon S3 迁移到 Azure Data Lake Storage Gen2）时，复制活动支持从上次失败的运行中恢复。 它适用于下述基于文件的连接器：[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md) 和 [SFTP](connector-sftp.md)。

可以通过下述两种方式利用复制活动恢复功能：

- **活动级别重试：** 可以设置复制活动的重试计数。 在管道执行过程中，如果此复制活动运行失败，则下一次自动重试将从上一次试用的故障点开始。
- **从失败的活动重新运行：** 管道执行完成后，还可以通过 ADF UI 监视视图或编程方式触发从失败的活动重新运行的操作。 如果失败的活动是复制活动，则该管道将不仅从此活动重新运行，而且也会从上一个运行的故障点恢复。

    ![复制恢复](media/copy-activity-overview/resume-copy.png)

要注意的几点：

- 恢复发生在文件级别。 如果复制活动在复制文件时失败，则在下一次运行时，会重新复制此特定文件。
- 若要正常使用恢复功能，请不要在两次重新运行之间更改复制活动设置。
- 从 Amazon S3、Azure Blob、Azure Data Lake Storage Gen2 和 Google Cloud Storage 复制数据时，复制活动可以从任意数量的已复制文件恢复。 虽然就其他作为源的基于文件的连接器来说，目前复制活动只支持从有限数量的文件中恢复（通常是在数万个文件的范围内，因文件路径的长度而异），但超出此数量的文件将在重新运行期间重新复制。

对于二进制文件复制以外的其他情况，将从头开始重新运行复制活动。

## <a name="preserve-metadata-along-with-data"></a>保留元数据和数据

在将数据从源复制到接收器时，在进行 Data Lake 迁移这样的情况下，还可以选择使用复制活动来保存元数据和 ACL 以及数据。 有关详细信息，请参阅[保留元数据](copy-activity-preserve-metadata.md)。

## <a name="schema-and-data-type-mapping"></a>架构和数据类型映射

有关复制活动如何将源数据映射到接收器的信息，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

## <a name="fault-tolerance"></a>容错

默认情况下，如果源数据行与接收器数据行不兼容，复制活动将停止复制数据，并返回失败结果。 要使复制成功，可将复制活动配置为跳过并记录不兼容的行，仅复制兼容的数据。 有关详细信息，请参阅[复制活动容错](copy-activity-fault-tolerance.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下快速入门、教程和示例：

- [在同一 Azure Blob 存储帐户中将数据从一个位置复制到另一个位置](quickstart-create-data-factory-dot-net.md)
- [将数据从 Azure Blob 存储复制到 Azure SQL 数据库](tutorial-copy-data-dot-net.md)
- [将数据从本地 SQL Server 数据库复制到 Azure](tutorial-hybrid-copy-powershell.md)
