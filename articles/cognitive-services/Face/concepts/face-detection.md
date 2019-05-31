---
title: 人脸检测和属性的概念
titleSuffix: Azure Cognitive Services
description: 了解有关人脸检测和人脸属性的概念。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
origin.date: 04/26/2019
ms.date: 05/14/2019
ms.author: v-junlch
ms.openlocfilehash: 2ae856089150df71978b2af1241f0cefd023fc00
ms.sourcegitcommit: 71172ca8af82d93d3da548222fbc82ed596d6256
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/15/2019
ms.locfileid: "65669071"
---
# <a name="face-detection-and-attributes"></a>人脸检测和属性

本文解释人脸检测和人脸属性数据的概念。 人脸检测是在图像中查找人脸，并选择地返回各种人脸相关数据的动作。

可以使用[人脸 - 检测](https://dev.cognitive.azure.cn/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)操作在图像中检测人脸。 最起码，检测到的每个人脸将对应于响应中的 **faceRectangle** 字段。 这是一组标记所找到的人脸的像素坐标（左边缘、顶部、宽度、高度）。 使用这些坐标可以获取人脸的位置及其大小。 在 API 响应中，人脸按照从大到小的顺序列出。

## <a name="face-id"></a>人脸 ID

人脸 ID 只是在图像中检测到的每个人脸的唯一标识符字符串。 可以在[人脸 - 检测](https://dev.cognitive.azure.cn/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 调用中请求人脸 ID。

## <a name="face-landmarks"></a>人脸特征点

人脸特征点是人脸上的一组易于查找的点，例如瞳孔或鼻尖。 默认情况下，有 27 个预定义的特征点。 下图显示了所有 27 个点：

![标有所有 27 个特征点的人脸插图](../Images/landmarks.1.jpg)

以像素为单位返回的点坐标。

## <a name="attributes"></a>属性

属性是可由[人脸 - 检测](https://dev.cognitive.azure.cn/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API 选择性地检测到的一组附加人脸特征。 下面是可以检测到的属性：

* **Age**：特定人脸的估计年龄（岁）。
* **Blur**：图像中人脸的模糊度。 将返回 0 到 1 的值，以及非正式的分级（“low”、“medium”、“high”）。
* **Exposure**：图像中人脸的曝光度。 将返回 0 到 1 的值，以及非正式的分级（“underExposure”、“goodExposure”、“overExposure”）。
* **Emotion**：给定人脸的情感列表及其检测置信度。 置信度分数会进行标准化：所有情感的分数加起来后会得到一个总的分数。 返回的情感包括快乐、悲伤、中性、愤怒、蔑视、厌恶、惊讶、恐惧。
* **Facial hair**：给定人脸的胡须状态和长度。
* **Gender**：给定人脸的估计性别。 可能的值为“male”、“female”和“genderless”。
* **Glasses**：给定的人脸是否戴有眼镜。 可能的值为“NoGlasses”、“ReadingGlasses”、“Sunglasses”和“Swimming Goggles”。
* **Hair**：人脸的发型。 这会显示头发是否可见、是否检测到秃顶，以及检测到了哪种发色。
* **Head pose**：人脸在 3D 空间中的摆向。 此属性以俯仰角、翻滚角和偏航角（以度为单位）描述。 值的范围分别为 [-90, 90]、[-180, 180] 和 [-90, 90] 度。 有关角度映射，请参阅以下示意图：

    ![标有俯仰、翻滚和偏航轴的头部](../Images/headpose.1.jpg)
* **Makeup**：人脸是否有化妆。 将返回“eyeMakeup”和“lipMakeup”的布尔值。
* **Noise**：在人脸图像中检测到的视觉噪点。 将返回 0 到 1 的值，以及非正式的分级（“low”、“medium”、“high”）。
* **Occlusion**：是否存在遮挡人脸部位的物体。 将返回“eyeOccluded”、“foreheadOccluded”和“mouthOccluded”的布尔值。
* **Smile**：给定人脸的微笑表情。 这是一个介于 0（未微笑）与 1（明确的微笑）之间的值。

> [!IMPORTANT]
> 人脸属性是使用统计算法预测的，不一定准确。 根据特性数据做出决策时请小心。

## <a name="input-data"></a>输入数据

使用以下提示来确保输入的图像提供最准确的检测结果：

* 支持的输入图像格式为 JPEG、PNG、GIF（第一帧）和 BMP。
* 图像文件不得大于 4 MB。
* 可检测的面部大小范围为 36x36 到 4096x4096 像素。 无法检测超出此范围的人脸。
* 某些人脸会因技术难题而检测不到。 极端的人脸角度（头部姿势）或人脸遮挡物（太阳镜或遮挡人脸部位的手等物体）可能会影响检测。 正面和接近正面的人脸可提供最佳结果。

## <a name="next-steps"></a>后续步骤

熟悉人脸检测的概念后，接下来请了解如何编写一个可在给定图像中检测人脸的简单脚本。

* [如何检测图像中的人脸](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
