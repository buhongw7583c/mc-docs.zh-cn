---
title: 计算机视觉 API PHP 快速入门 | Microsoft Docs
description: 获取信息和代码示例，帮助自己快速开始使用 PHP 和认知服务中的计算机视觉 API。
services: cognitive-services
author: alexchen2016
manager: digimobile
ms.service: cognitive-services
ms.technology: computer-vision
ms.topic: article
origin.date: 05/22/2017
ms.date: 10/13/2017
ms.author: v-junlch
ms.openlocfilehash: d4a5b96ea689f975b6e3feb13cad218599d04a8b
ms.sourcegitcommit: d75065296d301f0851f93d6175a508bdd9fd7afc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52647875"
---
# <a name="computer-vision-php-quick-starts"></a>计算机视觉 PHP 快速入门

本文提供信息和代码示例来帮助读者快速开始使用计算机视觉 API 和 PHP 来完成以下任务：
- [分析图像](#AnalyzeImage) 
- [使用特定于域的模型](#DomainSpecificModel)
- [以智能方式生成缩略图](#GetThumbnail)
- [从图像中检测并提取文本](#OCR)

在[此处](../Vision-API-How-to-Topics/HowToSubscribe.md)详细了解如何获取免费订阅密钥

## 使用计算机视觉 API 通过 PHP 分析图像 <a name="AnalyzeImage"> </a>

使用[“分析图像”方法](https://dev.cognitive.azure.cn/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fa)，可以基于图像内容提取视觉特征。 可以上传图像或指定图像 URL 并选择要返回的特征，包括：
- 在此[分类](../Category-Taxonomy.md)中定义的类别。 
- 与图像内容相关的标记的详细列表。 
- 完整句子中图像内容的说明。 
- 图像包含的任何人脸的坐标、性别和年龄。
- ImageType（剪贴画或线条绘图）
- 主色、强调色，或者图像是否为黑白色。
- 图像是否包含色情或性暗示内容。 

### <a name="analyze-an-image-php-example-request"></a>分析图像 PHP 示例请求

更改 REST URL 以使用订阅密钥的获取位置，并将“Ocp-Apim-Subscription-Key”值替换为有效的订阅密钥。

```php
<?php
// This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

$request = new Http_Request2('https://api.cognitive.azure.cn/vision/v1.0/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',

    // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '13hc77781f7e4b19b5fcdd72a8df7156',
);

$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'visualFeatures' => 'Categories',
    'details' => '{string}',
    'language' => 'en',
);

$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body
$request->setBody("{body}");  // Replace with the body, for example, "{"url": "http://www.example.com/images/image.jpg"}

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}

?>
```

### <a name="analyze-an-image-response"></a>分析图像响应

成功响应将以 JSON 格式返回。 下面是成功响应的示例： 

```json
{
  "categories": [
    {
      "name": "abstract_",
      "score": 0.00390625
    },
    {
      "name": "people_",
      "score": 0.83984375,
      "detail": {
        "celebrities": [
          {
            "name": "Satya Nadella",
            "faceRectangle": {
              "left": 597,
              "top": 162,
              "width": 248,
              "height": 248
            },
            "confidence": 0.999028444
          }
        ]
      }
    }
  ],
  "adult": {
    "isAdultContent": false,
    "isRacyContent": false,
    "adultScore": 0.0934349000453949,
    "racyScore": 0.068613491952419281
  },
  "tags": [
    {
      "name": "person",
      "confidence": 0.98979085683822632
    },
    {
      "name": "man",
      "confidence": 0.94493889808654785
    },
    {
      "name": "outdoor",
      "confidence": 0.938492476940155
    },
    {
      "name": "window",
      "confidence": 0.89513939619064331
    }
  ],
  "description": {
    "tags": [
      "person",
      "man",
      "outdoor",
      "window",
      "glasses"
    ],
    "captions": [
      {
        "text": "Satya Nadella sitting on a bench",
        "confidence": 0.48293603002174407
      }
    ]  },
  "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
  "metadata": {
    "width": 1500,
    "height": 1000,
    "format": "Jpeg"
  },
  "faces": [
    {
      "age": 44,
      "gender": "Male",
      "faceRectangle": {
        "left": 593,
        "top": 160,
        "width": 250,
        "height": 250
      }
    }
  ],
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown",
      "Black"
    ],
    "accentColor": "873B59",
    "isBWImg": false
  },
  "imageType": {
    "clipArtType": 0,
    "lineDrawingType": 0
  }
}
```

## 使用特定于域的模型 <a name="DomainSpecificModel"> </a>

特定于域的模型是经过训练，可在图像中识别一组特定对象的模型。 当前可用的两个特定于域的模型为 celebrities 和 landmarks。 以下示例识别图像中的地标。

### <a name="landmark-php-example-request"></a>地标 PHP 示例请求

更改 REST URL 以使用订阅密钥的获取位置，并将“Ocp-Apim-Subscription-Key”值替换为有效的订阅密钥。

```php
<html>
<head>
    <title>PHP Sample</title>
</head>
<body>
<?php
// This sample uses PEAR (https://pear.php.net/package/HTTP_Request2/download)
require_once 'HTTP/Request2.php';

// Change "landmarks" to "celebrities" in the url to use the Celebrities model.
$request = new Http_Request2('https://api.cognitive.azure.cn/vision/v1.0/models/landmarks/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',

    // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '13hc77781f7e4b19b5fcdd72a8df7156',
);

$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'model' => 'landmarks',   // Use 'model' => 'celebrities' to use the Celebrities model.
);

$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body
$body = json_encode(array(
    // Request body parameters
    'url' => 'https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg',
));
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" . json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

### <a name="landmark-example-response"></a>地标示例响应

成功响应将以 JSON 格式返回。 下面是成功响应的示例：  

```json
{
    "requestId": "0663b074-8eb3-4fab-a72e-4c31a49bd22e",
    "metadata": {
        "width": 2096,
        "height": 4132,
        "format": "Jpeg"
    },
    "result": {
        "landmarks": [
            {
                "name": "Space Needle",
                "confidence": 0.9998178
            }
        ]
    }
}
```

## 使用计算机视觉 API 通过 PHP 获取缩略图 <a name="GetThumbnail"> </a>

使用[“获取缩略图”方法](https://dev.cognitive.azure.cn/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fb)可以根据图像的兴趣区域 (ROI) 将该图像裁剪为所需的高度和宽度，即使纵横比不同于输入图像。 

### <a name="get-a-thumbnail-php-example-request"></a>获取缩略图 PHP 示例请求

更改 REST URL 以使用订阅密钥的获取位置，并将“Ocp-Apim-Subscription-Key”值替换为有效的订阅密钥。

```php
<?php
// This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

$request = new Http_Request2('https://api.cognitive.azure.cn/vision/v1.0/generateThumbnail');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',

    // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '13hc77781f7e4b19b5fcdd72a8df7156',
);

$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'width' => '{number}',      // Replace "{number}" with the desired width of your thumbnail.
    'height' => '{number}',     // Replace "{number}" with the desired height of your thumbnail.
    'smartCropping' => 'true',
);

$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body
$request->setBody("{body}");    // Replace "{body}" with the body. For example, '{"url": "http://www.example.com/images/image.jpg"}'

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}

?>
```

### <a name="get-a-thumbnail-response"></a>获取缩略图响应

成功响应包含缩略图二进制文件。 如果请求失败，则响应包含错误代码和消息，以帮助确定问题所在。

## 使用计算机视觉 API 通过 PHP 执行光学字符识别 (OCR) <a name="OCR"> </a>

使用[光学字符识别 (OCR) 方法](https://dev.cognitive.azure.cn/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc)可以检测图像中的文本，并将识别到的字符提取到机器可用的字符流中。

### <a name="ocr-php-example-request"></a>OCR PHP 示例请求

更改 REST URL 以使用订阅密钥的获取位置，并将“Ocp-Apim-Subscription-Key”值替换为有效的订阅密钥。

```php
<?php
// This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

$request = new Http_Request2('https://api.cognitive.azure.cn/vision/v1.0/ocr');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',

    // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '13hc77781f7e4b19b5fcdd72a8df7156',
);

$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'language' => 'unk',
    'detectOrientation ' => 'true',
);

