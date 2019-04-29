---
title: Azure IoT Hub 쿼리 언어 | Microsoft Docs
description: 개발자 가이드 - IoT Hub에서 디바이스/모듈 쌍 및 작업에 대한 정보를 검색하는 데 사용되는 SQL 유형의 IoT Hub 쿼리 언어에 대한 설명
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: rezas
ms.openlocfilehash: e5387f1e44a55b0a30f8620b49d237ac1e1ec2b6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442140"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>디바이스 및 모듈 쌍, 작업 및 메시지 라우팅에 대한 IoT Hub 쿼리 언어

IoT Hub는 [디바이스 쌍](iot-hub-devguide-device-twins.md) 및 [작업](iot-hub-devguide-jobs.md) 그리고 [메시지 라우팅](iot-hub-devguide-messages-d2c.md)과 관련된 정보를 검색할 수 있는 강력한 SQL 유형의 언어를 제공합니다. 이 문서에 제공되는 내용:

* IoT Hub 쿼리 언어의 주요 기능 소개 및
* 언어에 대한 자세한 설명 메시지 라우팅의 쿼리 언어에 대한 자세한 내용은 [메시지 라우팅의 쿼리](../iot-hub/iot-hub-devguide-routing-query-syntax.md)를 참조하세요.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>디바이스 및 모듈 쌍 쿼리

[디바이스 쌍](iot-hub-devguide-device-twins.md) 및 모듈 쌍은 임의의 JSON 개체를 태그와 속성으로 포함할 수 있습니다. IoT Hub를 사용하면 모든 쌍 정보를 포함하는 단일 JSON 문서로 디바이스 쌍 및 모듈 쌍을 쿼리할 수 있습니다.

예를 들어 IoT 허브 디바이스 쌍에 다음 구조가 있다고 가정합니다(모듈 쌍은 추가 moduleId와 유사함).

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>디바이스 쌍 쿼리

IoT Hub는 **devices**라는 문서 컬렉션으로 디바이스 쌍을 노출합니다. 예를 들어 다음 쿼리는 디바이스 쌍 전체 집합을 검색합니다.

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md)는 큰 결과의 페이징을 지원합니다.

IoT Hub는 임의의 조건으로 디바이스 쌍 필터링을 검색하도록 허용합니다. 예를 들어 **location.region** 태그가 **US**로 설정된 디바이스 쌍을 받으려면 다음 쿼리를 사용합니다.

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

부울 연산자 및 산술 비교도 지원됩니다. 예를 들어 미국에 있으며 1분 이하를 주기로 원격 분석 데이터를 보내도록 구성된 디바이스 쌍을 검색하려면 다음 쿼리를 사용합니다.

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

편의를 위해 **IN** 및**NIN**(IN이 아님) 연산자와 함께 배열 상수를 사용할 수도 있습니다. 예를 들어 WiFi 또는 유선 연결을 보고하는 디바이스 쌍을 검색하려면 다음 쿼리를 사용합니다.

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

특정 속성을 포함하는 모든 디바이스 쌍을 식별해야 하는 경우가 종종 있습니다. IoT Hub는 이러한 용도로 `is_defined()` 함수를 지원합니다. 예를 들어 `connectivity` 속성을 정의하는 디바이스 쌍을 검색하려면 다음 쿼리를 사용합니다.

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

