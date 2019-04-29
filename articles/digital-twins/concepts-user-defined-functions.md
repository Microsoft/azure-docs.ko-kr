---
title: Azure Digital Twins의 데이터 처리 및 사용자 정의 함수 | Microsoft Docs
description: Azure Digital Twins의 데이터 처리, 검사기 및 사용자 정의 함수의 개요입니다.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.openlocfilehash: 4db515a931bc7f423eb11ae31b7304a602f0da46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60925922"
---
# <a name="data-processing-and-user-defined-functions"></a>데이터 처리 및 사용자 정의 함수

Azure Digital Twins는 고급 계산 기능을 제공합니다. 개발자는 들어오는 원격 분석 메시지에 대해 사용자 지정 함수를 정의하고 실행하여 미리 정의된 엔드포인트에 이벤트를 보낼 수 있습니다.

## <a name="data-processing-flow"></a>데이터 처리 흐름

디바이스에서 원격 분석 데이터를 Azure Digital Twins에 보내면 개발자는 *유효성 검사*, *일치*, *계산* 및 *디스패치*의 네 가지 단계로 데이터를 처리할 수 있습니다.

![Azure Digital Twins 데이터 처리 흐름][1]

1. 유효성 검사 단계에서는 들어오는 원격 분석 메시지를 일반적으로 이해되는 [데이터 전송 개체](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5)형식으로 변환합니다. 이 단계에서는 디바이스 및 센서 유효성 검사도 실행합니다.
1. 일치 단계는 실행할 적절한 사용자 정의 함수를 찾습니다. 미리 정의된 검사기가 들어오는 원격 분석 메시지의 디바이스, 센서 및 공간 정보를 기반으로 사용자 정의 함수를 찾습니다.
1. 계산 단계는 이전 단계에서 일치한 사용자 정의 함수를 실행합니다. 이러한 함수는 공간 그래프 노드에서 계산된 값을 읽고 업데이트할 수 있으며 사용자 지정 알림을 내보낼 수 있습니다.
1. 디스패치 단계는 계산 단계에서 그래프에 정의된 엔드포인트로 사용자 지정 알림을 라우팅합니다.

## <a name="data-processing-objects"></a>데이터 처리 개체

Azure Digital Twins의 데이터 처리는 *검사기*, *사용자 정의 함수* 및 *역할 할당*이라는 세 가지 개체로 구성됩니다.

![Azure Digital Twins 데이터 처리 개체][2]

<div id="matcher"></div>

### <a name="matchers"></a>검사기

