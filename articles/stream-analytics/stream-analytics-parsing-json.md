---
title: 在 Azure 流分析中分析 JSON 和 AVRO
description: 本文介绍如何针对复杂数据类型（如数组、JSON、CSV 格式数据）进行操作。
author: lingliw
ms.author: v-lingwu
manager: digimobile
ms.service: stream-analytics
ms.topic: conceptual
origin.date: 01/29/2020
ms.date: 2/6/2020
ms.openlocfilehash: ca7a7dac0266a2540d6db2aee191d65fc1854a5f
ms.sourcegitcommit: c1ba5a62f30ac0a3acb337fb77431de6493e6096
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "78155132"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>在 Azure 流分析中分析 JSON 和 Avro 数据

Azure 流分析支持处理采用 CSV、JSON 和 Avro 数据格式的事件。 JSON 和 Avro 数据都可以结构化，并包含一些复杂类型，例如嵌套对象（记录）和数组。 




## <a name="record-data-types"></a>记录数据类型
如果在输入数据流中使用相应的格式，记录数据类型将用于表示 JSON 和 Avro 数组。 这些示例演示示例传感器，该传感器读取 JSON 格式的输入事件。 下面是单一事件的示例：

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```

### <a name="access-nested-fields-in-known-schema"></a>访问已知架构中的嵌套字段
使用点表示法 (.) 可以轻松地直接从查询访问嵌套字段。 例如，此查询选择上述 JSON 数据中 Location 属性下的纬度和经度坐标。 点表示法可用于浏览多个级别，如下所示。

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

结果为：

|DeviceID|Lat|Long|温度|版本|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>选择所有属性
可以使用“*”通配符选择嵌套记录的所有属性。 请考虑以下示例：

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

结果为：

|DeviceID|Lat|Long|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>当属性名称是变量时访问嵌套字段
 

例如，假设示例数据流需要**与包含各设备传感器阈值的参考数据联接**。 下面显示了此类参考数据的片段。

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

此处的目标是将本文顶部的示例数据集联接到该参考数据，并针对高于其阈值的每个传感器度量值输出一个事件。 这意味着，如果有多个传感器超出其各自的阈值，借助于联接，上述单个事件可以生成多个输出事件。 若要在不使用联接的情况下实现类似结果，请参阅下面的部分。

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** 选择 *SensorReadings* 中名称与来自参考数据的属性名称匹配的属性。 然后从 *SensorReadings* 中提取关联的值。

结果为：

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|湿度|Alert :Sensor above threshold|

### <a name="convert-record-fields-into-separate-events"></a>将记录字段转换为单独的事件

若要将记录字段转换为单独事件，请结合使用 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 运算符和 [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) 函数。

使用原始示例数据时，可以使用以下查询将属性提取到不同的事件中。

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

结果为：

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|温度|80|
|12345|湿度|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[object Object]|

使用 [WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)，可以将这些事件路由到不同的目标：

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

## <a name="array-data-types"></a>数组数据类型

数组数据类型是按顺序排列的值集合。 下面详细介绍一些针对数组值执行的典型操作。 这些事例使用函数 [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics)、[GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics)、[GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics) 和 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 运算符。

下面是单一事件的示例。 `CustomSensor03` 和 `SensorMetadata` 都是**数组**类型的：

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>使用特定数组元素
选择指定索引中的数组元素（选择第一个数组元素）：

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

结果为：

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>选择数组长度

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

结果为：

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>将数组元素转换为单独的事件

选择所有数组元素作为各个事件。 结合使用 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 运算符和 [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) 内置函数，提取所有数组元素作为各个事件：

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

结果为：

|DeviceId|ArrayIndex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
结果为：

|DeviceId|smKey|smValue|
|-|-|-|
|12345|制造商|ABC|
|12345|版本|1.2.45|

如果提取的字段需要显示在列中，则除了 [JOIN](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) 操作外，还可以使用 [WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) 语法来透视数据集。 该联接需要一个[时间边界](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff)条件来防止重复：

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

结果为：

|DeviceId|Lat|Long|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>另请参阅  
 [Azure 流分析中的数据类型](https://msdn.microsoft.com/azure/stream-analytics/reference/data-types-azure-stream-analytics)
 
<!-- Update_Description: new articles on stream analytics parsing json -->
<!--ms.date: 09/17/2018-->