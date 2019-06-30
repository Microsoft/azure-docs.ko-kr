---
title: Azure Stream Analytics에서 JSON 및 AVRO 구문 분석
description: 이 문서에서는 배열, JSON, CSV 형식의 데이터와 같은 복합 데이터 형식을 조작하는 방법을 설명합니다.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: daf5b97e4ac586f89e5964ee16ee73c86f59b01d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329354"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Azure Stream Analytics에서 JSON 및 Avro 데이터 구문 분석

Azure Stream Analytics는 CSV, JSON 및 Avro 데이터 형식에서 이벤트 처리를 지원합니다. JSON 및 Avro 데이터를 구조화할 수 있습니다 및 중첩 된 개체 (레코드) 및 배열과 같은 복합 형식도 포함 합니다. 




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
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```


### <a name="access-nested-fields-in-known-schema"></a>알려진된 스키마의 중첩 된 액세스 필드
쿼리에서 직접 중첩 된 필드에 쉽게 액세스할 점 (.) 표기법을 사용 합니다. 예를 들어이 쿼리는 앞의 JSON 데이터에서 위치 속성 아래 위도 및 경도 좌표를 선택합니다. 아래와 같이 여러 수준을 이동할 점 표기법을 사용할 수 있습니다.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>모든 속성을 선택 합니다.
중첩된 레코드의 모든 속성은 '*' 와일드카드를 사용하여 선택할 수 있습니다. 다음 예제를 살펴보세요.

```SQL
SELECT input.Location.*
FROM input
```

결과는 다음과 같습니다.

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>속성 이름은 변수 때 액세스 중첩 필드
사용 합니다 [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) 속성 이름은 변수 경우 작동 합니다. 

예를 들어, 샘플 데이터 스트림의 각 장치 센서에 대 한 참조 데이터가 포함 된 임계값을 사용 하 여 연결 해야 한다고 가정 합니다. 이러한 참조 데이터의 코드 조각은 다음과 같습니다.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>레코드 필드를 별도 이벤트 변환
레코드 필드를 별도의 이벤트로 변환하려면 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 연산자를 [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) 함수와 함께 사용합니다. 예를 들어 앞의 예제 SensorReading에 대 한 여러 레코드 있으면 다음 쿼리를 다른 이벤트에 압축을 풉니다 사용 수 있습니다.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>배열 데이터 형식

배열 데이터 형식은 정렬된 값의 컬렉션입니다. 배열 값에 일반적인 몇 가지 연산은 아래에 자세히 나와 있습니다. 이러한 예제에서는 입력 이벤트에 배열 데이터 형식인 "arrayField"라는 속성이 있다고 가정합니다.

이러한 예제에서는 [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics) 함수 및 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 연산자를 사용합니다.

### <a name="working-with-a-specific-array-element"></a>특정 배열 요소를 사용 하 여 작업
지정된 인덱스에서 배열 요소를 선택합니다(첫 번째 배열 요소 선택).

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>배열 길이 선택 합니다.

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>배열 요소를 별도 이벤트 변환
모든 배열 요소를 개별 이벤트로 선택합니다. [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 연산자는 [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) 기본 제공 함수와 함께 모든 배열 요소를 개별 이벤트로 추출합니다.

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>관련 항목
[Azure Stream Analytics의 데이터 형식](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
