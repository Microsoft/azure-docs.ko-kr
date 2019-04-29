---
title: Azure Stream Analytics에서 JSON 및 AVRO 구문 분석
description: 이 문서에서는 배열, JSON, CSV 형식의 데이터와 같은 복합 데이터 형식을 조작하는 방법을 설명합니다.
services: stream-analytics
ms.service: stream-analytics
author: lingliw
ms.author: v-lingwu
manager: digimobile
ms.topic: conceptual
origin.date: 08/03/2018
ms.date: 11/26/2018
ms.openlocfilehash: 3d4c1bfa8b376ec50efc7b3cddc8d22a40e70892
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478864"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Azure Stream Analytics에서 JSON 및 Avro 데이터 구문 분석

Azure Stream Analytics는 CSV, JSON 및 Avro 데이터 형식의 이벤트 처리를 지원합니다. JSON 및 Avro 데이터에는 모두 중첩된 개체(레코드) 및 배열과 같은 복합 형식이 포함될 수 있습니다. 

## <a name="array-data-types"></a>배열 데이터 형식  
배열 데이터 형식은 정렬된 값의 컬렉션입니다. 배열 값에 일반적인 몇 가지 연산은 아래에 자세히 나와 있습니다. 이러한 예제에서는 입력 이벤트에 배열 데이터 형식인 "arrayField"라는 속성이 있다고 가정합니다.

이러한 예제에서는 [GetArrayElement](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelement-azure-stream-analytics), [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics), [GetArrayLength](https://msdn.microsoft.com/azure/stream-analytics/reference/getarraylength-azure-stream-analytics) 함수 및 [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) 연산자를 사용합니다.

## <a name="examples"></a>예  
 지정된 인덱스에서 배열 요소를 선택합니다(첫 번째 배열 요소 선택).  

```SQL 
SELECT   
    GetArrayElement(arrayField, 0) AS firstElement  
FROM input  
```  

 배열 길이를 선택합니다.  

```SQL  
SELECT   
    GetArrayLength(arrayField) AS arrayLength  
FROM input  
```  

모든 배열 요소를 개별 이벤트로 선택합니다. [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) 연산자는 [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics) 기본 제공 함수와 함께 모든 배열 요소를 개별 이벤트로 추출합니다.  

```SQL  
SELECT   
    arrayElement.ArrayIndex,  
    arrayElement.ArrayValue  
FROM input as event  
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement  
```  

## <a name="record-data-types"></a>레코드 데이터 형식  
레코드 데이터 형식은 입력 데이터 스트림에서 사용될 때 JSON 및 Avro 배열을 나타내는 데 사용됩니다. 다음 예제에서는 입력 이벤트를 JSON 형식으로 읽는 샘플 센서를 보여 줍니다. 단일 이벤트의 예제는 다음과 같습니다.

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
        "CustomSensor02" : 99  
    }  
}  
```  

## <a name="examples"></a>예  
중첩된 필드에 액세스하기 위해 점(.) 표기법을 사용합니다. 예를 들어 이 쿼리는 선행 JSON 데이터의 Location 속성에서 Lat(위도)와 Long(경도) 좌표를 선택합니다. 

```SQL  
SELECT  
    DeviceID,  
    Location.Lat,  
    Location.Long  
FROM input  
```  

속성 이름을 알 수 없는 경우 [GetRecordPropertyValue](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordpropertyvalue-azure-stream-analytics) 함수를 사용합니다. 예를 들어 샘플 데이터 스트림을 각 디바이스 센서에 대한 임계값이 포함된 참조 데이터와 조인해야 한다고 가정해 보겠습니다.  

```json  
{  
    "DeviceId" : "12345",  
    "SensorName" :  "Temperature",
    "Value" : 75
}  
```  

```SQL  
SELECT  
    input.DeviceID,  
    thresholds.SensorName  
FROM input  
JOIN thresholds  
ON  
    input.DeviceId = thresholds.DeviceId  
WHERE  
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value  
```  

레코드 필드를 별도의 이벤트로 변환하려면 [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) 연산자를 [GetRecordProperties](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordproperties-azure-stream-analytics) 함수와 함께 사용합니다. 예를 들어 개별 센서 판독값을 사용하여 샘플 스트림을 이벤트 스트림으로 변환하려면 다음 쿼리를 사용할 수 있습니다.  

```SQL  
SELECT   
    event.DeviceID,  
    sensorReading.PropertyName,  
    sensorReading.PropertyValue  
FROM input as event  
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading  
```  

중첩된 레코드의 모든 속성은 '*' 와일드카드를 사용하여 선택할 수 있습니다. 다음 예제를 살펴보세요.  

```SQL  
SELECT input.SensorReadings.*  
FROM input  
```  

결과는 다음과 같습니다.  

```json  
{  
    "Temperature" : 80,  
    "Humidity" : 70,  
    "CustomSensor01" : 5,  
    "CustomSensor022" : 99  
}  
```  

## <a name="see-also"></a>관련 항목  
 [Azure Stream Analytics의 데이터 형식](https://msdn.microsoft.com/azure/stream-analytics/reference/data-types-azure-stream-analytics)
 
<!-- Update_Description: new articles on stream analytics parsing json -->
<!--ms.date: 09/17/2018-->