필터링 기능에 대한 전체 참조는 [WHERE 절](iot-hub-devguide-query-language.md#where-clause) 섹션을 참조하세요.

그룹화 및 집계도 지원됩니다. 예를 들어 각 원격 분석 구성 상태에서 디바이스 수를 찾으려면 다음 쿼리를 사용합니다.

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

이 그룹화 쿼리는 다음 예제와 비슷한 결과를 반환합니다.

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

이 예제에서는 세 디바이스는 성공적인 구성을 보고하고 두 디바이스는 구성을 계속 적용하고 있으며 하나는 오류를 보고했습니다.

개발자는 프로젝션 쿼리를 사용하여 관심 있는 속성만 반환할 수 있습니다. 예를 들어, 연결된 모든 디바이스의 마지막 작업 시간을 검색하려면 다음 쿼리를 사용합니다.

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>모듈 쌍 쿼리

모듈 쌍에 대한 쿼리는 디바이스 쌍에 대한 쿼리와 유사하지만, "디바이스에서"가 아니라 다른 컬렉션/네임스페이스를 사용하여 device.modules를 쿼리할 수 있습니다.

```sql
SELECT * FROM devices.modules
```

디바이스 및 devices.modules 컬렉션 간의 조인을 허용하지 않습니다. 디바이스 간에 모듈 쌍을 쿼리하려는 경우 태그에 따라 수행합니다. 이 쿼리는 검색 상태와 함께 모든 디바이스에서 모든 모듈 쌍을 반환합니다.

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

이 쿼리는 검색 상태와 함께 모든 모듈 쌍을 반환하지만, 지정된 하위 집합의 디바이스에서만 반환합니다.

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>C# 예제

쿼리 기능은 [C# 서비스 SDK](iot-hub-devguide-sdks.md)의 **RegistryManager** 클래스를 통해 공개됩니다.

다음은 간단한 예제 쿼리입니다.

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

**query** 개체는 페이지 크기로 인스턴스화됩니다(최대 100). 그런 후 **GetNextAsTwinAsync** 메서드를 여러 번 호출하여 여러 페이지가 검색됩니다.

query 개체는 쿼리에 필요한 역직렬화 옵션에 따라 여러 개의 **Next** 값을 노출합니다. 프로젝션을 사용할 경우의 디바이스 쌍이나 작업 개체 또는 일반 JSON을 예로 들 수 있습니다.

### <a name="nodejs-example"></a>Node.js 예제

쿼리 기능은 [Node.js용 Azure IoT 서비스 SDK](iot-hub-devguide-sdks.md)의 **Registry** 개체에서 공개됩니다.

다음은 간단한 예제 쿼리입니다.

```javascript
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

**query** 개체는 페이지 크기로 인스턴스화됩니다(최대 100). 그런 후 **nextAsTwin** 메서드를 여러 번 호출하여 여러 페이지가 검색됩니다.

query 개체는 쿼리에 필요한 역직렬화 옵션에 따라 여러 개의 **Next** 값을 노출합니다. 프로젝션을 사용할 경우의 디바이스 쌍이나 작업 개체 또는 일반 JSON을 예로 들 수 있습니다.

### <a name="limitations"></a>제한 사항

> [!IMPORTANT]
> 쿼리 결과는 디바이스 쌍의 최신 값에 따라 몇 분 정도 지연될 수 있습니다. ID별로 개별 디바이스 쌍을 쿼리하는 경우 디바이스 쌍 검색 API를 사용합니다. 이 API는 항상 최신 값을 포함하며 더 높은 제한을 적용합니다.

현재 비교는 기본 형식(개체 없음) 간에만 지원됩니다. 예를 들어 `... WHERE properties.desired.config = properties.reported.config`는 해당 속성에 기본 값이 있는 경우에만 지원됩니다.

## <a name="get-started-with-jobs-queries"></a>작업 쿼리 시작

[작업](iot-hub-devguide-jobs.md)은 디바이스 집합에 대해 작업을 실행하는 방법을 제공합니다. 각 디바이스 쌍은 작업에 대한 정보를 포함하며 이것은 **jobs**라는 컬렉션에 속합니다.

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

현재 이 컬렉션은 IoT Hub 쿼리 언어를 사용하여 **devices.jobs**로 쿼리할 수 있습니다.

> [!IMPORTANT]
> 현재 jobs 속성은 디바이스 쌍을 쿼리하는 경우에는 반환되지 않습니다. ‘FROM devices’를 포함하는 쿼리가 여기에 해당합니다. 작업 속성은 `FROM devices.jobs`를 사용하여 쿼리를 통해서만 직접 액세스할 수 있습니다.
>
>

예를 들어 단일 디바이스에 영향을 미치는 모든 작업(지난 작업 및 예정된 작업)을 가져오려면 다음 쿼리를 사용합니다.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

이 쿼리가 반환된 각 작업의 디바이스별 상태(및 가능한 경우 직접 메서드 응답)를 제공하는 방법에 유의합니다.

**devices.jobs** 컬렉션의 모든 개체 속성에 대해 임의의 부울 조건으로 필터링할 수도 있습니다.

예를 들어, 특정 디바이스에 대해 2016년 9월 이후에 작성되어 완료된 디바이스 트윈 업데이트 작업을 모두 검색하려면 다음 쿼리를 사용합니다.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

단일 작업의 장치별 결과를 검색할 수도 있습니다.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>제한 사항

현재 **devices.jobs**에 대한 쿼리는 다음을 지원하지 않습니다.

* 프로젝션(따라서 `SELECT *`만 가능)
* 작업 속성 외에 디바이스 쌍을 참조하는 조건(앞 섹션 참조)
* 집계 수행(예: count, avg, group by)

## <a name="basics-of-an-iot-hub-query"></a>IoT Hub 쿼리의 기초

모든 IoT Hub 쿼리는 SELECT 및 FROM 절로 이루어지며 선택적으로 WHERE 및 GROUP BY 절이 포함됩니다. 모든 쿼리는 JSON 문서(예: 디바이스 쌍) 컬렉션에 대해 실행됩니다. FROM 절은 반복이 수행될 문서 컬렉션을 나타냅니다(예: **devices** 또는 **devices.jobs**). 그런 다음 WHERE 절의 필터가 적용됩니다. 집계를 사용할 경우 이 단계의 결과는 GROUP BY 절에 지정된 대로 그룹화됩니다. 각 그룹에 대해 SELECT 절에 지정된 대로 행이 생성됩니다.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM 절

**FROM <from_specification>** 절은 두 가지 값, 즉, 디바이스 쌍을 쿼리하는 **FROM devices** 또는 디바이스별 세부 정보를 쿼리하는 **FROM devices.jobs**만 가정할 수 있습니다.


## <a name="where-clause"></a>WHERE 절
**WHERE <filter_condition>** 절은 선택 사항입니다. FROM 컬렉션의 JSON 문서가 결과의 일부로 포함되기 위해 충족해야 하는 하나 이상의 조건을 지정합니다. JSON 문서가 결과에 포함되려면 지정된 조건을 "true"로 평가해야 합니다.

허용되는 조건은 [식 및 조건](iot-hub-devguide-query-language.md#expressions-and-conditions) 섹션에 설명되어 있습니다.

## <a name="select-clause"></a>SELECT 절

**SELECT <select_list>** 는 필수이며 쿼리에서 검색되는 값을 지정합니다. 새 JSON 개체를 생성하는 데 사용될 JSON 값을 지정합니다.
FROM 컬렉션의 필터링된(그리고 선택적으로 그룹화된) 하위 집합의 각 요소에 대해 프로젝션 단계는 새 JSON 개체를 생성합니다. 이 개체는 SELECT 절에 지정된 값으로 구성됩니다.

다음은 SELECT 절의 문법입니다.

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name**은 FROM 컬렉션에 있는 JSON 문서의 속성을 참조합니다. SELECT 절에 대한 일부 예제는 디바이스 쌍 쿼리 시작 섹션에서 확인할 수 있습니다.

현재 **SELECT***와 다른 선택 절은 디바이스 쌍에 대한 집계 쿼리에서만 지원됩니다.

## <a name="group-by-clause"></a>GROUP BY 절
**GROUP BY <group_specification>** 절은 WHERE 절에 지정된 필터 뒤에서, 그리고 SELECT에 지정된 프로젝션 앞에서 실행되는 선택적 단계입니다. 특성의 값을 기반으로 문서를 그룹화합니다. 이러한 그룹은 SELECT 절에 지정된 대로 집계된 값을 생성하는 데 사용됩니다.

GROUP BY를 사용한 쿼리의 예:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

GROUP BY에 대한 형식 구문:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name**은 FROM 컬렉션에 있는 JSON 문서의 속성을 참조합니다.

현재 GROUP BY 절은 디바이스 쌍을 쿼리하는 경우에만 지원됩니다.

> [!IMPORTANT]
> 용어 `group`은 현재 쿼리에서 특수 키워드로 취급됩니다. 이 경우 속성 이름으로 `group`을 사용하고 오류를 방지하기 위해 이중 괄호를 사용하여 묶는 것이 좋습니다. 예를 들면 `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'`입니다.
>
>

## <a name="expressions-and-conditions"></a>식 및 조건
높은 수준에서 *식*은:

* JSON 형식(예: 부울, 숫자, 문자열, 배열 또는 개체)의 인스턴스로 평가됩니다.
* 기본 제공되는 연산자와 함수를 사용하여 상수 및 디바이스 JSON 문서에서 오는 조작 데이터에 의해 정의됩니다.

*조건*은 부울 값으로 평가되는 식입니다. 부울 **true**와는 다른 상수는 **false**로 간주됩니다. 이 규칙에는 **null**, **정의되지 않음**, 개체 또는 배열 인스턴스, 문자열 및 부울 **false**가 포함됩니다.

식에 대한 구문:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

식 구문의 각 기호가 나타내는 의미를 이해하려면 다음 테이블을 참조하세요.

| 기호 | 정의 |
| --- | --- |
| attribute_name | **FROM** 컬렉션에 있는 JSON 문서의 모든 속성입니다. |
| binary_operator | [연산자](#operators) 섹션에서 나열한 모든 이항 연산자입니다. |
| function_name| [함수](#functions) 섹션에서 나열한 모든 함수입니다. |
| decimal_literal |소수 표기법으로 표현되는 부동 float입니다. |
| hexadecimal_literal |뒤에 16진수 문자열이 붙는 ‘0x’ 문자열로 표현되는 숫자입니다. |
| string_literal |문자열 리터럴은 연속적인 0이상의 유니코드 문자 또는 이스케이프 시퀀스로 표현되는 유니코드 문자열입니다. 문자열 리터럴은 작은 따옴표나 큰 따옴표로 묶습니다. 허용되는 이스케이프: `\'`, `\"`, `\\`, 4개의 16진수로 정의되는 유니코드 문자인 경우 `\uXXXX` |

### <a name="operators"></a>연산자
다음과 같은 연산자가 지원됩니다.

| 패밀리 | 연산자 |
| --- | --- |
| 산술 |+, -, *, /, % |
| 논리 |AND, OR, NOT |
| 비교 |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions
쌍과 작업을 쿼리할 때 지원되는 유일한 함수는 다음과 같습니다.

| 함수 | 설명 |
| -------- | ----------- |
| IS_DEFINED(속성) | 속성에 값(`null` 포함)이 할당되었는지 여부를 나타내는 부울 값을 반환합니다. |

경로 조건에서 지원되는 수학 함수는 다음과 같습니다.

| 함수 | 설명 |
| -------- | ----------- |
| ABS(x) | 지정한 숫자 식의 절대(양수) 값을 반환합니다. |
| EXP(x) | 지정한 숫자 식(e^x)의 지수 값을 반환합니다. |
| POWER(x,y) | 지정한 식의 값을 지정한 거듭제곱(x^y)으로 반환합니다.|
| SQUARE(x) | 지정한 숫자 값의 제곱을 반환합니다. |
| CEILING(x) | 지정한 숫자 식보다 크거나 같은 가장 작은 정수 값을 반환합니다. |
| FLOOR(x) | 지정한 숫자 식보다 작거나 같은 가장 큰 정수 값을 반환합니다. |
| SIGN(x) | 지정한 숫자 식의 양수(+1), 0(0) 또는 음수(-1) 부호를 반환합니다.|
| SQRT(x) | 지정된 숫자 값의 제곱근을 반환합니다. |

경로 조건에서 지원되는 형식 검사 및 캐스팅 함수는 다음과 같습니다.

| 함수 | 설명 |
| -------- | ----------- |
| AS_NUMBER | 입력 문자열을 숫자로 변환합니다. 입력이 숫자이면 `noop`이고, 문자열이 숫자를 나타내지 않으면 `Undefined`입니다.|
| IS_ARRAY | 지정한 식의 형식이 배열인지 여부를 나타내는 부울 값을 반환합니다. |
| IS_BOOL | 지정한 식의 형식이 부울인지 여부를 나타내는 부울 값을 반환합니다. |
| IS_DEFINED | 속성이 값을 할당할지를 나타내는 부울 값을 반환합니다. |
| IS_NULL | 지정한 식의 형식이 널인지 여부를 나타내는 부울 값을 반환합니다. |
| IS_NUMBER | 지정한 식의 형식이 숫자인지 여부를 나타내는 부울 값을 반환합니다. |
| IS_OBJECT | 지정한 식의 형식이 JSON 개체인지 여부를 나타내는 부울 값을 반환합니다. |
| IS_PRIMITIVE | 지정한 식의 형식이 기본 형식(문자열, 부울, 숫자 또는 `null`)인지 여부를 나타내는 부울 값을 반환합니다. |
| IS_STRING | 지정한 식의 형식이 문자열인지 여부를 나타내는 부울 값을 반환합니다. |

경로 조건에서 지원되는 문자열 함수는 다음과 같습니다.

| 함수 | 설명 |
| -------- | ----------- |
| CONCAT(x, y, …) | 둘 이상의 문자열 값을 연결한 결과인 문자열을 반환합니다. |
| LENGTH(x) | 지정한 문자열 식의 문자 수를 반환합니다.|
| LOWER(x) | 대문자 데이터를 소문자로 변환한 후에 문자열 식을 반환합니다. |
| UPPER(x) | 소문자 데이터를 대문자로 변환한 후에 문자열 식을 반환합니다. |
| SUBSTRING(string, start [, length]) | 지정한 문자 0 기준 위치에서 시작하여 지정한 길이 또는 문자열의 끝까지에 이르는 문자열 식의 일부를 반환합니다. |
| INDEX_OF(string, fragment) | 지정된 첫 번째 문자열 식 내의 두 번째 문자열 식에서 첫 번째로 나타나는 시작 위치를 반환하거나 문자열을 찾을 수 없는 경우 -1을 반환합니다.|
| STARTS_WITH(x, y) | 첫 번째 문자열 식이 두 번째 문자열 식에서 시작하는지 여부를 나타내는 부울 값을 반환합니다. |
| ENDS_WITH(x, y) | 첫 번째 문자열 식이 두 번째 문자열 식에서 끝나는지 여부를 나타내는 부울 값을 반환합니다. |
| CONTAINS(x,y) | 첫 번째 문자열 식이 두 번째를 포함하는지를 나타내는 부울 값을 반환합니다. |

## <a name="next-steps"></a>다음 단계

[Azure IoT SDK](iot-hub-devguide-sdks.md)를 사용하여 앱에서 쿼리를 수행하는 방법을 알아봅니다.