검사기는 들어오는 센서 원격 분석을 기반으로 수행할 작업을 평가하는 조건 집합을 정의합니다. 일치를 판별하기 위한 조건에는 센서의 속성, 센서의 상위 디바이스, 센서의 상위 공간이 포함될 수 있습니다. 조건은 이 예제에 설명된 대로 [JSON 경로](https://jsonpath.com/)에 대한 비교로 표현됩니다.

- **온도** 데이터 형식의 모든 센서는 이스케이프된 문자열 값 `\"Temperature\"`로 표현됩니다.
- 포트에 `01`이 있습니다.
- 이스케이프된 문자열 값 `\"GoodCorp\"`로 설정된 확장 속성 키 **제조업체**가 있는 디바이스에 속합니다.
- 이스케이프된 문자열 `\"Venue\"`에서 지정된 형식의 공간에 속합니다.
- 상위 **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`의 하위 항목입니다.

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - JSON 경로는 대/소문자를 구분합니다.
> - JSON 페이로드는 다음에 의해 반환되는 페이로드와 동일합니다.
>   - 센서의 `/sensors/{id}?includes=properties,types`
>   - 센서의 상위 디바이스의 `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`
>   - 센서의 상위 공간의 `/spaces/{id}?includes=properties,types,location,timezone`
> - 모든 비교는 대/소문자를 구분하지 않습니다.

### <a name="user-defined-functions"></a>사용자 정의 함수

사용자 정의 함수는 격리된 Azure Digital Twins 환경 내에서 실행된 사용자 지정 함수입니다. 사용자 정의 함수는 수신되는 원시 센서 원격 분석 메시지에 액세스할 수 있습니다. 사용자 정의 함수는 공간 그래프 및 디스패처 서비스에 액세스할 수도 있습니다. 사용자 정의 함수가 그래프 내에 등록되면 함수를 실행할 시기를 지정하도록 선택기([위](#matcher)에서 자세히 설명)를 만들어야 합니다. 예를 들어, Azure Digital Twins가 지정 센서에서 새 원격 분석을 수신하는 경우 일치하는 사용자 정의 함수는 마지막 몇 개의 센서 값에 대한 이동 평균을 계산할 수 있습니다.

사용자 정의 함수는 JavaScript로 작성될 수 있습니다. 도우미 메서드는 사용자 정의 실행 환경에서 그래프와 상호 작용합니다. 개발자는 센서 원격 분석 메시지에 대해 사용자 지정 코드 조각을 실행할 수 있습니다. 다음은 이러한 템플릿의 예입니다.

- 센서 값을 그래프 내의 센서 개체에 직접 설정합니다.
- 다양한 센서 값을 기반으로 그래프의 공간 내에서 작업을 수행합니다.
- 들어오는 센서 값에 대해 특정 조건이 충족되면 알림을 생성합니다.
- 알림을 전송하기 전에 센서 값에 그래프 메타데이터를 첨부합니다.

자세한 내용은 [사용자 정의 함수를 사용하는 방법](./how-to-user-defined-functions.md)을 참조하세요.


#### <a name="examples"></a>예

[Digital Twins C# 샘플에 대한 GitHub 리포지토리](https://github.com/Azure-Samples/digital-twins-samples-csharp/)에는 사용자 정의 함수의 몇 가지 예가 포함되어 있습니다.
- [이 함수](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js)는 이산화탄소, 동작 및 온도 값을 찾아 범위 내에서 이러한 값으로 객실을 사용할 수 있는지 여부를 결정합니다. [Digital Twins 자습서](tutorial-facilities-udf.md)에 이 함수에 대해 보다 자세히 나와 있습니다. 
- [이 함수](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js)는 여러 동작 센서에서 데이터를 찾은 후 어떤 동작 센서로도 동작이 검색되지 않는 경우 공간을 사용할 수 있음을 결정합니다. 파일의 주석 섹션에 설명된 변경을 수행하여 [빠른 시작](quickstart-view-occupancy-dotnet.md) 또는 [자습서](tutorial-facilities-setup.md)에 사용된 사용자 정의 함수를 쉽게 바꿀 수 있습니다. 



### <a name="role-assignment"></a>역할 할당

사용자 정의 함수의 작업은 Azure Digital Twins의 [역할 기반 액세스 제어](./security-role-based-access-control.md)에 따라 서비스 내의 데이터를 보호합니다. 역할 할당은 공간 그래프 및 해당 엔터티와 상호 작용할 수 있는 적절한 사용 권한이 있는 사용자 정의 함수를 정의합니다. 예를 들어, 사용자 정의 함수에는 지정된 공간에서 그래프 데이터에 대한 *만들기*, *읽기*, *업데이트* 또는 *삭제* 기능 및 사용 권한이 있습니다. 사용자 정의 함수의 액세스 수준은 해당 함수가 데이터에 대한 그래프를 요청하거나 작업을 시도할 때 검사됩니다. 자세한 내용은 [역할 기반 액세스 제어](./security-create-manage-role-assignments.md)를 참조하세요.

검사기가 역할 할당이 없는 사용자 정의 함수를 트리거할 수 있습니다. 이 경우에 사용자 정의 함수는 그래프에서 데이터를 읽을 수 없습니다.

## <a name="next-steps"></a>다음 단계

- 이벤트 및 원격 분석 메시지를 다른 Azure 서비스로 라우팅하는 방법에 대한 자세한 내용은 [이벤트 및 메시지 라우팅](./concepts-events-routing.md)을 참조하세요.

- 검사기, 사용자 정의 함수 및 역할 할당을 생성하는 방법에 대한 자세한 내용은 [Guide for using user-defined functions](./how-to-user-defined-functions.md)(사용자 정의 함수 사용 가이드)를 참조하세요.

- [사용자 정의 함수 클라이언트 라이브러리 참조 설명서](./reference-user-defined-functions-client-library.md)를 검토하세요.

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
