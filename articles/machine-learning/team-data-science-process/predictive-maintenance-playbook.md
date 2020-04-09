---
title: 针对预测性维护解决方案 (Team Data Science Process) 的 Azure AI 指南
description: 有关增强多个垂直行业预测性维护解决方案的数据科学的全面说明。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 461e9b409a53b3e4fdd6429e42ab0bdec9efb750
ms.sourcegitcommit: 6ddc26f9b27acec207b887531bea942b413046ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80343797"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>针对预测性维护解决方案的 Azure AI 指南

## <a name="summary"></a>摘要

预测性维护 (**PdM**) 是一种流行的预测分析应用程序，可帮助多个行业中的企业实现较高的资产利用率和运营成本节省。 本指南提供业务和分析准则与最佳做法，介绍如何使用 [Microsoft Azure AI 平台](https://azure.microsoft.com/overview/ai-platform)技术成功开发和部署 PdM 解决方案。

针对初学者，本指南介绍了行业特定的业务方案，以及使这些方案适合 PdM 的过程。 此外，还提供了数据要求，以及生成 PdM 解决方案的建模技术。 本指南的主要内容涉及到数据科学过程 - 包括数据准备、特征工程、模型创建和模型操作化的步骤。 为了补充这些关键概念，本指南列出一组解决方案模板来帮助加快 PdM 应用程序的开发。 本指南还提供了有用培训资源的链接，让 实践者了解数据科学幕后的 AI 技术。 

### <a name="data-science-guide-overview-and-target-audience"></a>数据科学指南概述与目标受众
本指南的前半部分描述典型的业务问题、实施 PdM 解决这些问题的好处，并列出一些常见的用例。 业务决策者 (BDM) 将受益于此内容。 后半部分介绍 PdM 幕后的数据科学，并提供使用本指南所述原理生成的 PdM 解决方案列表。 此外，还提供了学习路径和培训材料的链接。 技术决策者 (TDM) 将受益于此内容。

| 从以下内容开始... | 如果你是… |
|:---------------|:---------------|
| [预测性维护的业务案例](#business-case-for-predictive-maintenance) |业务决策者 (BDM)，正在寻求减少停机时间和运营成本，并提高设备利用率 |
| [预防性维护的数据科学](#data-science-for-predictive-maintenance) |技术决策者 (TDM)，正在评估 PdM 技术，以了解预防性维护的独特数据处理和 AI 要求 |
| [预测性维护的解决方案模板](#solution-templates-for-predictive-maintenance)|软件架构师或 AI 开发人员，正在寻求快速建立演示或概念证明 |
| [预测性维护的培训资源](#training-resources-for-predictive-maintenance) | 上述任何或所有身份，想要了解数据科学、工具和技术背后的基本概念。

### <a name="prerequisite-knowledge"></a>必备知识
BDM 内容并不要求读者事先拥有数据科学方面的知识。 若要学习 TDM 内容，基本了解统计和数据科学会有帮助。 建议了解 Azure 数据和 AI 服务、Python、R、XML 和 JSON 方面的知识。 AI 技术在 Python 和 R 包中实现。 解决方案模板是使用 Azure 服务、开发工具和 SDK 实现的。

## <a name="business-case-for-predictive-maintenance"></a>预测性维护的业务案例

企业需要运行关键的设备来保持高峰效率和利用率，以实现其投资回报。 这些资产既包括价值数百万美元的飞机引擎、涡轮机、电梯或工业冷却塔，也包括复印机、咖啡机或饮水机等日常消费设备。
- 默认情况下，大多数企业都依赖于纠正性维护，即，在部件发生故障时将其更换。  纠正性维护可确保充分使用部件（因此不会浪费组件的生命周期），代价是会导致停机、人工费用和计划外的维护要求（加班或转换到不方便的位置）。
- 在下一个层面，企业可实行预防性维护，即，确定确定某个部件的有效生存期，并在故障之前对其进行维护或更换。  预防性维护可避免计划外和灾难性的故障。 但仍然存在由于计划内停机、组件在其使用生存期内利用率不足以及人工而造成的较高成本。
- 预测性维护的目标是通过实现组件的适时更换，来优化纠正性维护与预防性维护之间的平衡。   这种方法只会更换即将发生故障的组件。 通过扩长组件的生存期（与预防性维护相比）并减少计划外维护和人工费用（与纠正性维护相比），企业可以获得成本节省和竞争优势。

## <a name="business-problems-in-pdm"></a>PdM 中的业务问题
意外的故障以及对复杂系统中问题的根本原因洞察能力不足，导致企业面临较高的运营风险。 部分关键业务问题包括：

- 检测设备或系统中性能或功能的异常。
- 预测资产在不久的将来是否会发生故障。
- 估算资产的剩余使用寿命。
- 识别资产故障的主要原因。
- 识别何时需要对资产执行何种维护操作。

从 PdM 角度讲，典型的目标陈述为：

- 降低任务关键型设备的运营风险。
- 通过在发生故障之前预测故障，来提高资产的回报率。
- 通过启用适时维护操作来控制维护成本。
- 降低客户流失率，提高品牌形象，减少销量损失。
- 通过预测再订购点降低库存水平，从而减少库存成本
- 发现与各种维护问题相关的模式。
- 提供 KPI（关键绩效指标），例如资产状态的运行状况评分。
- 估算资产的剩余使用寿命。
- 及时推荐维护活动。
- 通过估算更换部件的订购日期，实现适时库存。

以下受众可以使用这些目标陈述作为起点：

- 分析并解决具体预测问题的数据科学家。 
- 整合端到端解决方案的云架构师和开发人员。 

## <a name="qualifying-problems-for-predictive-maintenance"></a>符合预防性维护条件的问题
要强调的一个重点是，PdM 无法有效解决所有用例或业务问题。 在问题选择期间，需要考虑三个重要的限定条件：

- 该问题在性质上必须是预测性的；也就是说，应该有预测的目标或结果。 该问题还应该有明确的操作路径，防止检测到故障时发生此类故障。
- 该问题应具有设备的操作历史记录，其中包含利弊结果。  这些记录中还应该包含一套用于缓解不利结果的措施。 错误报告、性能下降维护日志、修复和更换日志也是重要。 此外，为改善问题而采取的修复措施以及更换记录也很有用。
- 历史记录应在相关的数据中反映，这些数据具有足够高的质量，可支持用例。   有关数据相关性和充分性的详细信息，请参阅[预防性维护的数据要求](#data-requirements-for-predictive-maintenance)。
- 最后，企业应该拥有能够明确理解问题的领域专家。 他们熟悉内部流程和实践，可帮助分析师理解和解释数据。 他们还应该能够对现有业务流程进行必要的更改，以根据需要帮助收集正确的数据来解决问题。

## <a name="sample-pdm-use-cases"></a>PdM 示例用例
本部分重点介绍 PdM 在航天、公用事业和运输业等多种行业中的一系列用例。 每个部分以业务问题开始，再讨论 PdM 的优势、围绕业务问题的相关数据，最后讨论 PdM 解决方案的优势。

| 业务问题 | PdM 的优势 |
|:-----------------|-------------------|
|**航空**      |                   |
|机械问题导致航班延误和取消。  无法及时修复的故障可能导致航班取消，并中断日程安排和运营。 |PdM 解决方案可以预测机械故障导致航班延误或取消的概率。|
|飞机引擎部件故障  ：在航空业中，飞机引擎部件更换属于最常见的维护任务。 维护解决方案要求仔细管理组件库存可用性、交付和计划。|能够收集组件可靠性的智能可以大大降低投资成本。|
|**金融** |                         |
|ATM 故障是银行业中的一个常见问题。  此处的问题是报告提款机发生卡纸或部件故障导致 ATM 提现交易中断的概率。 根据交易故障的预测，可以提前维修 ATM，以防止发生故障。| 所需的替代方案是防止交易中途机器发生故障，并根据预测设定机器程序以拒绝服务。|
|**能源** |                          |
|风力涡轮机故障  ：风力涡轮机是提倡环保的国家/地区的主要能源设备，其投资较高。 风力涡轮机的关键部件是发电电动机，它的故障会使涡轮机失效。 并且维修费用高昂。|MTTF（平均故障时间）等预测 KPI 可帮助能源公司防止涡轮机故障，并确保尽量缩减停机时间。 故障概率会告知技术人员监控可能即将发生故障的涡轮机，并根据时间安排好维护方案。 使用预测模型可以洞察导致故障的不同因素，帮助技术人员更好地了解问题的根本原因。|
|断路器故障  ：家庭和企业的配电要求电线始终保持正常状态，这样才能保证能源供应。 在遇到过载或不利天气状况时，断路器有助于限制或避免电线损坏。 此处的业务问题是预测断路器故障。| PdM 解决方案有助于降低修复成本，并延长设备（如断路器）的工作寿命。 它们通过减少意外的故障和服务中断，来帮助提高电网的质量。|
|**运输和物流** |    |
|电梯门故障  ：大型电梯公司为全球数百万部功能电梯提供完整堆栈服务。 电梯安全性、可靠性和运行时间是客户的主要考虑因素。 这些公司通过传感器跟踪这些属性和其他各种属性，以帮助客户采取纠正和预防性维护。 在电梯中，最主要的客户问题是电梯门故障。 此用例中的业务问题是提供知识库预测应用程序，用于预测门故障的潜在原因。| 电梯是可能会达到 20-30 年的资本投资。 因此，每笔潜在销售都可能遇到激烈的竞争；客户对服务和支持的预期也很高。 在产品和服务方面，预测性维护可为这些公司提供优势，让他们打败竞争对手。|
|车轮故障  ：车轮故障占到所有火车脱轨原因的一半，给全球铁路行业造成了数十亿美元的代价。 车轮故障还会导致铁路退化，有时甚至导致铁路提前断裂。 而铁路断裂又会导致灾难性事件，例如脱轨。 为了避免这种情况，铁路公司会监控车轮的性能，并以预防性的方式更换有问题的车轮。 此处的业务问题是预测车轮故障。| 对车轮进行预测性维护有助于适时更换车轮 |
|地铁车门故障  ：在地铁运营中，延误的主要原因之一是列车发生车门故障。 此处的业务问题是预测列车车门故障。|提前察觉车门故障，或者距离故障的天数，有助于企业优化列车车门检修计划。|

下一部分详细介绍如何实现前面所述的 PdM 优势。

## <a name="data-science-for-predictive-maintenance"></a>预防性维护的数据科学

本部分提供 PdM 的数据科学原理和实践的一般准则， 旨在帮助 TDM、解决方案架构师或开发人员了解生成适用于 PdM 的端到端 AI 应用程序的先决条件和过程。 可以结合[预防性维护的解决方案模板](#solution-templates-for-predictive-maintenance)中所列的演示和概念证明模板阅读本部分。 然后，可以运用这些原理和最佳做法，在 Azure 中实现 PdM 解决方案。

> [!NOTE]
> 本指南并不旨在向读者讲解数据科学。 [预防性维护的培训资源](#training-resources-for-predictive-maintenance)部分中提供了多个有用的资源，欢迎进一步阅读。 本指南中所列的[解决方案模板](#solution-templates-for-predictive-maintenance)演示用于解决具体 PdM 问题的其中一些 AI 技术。

## <a name="data-requirements-for-predictive-maintenance"></a>预测性维护的数据要求

能否从学习中获得成功取决于：(a) 讲述内容的质量，(b) 学习者的能力。 预测模型能够从历史数据中学习模式，并根据这些观察到的模式，结合特定的概率预测将来的结果。 模型的预测准确性取决于训练和测试数据的相关性、充分性和质量。 使用此模型“评分”的新数据应具有与训练/测试数据相同的特征和架构。 新数据的特征（类型、密度、分布等）应与训练和测试数据集相匹配。 本部分重点描述此类数据要求。

### <a name="relevant-data"></a>相关的数据

首先，数据必须与问题相关。  以前面所述的“车轮故障”用例为例 - 训练数据应包含与车轮的运行相关的特征。  如果问题是预测牵引系统的故障，则训练数据必须包含牵引系统的所有不同组件。  第一种情况针对特定的组件，而第二种情况针对更大子系统的故障。 一般的建议是围绕特定的组件而不是更大子系统设计预测系统，因为后者的数据更分散。 领域专家（参阅[符合预防性维护条件的问题](#qualifying-problems-for-predictive-maintenance)）应帮助选择用于分析的最相关数据子集。 [预防性维护的数据准备](#data-preparation-for-predictive-maintenance)中更详细介绍了相关数据源。

### <a name="sufficient-data"></a>充足的数据
在故障历史记录数据方面，我们经常会提出两个问题：(1)“训练一个模型需要多少个故障事件？” (2)“多少条记录被视为‘足够’？”这些问题没有绝对的答案，而只能凭经验法则来解答。 对于问题 (1)，故障事件越多，则模型质量就越好。 对于问题 (2)，故障事件的确切数目取决于所要解决的问题的数据和上下文。 但另一方面，如果某台机器过于频繁地发生故障，在这种情况下企业会将其更换，这样就会减少故障实例。 同样，领域专家的指导非常重要。 但是，可以通过某些方法来处理罕见事件的问题。  [处理不平衡的数据](#handling-imbalanced-data)部分对此做了介绍。

### <a name="quality-data"></a>数据质量
数据的质量至关重要 - 与目标变量值相结合时，每个预测器属性值必须准确。  在统计和数据管理学科中，数据质量已有全面的研究，本指南不会予以阐述。

> [!NOTE]
> 可通过多种资源和企业产品来交付质量数据。 下面提供了参考示例：
> - 2003 年 Dasu, T 和 Johnson, T. 在 Wiley 上发表的“Exploratory Data Mining and Data Cleaning”（探索性数据挖掘和数据清理）
> - 维基百科文章 [Exploratory Data Analysis](https://en.wikipedia.org/wiki/Exploratory_data_analysis)（探索性数据分析）
> - Hellerstein, J 发表的 [Quantitative Data Cleaning for Large Databases](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)（大型数据库的量化数据清理）
> - de Jonge, E 和 van der loo, M 发表的 [Introduction to Data Cleaning with R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)（使用 R 清理数据简介）

## <a name="data-preparation-for-predictive-maintenance"></a>预测性维护的数据准备

### <a name="data-sources"></a>数据源

预防性维护的相关数据源包括但不限于：
- 故障历史记录
- 维护/修复历史记录
- 机器运行状态
- 设备元数据

#### <a name="failure-history"></a>故障历史记录
故障事件在 PdM 应用程序中很少出现。 但是，在生成预测模型时，算法需要学习组件的正常操作模式及其故障模式。 因此，训练数据应包含这两个类别中的足够示例。 维护记录和部件更换历史记录是用于查找故障事件的不错来源。 凭借一定的领域知识，还可以将训练数据中的异常定义为故障。

#### <a name="maintenancerepair-history"></a>维护/修复历史记录
资产的维护历史记录包含有关已更换的组件、已执行的维修活动等的详细信息。这些事件会记录降级模式。 训练数据中缺少此重要信息可能导致误导性的模型结果。 也可以在维护历史记录中找到特殊错误代码或部件订购日期形式的故障历史记录。 领域专家应该调查并提供影响故障模式的其他数据源。

#### <a name="machine-operating-conditions"></a>机器运行状态
运行中设备的基于传感器的（或其他）流数据是重要的数据源。 PdM 中的一项重要假设是，机器的运行状况在日常运行过程中会不断降级。 我们预期数据包含随时间改变的特征，这些特征捕获这种老化模式，以及导致降级的任何异常。 算法需要根据数据的时态特征来学习不同时间的故障和非故障模式。 根据这些数据点，算法会通过学习来预测机器在发生故障之前，还能继续工作多少个时间单位。

#### <a name="static-feature-data"></a>静态特征数据
静态特征是有关设备的元数据。 例如，设备制造商、型号、制造日期、服务开始日期、系统位置和其他技术规格。

下面以表格形式列出了 [PdM 示例用例](#sample-pdm-use-cases)的相关数据示例：

| 用例 | 相关数据的示例 |
|:---------|---------------------------|
|航班延误和取消  | 航段和页面日志形式的航班路线信息。 航段数据包括路线详细信息，例如出发/抵达日期、时间、机场、中途停留地点等。页面日志数据包括地面维护人员所记录的一系列错误和维护代码。|
|飞机引擎部件故障  | 从飞机传感器收集的数据，提供各部件状况的信息。 维护记录还有助于识别何时发生了组件故障，以及何时更换了这些组件。|
|ATM 故障  | 有关每笔交易（现金存取）和现金发放的传感器读数。 纸币、纸币厚度、纸币送出距离、凭单属性等的差距度量值的信息。维护记录：提供错误代码、维修信息，上次在提款机中放钱时间。|
|风力涡轮机故障  | 传感器会监控温度、风向、发电功率、发电机转速等涡轮机状况。从不同区域的风力发电厂中的多个风力涡轮机收集数据。 通常，每个涡轮机有多个传感器读数，它们会按固定的时间间隔中继度量值。|
|断路器故障  | 维护日志：包括纠正、预防和系统性措施。 操作数据：包括发送到断路器的自动和手动命令，例如打开和关闭操作。 制造日期、位置、型号等设备元数据。电压级别、地理位置、环境条件等断路器规格。|
|电梯门故障 | 电梯类型、制造日期、维护频率、建筑物类型等电梯元数据。 门周期数、门平均关闭时间等操作信息。 包含原因的故障历史记录。|
|车轮故障  | 度量车轮加速度、刹车距离、传动距离、速度等的传感器数据。有关车轮的静态信息，例如制造商、制造日期。 从跟踪订购日期和数量的部件订购数据库推理的故障数据。|
|地铁车门故障  | 车门打开和关闭时间，以及列车车门当前状况等其他操作数据。 静态数据包括资产标识符、时间和状况值列。|

### <a name="data-types"></a>数据类型
提供上述数据源后，在 PdM 域中观察到的两个主要数据类型为：

-  时态数据：操作遥测数据、机器状况、工单类型，以及优先级代码（包括记录时的时间戳）。 故障、维护/维修和使用情况历史记录也包含与每个事件关联的时间戳。
-  静态数据：机器特征和操作员特征通常是静态的，因为它们描述机器的技术规格或操作员属性。 如果这些特征会随时时间的变化而变化，则应包含关联的时间戳。

应该根据所用的算法，将预测器和目标变量预处理/转换成[数字、分类和其他数据类型](https://www.statsdirect.com/help/basics/measurement_scales.htm)。

### <a name="data-preprocessing"></a>数据预处理
特征工程的先决条件之一是准备各种流中的数据，以编写可从中轻松生成特征的架构。  首先将数据可视化为记录表。 表中的每一行表示训练实例，列表示预测器特征（也称为独立属性或变量）。  对数据进行组织，使最后一列成为目标（依赖的变量）。  对于每个训练实例，分配一个标签作为此列的值。 

对于时态数据，将传感器数据的持续时间分割成时间单位。 每条记录应属于某个资产的时间单位，并且应提供不同的信息。  时间单位根据业务需求以秒数、分钟数、小时数、天数、月数等的乘数进行定义。 时间单位不一定要与数据收集频率相同。  如果频率较高，数据可能不会显示不同单位之间的重要差异。 例如，假设每隔 10 秒收集环境温度。 对训练数据使用这种间隔只会增大示例数目，而不会提供更多的信息。 对于这种情况，更好的策略是根据业务理由，对超过 10 分钟或一小时的数据求平均值。

对于静态数据：
-  维护记录：原始维护数据包含资产标识符和时间戳，以及在给定时间点执行的维护活动的信息。 将维护活动转换为分类列，其中每个类别描述符唯一映射到特定的维护操作。  维护记录的架构包括资产标识符、时间和维护操作。

- 故障记录：  可将故障或故障原因记录为特定业务条件定义的特定错误代码或故障事件。 如果设备具有多个错误代码，领域专家应该帮助识别与目标变量相关的代码。 使用剩余的错误代码或条件来构造与这些故障相关的预测器特征。  故障记录的架构包括资产标识符、时间、故障或故障原因（如果可用）。

-  机器和操作员元数据：将机器和操作员数据合并到一个架构，以将资产与其操作员及其相关的属性相关联。 机器状况的架构包括资产标识符、资产特征、操作员标识符和操作员特征。

其他数据预处理步骤包括处理缺失值和属性值的规范化。   本指南不会对此进行详细讨论 - 请参阅下一部分的有用参考资源。

完成上面所述的数据源预处理后，在进行特征工程之前所做的最后一项转换是，根据资产标识符和时间戳联接上述表。 机器处于正常运行状态后，生成的表将在故障列中包含 null 值。 可以根据正常操作的指示符推算这些 null 值。 使用此故障列创建预测模型的标签。  有关详细信息，请参阅[预防性维护的建模技术](#modeling-techniques-for-predictive-maintenance)部分。

## <a name="feature-engineering"></a>特性工程
特征工程是为数据建模之前的第一个步骤。 [此处介绍](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features)了该步骤在数据科学过程中的作用。 特征是模型的预测属性 - 例如温度、压力、震动，等等。  对于 PdM 而言，特征工程涉及根据在相当长的时间内收集的历史数据抽象出机器的运行状况。 在这种意义上，它不同于其对等术语，例如远程监视、异常检测和故障检测。 

### <a name="time-windows"></a>时间窗口
远程监视需要报告截止相应时间点发生的事件。  异常检测模型评估（评分）传入的数据流，以标记截止相应时间点发生的异常。 故障检测将时间点发生的故障分类为特定的类型。 相比之下，PdM 涉及到根据特征预测将来时间段的故障。这些特征表示历史时间段机器的行为。   对于 PdM 而言，来自各个时间点的特征数据过于杂乱，而没有预测性。 因此，需要通过聚合不同时间窗口的数据点，将每个特征的数据平滑化。 

### <a name="lag-features"></a>滞后特性
业务要求定义模型预测将来的远近程度。 而这个持续时间又有助于定义“模型需要回溯多久以前的数据”来做出这些预测。 这段“回溯”期称为“延隔时间”，在此延隔时间段设计的特征称为“延隔特征”。   本部分介绍可从包含时间戳的数据源构造的延隔特征，以及如何从静态数据源创建特征。 延隔特征在性质上通常是数字型的。 

> [!IMPORTANT]
> 窗口大小通过试验来确定，应该在领域专家的帮助下最终确定。 选择和定义延隔特征、其聚合与窗口类型时，应遵循相同的注意事项。

#### <a name="rolling-aggregates"></a>滚动聚合
对于每条资产记录，选择大小为“W”的滚动窗口作为时间单位数来计算聚合。 然后，使用该记录日期之前的 W 时段来计算延隔特征。  在图 1 中，蓝线显示在每个时间单位针对资产记录的传感器值。 它们表示在窗口大小 W = 3 时，特征值的滚动平均值。 滚动平均值是根据 t<sub>1</sub>（橙色）到 t<sub>2</sub>（绿色）范围内包含时间戳的所有记录计算得出的。 W 值通常以分钟或小时为单位，具体取决于数据的性质。 但对于某些问题，选择较大的 W（假设 12 个月）能够提供记录时间之前某个资产的整个历史记录。

![图 1. 滚动聚合功能](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

图 1. 滚动聚合功能

基于时间窗口的滚动聚合的示例包括计数、平均、CUMESUM（累计和）度量、最小/最大值。 此外，经常会使用方差、标准偏差和超过 N 标准偏差的离群值计数。 下面列出了可能适用于本指南所述[用例](#sample-pdm-use-cases)的聚合示例。 
- 航班延误：过去一天/一周的错误代码计数。 
- 飞机引擎部件故障：滚动平均、标准偏差，以及过去一天/一周的总和，等等。  应该在业务领域专家的配合下确定此指标。
- ATM 故障：滚动平均、中间值、范围、标准偏差、超过三个标准偏差的离群值计数、上限和下限 CUMESUM。 
- 地铁车门故障  ：过去一天、一周、两周的事件计数等。
- 断路器故障  ：过去一周、一年、三年的故障计数等。

PdM 中的另一个有用技术是使用检测数据异常的算法来捕获趋势变化、峰值和水平变化。

#### <a name="tumbling-aggregates"></a>翻转聚合
对于每条带有标签的资产记录，定义大小为 _W-<sub>k</sub>_ 的窗口，其中，_k_ 是大小为 _W_ 的窗口数。然后，根据记录时间戳之前时段的 _k_ _翻转窗口_ _W-k, W-<sub>(k-1)</sub>, …, W-<sub>2</sub>, W-<sub>1</sub>_ 创建聚合。 _k_ 可以是较小数字（以捕获短期效应），也可以是较大数字（以捕获长期降级模式）。 （参阅图 2）。

![图 2. 翻转聚合特性](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

图 2. 翻转聚合特性

例如，可以使用 W=1 和 k=3 创建风力涡轮机用例的延隔特征。 这些特征使用顶部和底部离群值表示过去三个月每个月的延隔时间。

### <a name="static-features"></a>静态特性

这些设备的技术规范（例如制造日期、型号、位置）就是静态特征的示例。 可将这些特征视为建模的分类变量。  对于断路器用例，静态特征的部分示例包括电压、电流、功率容量、变压器类型和电源。 对于车轮故障用例，轮胎类型（合金还是钢）就是静态特征的示例。

完成上面所述的数据准备工作后，接下来应该可以根据下面所述对数据进行组织。 训练、测试和验证数据应具有此逻辑架构（此示例中的时间单位为“天”）。

| 资产 ID | 时间 | \<功能列 > | Label |
| ---- | ---- | --- | --- |
| A123 |第 1 天 | 上获取。 上获取。 上获取。 | 上获取。 |
| A123 |第 2 天 | 上获取。 上获取。 上获取。 | 上获取。 |
| ...  |...   | 上获取。 上获取。 上获取。 | 上获取。 |
| B234 |第 1 天 | 上获取。 上获取。 上获取。 | 上获取。 |
| B234 |第 2 天 | 上获取。 上获取。 上获取。 | 上获取。 |
| ...  |...   | 上获取。 上获取。 上获取。 | 上获取。 |

特征工程的最后一个步骤是将目标变量加上**标签**。 此过程依赖于建模技术。 而建模技术又依赖于业务问题和可用数据的性质。 下一部分将介绍标签。

> [!IMPORTANT]
> 若要获得成功的 PdM 解决方案，数据准备和特征工程与建模技术同等重要。 领域专家和实践者应投入大量的时间来获得适当的特征和模型数据。 下面列出了许多书籍中有关特征工程的简短示例：
> - 1999 年 Pyle, D. 发表的“Data Preparation for Data Mining (The Morgan Kaufmann Series in Data Management Systems)”（数据挖掘的数据准备（数据管理系统中的 Morgan Kaufmann 系列））
> - 2018 年 Zheng, A. 和 Casari, A. 在 O'Reilly 上发表的“Feature Engineering for Machine Learning: Principles and Techniques for Data Scientists”（机器学习的特征工程：面向数据科学家的原理和技术）。
> - 2018 年 Dong, G. 和 Liu, H.（编辑）在 CRC Press 上发表的“Feature Engineering for Machine Learning and Data Analytics (Chapman & Hall/CRC Data Mining and Knowledge Discovery Series)”（机器学习和数据分析的特征工程（Chapman 和 Hall/CRC 数据挖掘与知识探索系列））

## <a name="modeling-techniques-for-predictive-maintenance"></a>预防性维护的建模技术

本部分介绍 PdM 问题的主要建模技术及其具体的标签构造方法。 请注意，可跨不同的行业使用一种建模技术。 建模技术与数据科学问题而不是手头数据的上下文相搭配。

> [!IMPORTANT]
> 应在咨询领域专家后确定如何  
> 为故障案例和标签策略选择标签。

### <a name="binary-classification"></a>二元分类
二元分类用于_预测设备部件在未来时间段（称为“未来边际时段 X”）内发生故障的概率_。  在咨询领域专家的情况下，根据业务问题和手头数据确定 X。 示例如下：
- 更换组件、部署维护资源、执行维护以避免在该时段内发生问题的最小提前期。 
- 问题发生之前可能发生的事件的最小计数。 

在这种技术中，将识别两种类型的训练示例。 一个指示故障的正示例，其标签为 1。  一个指示正常操作的负示例，其标签为 0。 目标变量（因而也包括标签值）可分类。  模型应识别在将来 X 个时间单位内可能会发生故障或正常工作的每个新示例。

#### <a name="label-construction-for-binary-classification"></a>二元分类的标签构造
此处的问题是：“资产在将来的 X 个时间单位内发生故障的概率是多少？” 若要回答此问题，请将资产故障前面的 X 条记录标记为“即将发生故障”（标签 = 1），并将其他记录全部标记为“正常”（标签 = 0）。 （参阅图 3）。

![图 3. 为二进制分类标记](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

图 3. 为二进制分类标记

下面列出了某些用例的标签策略示例。
- 航班延误：  可将 X 选择为 1 天，以预测未来 24 小时的延误。 然后，将故障发生前 24 小时内的所有航班标记为 1。
- ATM 提款机故障：  目标可以是确定下一小时交易发生故障的概率。 在这种情况下，会将过去发生故障的一小时内发生的所有交易标记为 1。 若要预测吐出后面 N 张纸币后发生故障的概率，会将发生故障后吐出 N 张纸币时间段内吐出的所有纸币标记为 1。
- 断路器故障  ：目标可以是预测下一次断路器命令故障。 在这种情况下，请将 X 选择为将来的某个命令。
- 列车车门故障：  可将 X 选择为两天。
- 风力涡轮机故障  ：可将 X 选择为两个月。

### <a name="regression-for-predictive-maintenance"></a>预测维护的回归
回归模型用于计算资产的剩余使用寿命 (RUL)。  RUL 定义为在下一次发生故障之前，资产保持正常运行的时间长短。 每个训练示例是属于资产时间单位 _nY_ 的记录，其中 _n_ 是乘数。 模型应将每个新示例的 RUL 计算为连续数字。  此数字表示故障之前剩余的时间段。

#### <a name="label-construction-for-regression"></a>回归的标签构造
此处的问题是：“设备的剩余使用寿命 (RUL) 有多久？” 对于故障之前的每条记录，将标签计算为下一次故障之前剩余的时间单位数。 在此方法中，标签是连续变量。 （参阅图 4）

![图 4。 回归标记](./media/predictive-maintenance-playbook/labelling-for-regression.png)

图 4。 回归标记

对于回归，可以参照故障点完成标签操作。 如果不知道资产在故障之前已保留之久，则无法计算标签值。 因此，与二元分类相比，在数据中没有任何故障的资产不可用于建模。 最好是通过另一种称作[生存分析](https://en.wikipedia.org/wiki/Survival_analysis)的统计技术来解决此问题。 但是，对涉及到随时变化且间隔频繁的数据的 PdM 用例运用这种技术可能存在一定的难度。 有关生存分析的详细信息，请参阅[此单页指南](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf)。

### <a name="multi-class-classification-for-predictive-maintenance"></a>预测维护的多类分类
可以 PdM 解决方案中针对两种场景使用多类分类技术：
- 预测两个将来的结果：  第一个结果是资产的故障时间范围。  资产已分配到多个可能的时间段中的一个。 第二个结果是多个根本原因之一导致将来某个时间段发生故障的可能性。  维护人员可以使用这种预测来监视症状和安排维护计划。
- 预测给定故障的最可能根本原因。  此结果会建议一组用于解决故障的适当维护措施。 根本原因的排名列表和建议的修复措施可帮助技术人员在故障后排定修复措施的优先级。

#### <a name="label-construction-for-multi-class-classification"></a>多类分类的标签构造
此处的问题是：“资产在将来的 _nZ_（其中的 _n_ 是时段数目）个时间单位内发生故障的概率是多少？” 若要回答此问题，请使用时间桶 (3Z, 2Z, Z) 标记资产故障之前的 nZ 条记录。 将其他所有记录标记为“正常”（标签 = 0）。 在此方法中，目标变量保存分类值。  （参阅图 5）。

![图 5。 多类分类的故障时间预测标签](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

图 5。 用于故障时间预测的多类分类的标签

此处的问题是：“根本原因/问题 _P<sub>i</sub>_ 导致资产在将来的 X 个时间单位内发生故障的概率是多少？” 其中的 _i_ 是可能根本原因的数目。 若要回答此问题，请将资产故障前面的 X 条记录标记为“根本原因 _P<sub>i</sub>_ 即将导致故障”（标签 = _P<sub>i</sub>_ ）。 将其他所有记录标记为“正常”（标签 = 0）。 在此方法中，标签也可分类（参阅图 6）。

![图 6。 多类分类的根本原因预测标签](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

图 6。 用于根本原因预测的多类分类的标签

模型根据每个 _P<sub>i</sub>_ 分配故障概率以及不发生故障的概率。 这些概率可以按度量值排序，以允许预测最可能在未来发生的问题。

此处的问题是：“发生故障后你们建议采取哪些维护措施？” 若要回答此问题，标签不需要拾取未来边际，因为模型不会预测将来的故障。  它只是在已发生故障之后，预测最有可能的根本原因。 

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>预测性维护的训练、验证和测试方法
[Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 涵盖整个模型训练-测试-验证周期。 本部分介绍 PdM 的独特方面。

### <a name="cross-validation"></a>交叉验证
[交叉验证](https://en.wikipedia.org/wiki/Cross-validation_(statistics))的目标是定义一个数据集用于在训练阶段“测试”模型。 此数据集称为验证集。  此技术有助于限制“过度拟合”等问题，并提供有关模型如何通用化为独立数据集的见解。  该数据集是可能来自真实问题的未知数据集。 PdM 的训练和测试例程需要考虑到随时可变的因素，以基于不可见的未来数据更好地进行通用化。

许多机器学习算法依赖于可以显著改变模型性能的多个超参数。 在训练模型时，这些超参数的最佳值不是自动计算的， 应由数据科学家指定。 有几种方法可以找到超参数的正确值。

最常见的是“k - 折叠交叉验证”，它将示例随机拆分为 _k_ 个折叠。  对于每组超参数值，运行学习算法 _k_ 次。 在每次迭代时，使用当前折叠中的示例作为验证集，使用剩余的示例作为训练集。 基于训练示例训练算法，基于验证示例计算性能指标。 在此循环结束时，计算 _k_ 个性能指标的平均值。 对于每个组超参数值，选择具有最佳平均性能的值。 选择超参数的任务在性质上通常是试验性的。

在 PdM 问题中，数据被记录为来自若干数据源的事件时序。 可根据标签时间将这些记录排序。 因此，如果数据集已随机拆分成训练和验证集，一些训练示例在时间上可能比另一些验证示例更晚。   将会根据训练模型之前到达的某些数据评估超参数值的未来性能。  这些估计可能过于乐观，特别是当时序不固定并且会随时间变化时。 因此，选择的超参数值可能欠佳。

建议的方法是以时间相关的方式将示例拆分为训练和验证集，使得在时间上所有验证示例比所有训练示例都晚。  对于每组超参数值，基于训练数据集训练算法。 基于相同的验证集度量模型的性能。 选择显示最佳性能的超参数值。 根据训练/验证拆分选择的超参数值使得未来的模型性能超过根据交叉验证选择的值。

可以使用最佳的超参数值，通过基于整个训练数据训练学习算法，来生成最终的模型。

### <a name="testing-for-model-performance"></a>测试模型性能
生成模型后，需要基于新数据对该模型的未来性能进行评估。 良好的评估结果是通过验证集计算的超参数值的性能指标，或通过交叉验证计算的平均性能指标。 这些评估结果通常过于乐观。 企业通常会制定附加的准则来规定如何测试模型。

PdM 的建议方法是以时间相关的方式将示例拆分为训练、验证和测试数据集。  所有测试示例在时间上应该比所有训练和验证示例要晚。 拆分后，根据前面所述生成模型并度量其性能。

如果时序固定且易于预测，则随时和时间相关的方法会生成类似的未来性能评估结果。 但是，如果时序不固定和/或难以预测，则时间相关的方法生成的未来性能评估结果会更真实。

### <a name="time-dependent-split"></a>依赖于时间的拆分
本部分介绍实现时间相关拆分的最佳做法。 下面介绍如何在训练集与测试集之间执行时间相关的双向拆分。

假设各种传感器发送了带有时间戳的事件流（例如测量值）。 定义特定时间范围内包含多个事件的训练和测试示例的特征与标签。 例如，对于二元分类，请基于过去的事件创建特征，并基于“X”个时间单位内的未来事件创建标签（请参阅有关[特征工程](#feature-engineering)和建模技术的部分）。 因此，示例的标签时间范围比其特征的时间范围要晚。

对于时间相关的拆分，请选择训练截止时间 T<sub>c</sub>，到该时间点时，将使用通过截至 T<sub>c</sub> 的历史数据进行优化的超参数来训练模型。  为了防止超过 T<sub>c</sub> 的未来标签泄漏到训练数据，请选择最新的时间将训练示例标记为 T<sub>c</sub> 之前的 X 个单位。 在图 7 所示的示例中，每个方块表示数据集中的一条记录，该数据集中的特征和标签已按前文所述进行计算。 图中显示，当 X = 2 且 W = 3 时应进入训练和测试集的记录：

![图 7。 适用于二进制分类的依赖于时间的拆分](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

图 7。 适用于二进制分类的依赖于时间的拆分

绿色方块表示属于时间单位的可用于训练的记录。 在考虑到过去三个特征生成时段，以及 T<sub>c</sub> 之前两个未来标签时段的情况下，生成每个训练示例。 如果两个未来时段的任何部分超过 T<sub>c</sub>，则从训练数据集中排除该示例，因为不会假设可见性超过 T<sub>c</sub>。

由于上述约束，黑色方块表示不应在训练数据集中使用的最终标记数据集的记录。 这些记录也不会在测试数据中使用，因为它们超过了 T<sub>c</sub>。 此外，其标签时间范围部分依赖于训练时间范围，这是不理想的。 训练和测试数据应具有不同的标签时间范围，以防标签信息泄漏。

到目前为止所述的方法允许时间戳接近 T<sub>c</sub> 的训练和测试示例之间存在重叠。 实现更大程度的隔离的解决方法是从测试集中排除处于 W 个 T<sub>c</sub> 时间单位内的示例。 但这种激进的拆分依赖于足够高的数据可用性。

用于预测 RUL 的回归模型更严重地受到泄漏问题的影响。 使用随机拆分方法会导致极端的过度拟合。 对于回归问题，拆分应该使得在 T<sub>c</sub> 之前发生故障的资产中的记录进入训练集。 截止时间之后发生故障的资产的记录进入测试集。

拆分训练和测试数据的另一种最佳做法是按资产 ID 使用拆分。 拆分应该避免训练集中使用的任何资产用于测试模型性能。 如果使用此方法，模型更有可能会使用新资产提供更真实的结果。

### <a name="handling-imbalanced-data"></a>处理不平衡的数据
在分类问题中，如果一个类拥有的示例比其他类多，则数据集被认为是不平衡的。  理想情况下，最好是在训练数据中为每个类提供足够的代表项目，以区分不同的类。 如果一个类小于 10% 的数据，该数据被视为不平衡。 代表数目欠足的类称为少数类。 

许多 PdM 问题会遇到这种不平衡的数据集，其中一个类的代表项目数相比其他一个或多个类要少得多。 在某些情况下，少数类可能只构成总数据点的 0.001%。 并非只有 PdM 存在类不平衡的问题。 其他极少发生故障和异常的领域也会遇到类似问题，例如欺诈检测和网络入侵。 这些故障构成了少数类示例。

如果数据中存在类不平衡的情况，大多数标准学习算法的性能受到牵连，因为它们旨在最小化总错误率。 对于负示例数占 99%、正示例数占 1% 的数据集，模型可以通过将所有实例标记为负来展示 99% 的准确性。 但是，模型会不当地将正示例分类；因此，即使其准确性较高，算法也不一定有用。 因此，诸如错误率的总体准确性这种传统评估指标在不平衡的学习情况下是不足的。  面对不平衡的数据集时，将使用其他指标进行模型评估：
- Precision
- 召回率
- F1 评分
- 成本调整的 ROC（接收方操作特征）

有关这些指标的详细信息，请参阅[模型评估](#model-evaluation)。

然而，有一些方法可以帮助补救类不平衡问题。 两个主要的方法是采样技术和成本敏感学习。  

#### <a name="sampling-methods"></a>采样方法
不平衡的学习涉及到使用采样方法将训练数据集修改为平衡的数据集。 采样方法不会应用到测试集。 尽管有多种采样技术，但最直接的技术是随机过采样和欠采样。  

随机过采样涉及到从少数类中选择随机样本，复制这些样本并将它们添加到训练数据集中。  因此，少数类中的示例数会增加，最终平衡不同类的示例数。 过采样的一个弊端是，某些示例的多个实例可能会使分类器变得过于具体，导致过度拟合。 模型可以展示较高的训练准确性，但处理不可见的测试数据时性能可能欠佳。

相反，随机欠采样从多数类中选择随机样本，并从训练数据集中删除这些示例。  然而，从多数类中删除样本可能导致分类器错过与多数类相关的重要概念。 另一种可行的方法是混合采样，即对少数类进行过采样，同时对多数类进行欠采样。 

有许多复杂的采样技术。 所选的技术取决于数据属性，以及数据科学家的迭代试验结果。

#### <a name="cost-sensitive-learning"></a>成本敏感学习
在 PdM 中，构成少数类的故障比普通示例更值得关注。 因此，重点主要在于算法处理故障的性能。 错误地将正类预测为负类可能比相反的做法开销更高。 这种情况通常称为不均等损失或非对称开销，它是错误地将元素分类为不同的类造成的。 理想的分类器应该针对少数类提供较高的预测准确性，且不会影响多数类的准确性。

可通过多种方法实现这种平衡。 为了缓解不均等损失问题，可将较高的开销分配给少数类的错误分类，并尝试将整体开销降至最低。 SVM（支持向量机）等算法原生就能适应此方法，它允许在训练期间指定正负示例的开销。  同样，提升决策树等提升方法通常对不平衡的数据展示较好的性能。 

## <a name="model-evaluation"></a>模型评估
在业务误报开销较高的 PdM 场景中，错误分类是一个严重的问题。 例如，根据错误的引擎故障预测使飞机着陆可能会中断日程安排和旅行计划。 在装配生产线上关闭某台机器可能会导致收入损失。 因此，根据新的测试数据使用适当的性能指标对模型进行评估非常关键。

下面介绍了用于评估 PdM 模型的典型性能指标：

- [准确性](https://en.wikipedia.org/wiki/Accuracy_and_precision)是用于描述分类器性能的最常用指标。 但是，准确性对数据的分布非常敏感，并且对于使用不平衡数据集的方案而言是一种低效的度量方法。 需改用其他指标。 可以使用[混淆矩阵](https://en.wikipedia.org/wiki/Confusion_matrix)等工具来计算和推理模型准确性。
- PdM 模型的[精度](https://en.wikipedia.org/wiki/Precision_and_recall)与误报率相关。 模型的精度较低通常意味着误报率较高。
- [召回](https://en.wikipedia.org/wiki/Precision_and_recall)率表示模型在测试集中正确识别的故障数。 较高的召回率意味着模型成功捕获了真正的故障。
- [F1 评分](https://en.wikipedia.org/wiki/F1_score)是精度和召回率的调和平均值，其值范围为 0（最差）到 1（最佳）。

对于二元分类：
- [接收方操作曲线 (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) 也是一个常用的指标。 在 ROC 曲线中，模型性能根据 ROC 上的固定操作点来解释。
- 但对于 PdM 问题， _decile 表_和_提升图_更具信息性。 它们只注重正类（故障），提供的算法性能图比 ROC 曲线更复杂。
  - 十分位表是使用文本示例根据故障概率的降序创建的。  然后，将排序的示例分组成十分位（具有最高概率的样本的 10%、20%、30%，依此类推）。 每个十分位的比率（真实正比率）/（随机基线）可帮助估计每个十分位的算法性能。 随机基线采用值 0.1、0.2，依此类推。
  - [提升图](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html)绘制十分位的真实正比率，而不是所有十分位的随机真实正比率。 最前面的十分位是结果的重点，因为它们展示了最大增益。 用于 PdM 时，也可以将最前面的十分位视为“有风险”的代表。

## <a name="model-operationalization-for-predictive-maintenance"></a>预测性维护的模型操作化

只有在使训练的模型可操作后，才能体现数据科学实践的优势。 也就是说，必须将模型部署到业务系统，才能基于前所未见的新数据做出预测。  新数据必须在两个方面完全符合经过训练的模型的模型签名： 
- 所有特征必须在新数据的每个逻辑实例（例如表中的某行）中存在。
- 必须像训练数据一样预处理新数据和设计每个特征。

学术与和行业文献中从多个角度阐述了上述过程。 但是，以下所有陈述的意思相同：
- 使用模型为新数据评分 
- 将模型应用到新数据 
- 使模型可操作 
- 部署模型 
- 针对新数据运行模型 

如前所述，PdM 的模型操作化不同于其对等模块。 涉及到异常检测和故障检测的方案通常实施在线评分（也称为实时评分）。   此处，模型将对每条传入的记录评分，并返回预测结果。  对于异常检测，预测指示发生了异常（示例：单类 SVM）。 对于故障检测，预测会指示故障的类型或类。

相比之下，PdM 涉及到批量评分。  为了符合模型签名，必须像训练数据一样设计新数据中的特征。 对于新数据经常采用的大型数据集，特征将会基于不同的时间窗口聚合，并分批进行评分。 批量评分通常在 [Spark](https://spark.apache.org/) 或 [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics) 等分布式系统中进行。 可用采用两种替代方案 - 但两者都欠佳：
- 流数据引擎支持基于内存中窗口的聚合。 因此，对于它们是否支持在线评分有所争议。 但是，这些系统适用于较窄时间窗口中的密集数据，或较宽窗口中的稀疏元素。 如 PdM 方案中所示，对于较宽时间窗口中的密集数据，它们可能无法正常缩放。
- 如果批量评分不可用，解决方法是调整在线评分，以便每次以较小的批次处理新数据。

## <a name="solution-templates-for-predictive-maintenance"></a>预测性维护的解决方案模板

本指南的最后一部分提供可在 Azure 中实现的 PdM 解决方案模板、教程和试验的列表。 在某些情况下，只需片刻时间即可将这些 PdM 应用程序部署到 Azure 订阅。 可将它们用作概念证明演示、用于试验替代方案的沙盒，或者用于实际生产实施项目的加速器。 这些模板在 [Azure AI 库](https://gallery.azure.ai)或 [Azure GitHub](https://github.com/Azure) 中提供。 这些不同的示例会逐渐部署到此解决方案模板。

| # | 标题 | 说明 |
|--:|:------|-------------|
| 2 | [Azure 预测性维护解决方案模板](https://github.com/Azure/AI-PredictiveMaintenance) | 开放源代码解决方案模板，用于演示 Azure 机器学习建模和完整的 Azure 基础结构，该结构可支持 IoT 远程监视环境中的预测性维护方案。 |
| 3 | [预测性维护的深度学习](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | 包含一个演示解决方案的 Azure Notebook。该解决方案使用 LSTM （长短期记忆）网络（某类递归神经网络）进行预测性维护。请参阅[有关此示例的博客文章](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance)。|
| 4 | [预测性维护的 R 建模指南](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | 有关使用 R 脚本进行 PdM 建模的指南|
| 5 | [面向航天工业的 Azure 预测性维护](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | 基于 Azure ML v1.0 的首批 PdM 解决方案模板之一，适用于飞机维护。 本指南源于此项目。 |
| 6 | [Azure AI Toolkit for IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | IoT Edge 中使用 TensorFlow 的 AI；该工具包在与 Azure IoT Edge 兼容的 Docker 容器中打包了深度学习模型，并以 REST API 的形式公开这些模型。
| 7 | [Azure IoT 预测性维护](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT 套件 PCS - 预配置解决方案。 包含 IoT 套件的飞机维护 PdM 模板。 与同一个项目相关的[另一个文档](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview)和[演练](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough)。 |
| 8 | [使用 SQL R Services 的预测性维护模板](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | 基于 R Services 演示剩余使用寿命的场景。 |
| 9 | [预测性维护建模指南](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | 结合[试验](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1)和[数据集](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1)，以及 AzureML v1.0 中的 [Azure Notebook](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) 和[试验](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)，使用 R 设计的飞机维护数据集特征|

## <a name="training-resources-for-predictive-maintenance"></a>预测性维护的培训资源

除了有关一般 AI 概念和实践的内容和培训之外，Microsoft Azure 还为 PdM 技术背后的基础概念提供了学习路径。

| 培训资源  | 可用性 |
|:-------------------|--------------|
| [使用树和随机林学习 PdM 的路径](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | 公共 | 
| [使用深度学习学习 PdM 的路径](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | 公共 |
| [Azure 上的 AI 开发人员](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | 公共 |
| [Microsoft AI 学校](https://aischool.microsoft.com/learning-paths) | 公共 |
| [GitHub 中的 Azure AI 学习资源](https://github.com/Azure/connectthedots/blob/master/readme.md) | 公共 |
| [LinkedIn Learning](https://www.linkedin.com/learning) | 公共 |
| [Microsoft AI YouTube 网络研讨会](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | 公共 |
| [Microsoft AI 展示](https://channel9.msdn.com/Shows/AI-Show) | 公共 |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | 合作伙伴 |
| [Microsoft 合作伙伴网络](https://learningportal.microsoft.com) | 合作伙伴 |

此外，Stanford 和 MIT 等学术机构以及其他培训公司也在线提供了有关 AI 的免费 MOOCS（大型开放式在线课程）。