$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body
$request->setBody("{body}");    // Replace "{body}" with the body. For example, '{"url": "http://www.example.com/images/image.jpg"}'

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}

?>
```

### <a name="ocr-example-response"></a>OCR 示例响应

如果成功，则返回的 OCR 结果包括文本、区域范围框、线条和单词。 

```json 
{
  "language": "en",
  "textAngle": -2.0000000000000338,
  "orientation": "Up",
  "regions": [
    {
      "boundingBox": "462,379,497,258",
      "lines": [
        {
          "boundingBox": "462,379,497,74",
          "words": [
            {
              "boundingBox": "462,379,41,73",
              "text": "A"
            },
            {
              "boundingBox": "523,379,153,73",
              "text": "GOAL"
            },
            {
              "boundingBox": "694,379,265,74",
              "text": "WITHOUT"
            }
          ]
        },
        {
          "boundingBox": "565,471,289,74",
          "words": [
            {
              "boundingBox": "565,471,41,73",
              "text": "A"
            },
            {
              "boundingBox": "626,471,150,73",
              "text": "PLAN"
            },
            {
              "boundingBox": "801,472,53,73",
              "text": "IS"
            }
          ]
        },
        {
          "boundingBox": "519,563,375,74",
          "words": [
            {
              "boundingBox": "519,563,149,74",
              "text": "JUST"
            },
            {
              "boundingBox": "683,564,41,72",
              "text": "A"
            },
            {
              "boundingBox": "741,564,153,73",
              "text": "WISH"
            }
          ]
        }
      ]
    }
  ]
}
```
