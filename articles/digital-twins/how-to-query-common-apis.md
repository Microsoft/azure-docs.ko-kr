---
title: Azure Digital Twins 일반적인 쿼리 패턴 | Microsoft Docs
description: Azure Digital Twins 관리 API를 쿼리하는 일반적인 패턴에 대해 알아봅니다.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: ff8638042fa10c939ff9c5fa7af99a660fcdc753
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924739"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>일반적인 작업에 대해 Azure Digital Twins API를 쿼리하는 방법

이 문서에서는 Azure Digital Twins 인스턴스에 대해 일반적인 시나리오를 실행하는 데 도움이 되는 쿼리 패턴을 보여줍니다. Digital Twins 인스턴스가 이미 실행되고 있다고 가정합니다. Postman과 같은 REST 클라이언트를 사용할 수 있습니다. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>공백 및 형식에 대한 쿼리

이 섹션에서는 프로비전된 공간에 대한 자세한 정보를 가져오는 샘플 쿼리를 보여줍니다. 자리 표시자를 설치 프로그램의 값으로 대체하여 샘플 쿼리로 인증된 GET HTTP 요청을 만듭니다. 

- 루트 노드인 공간을 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- 이름별로 공간을 가져오고 디바이스, 센서, 계산된 값 및 센서 값을 포함합니다. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- [지정된 공간을 기준](how-to-navigate-apis.md#api-navigation)으로 수준 2~5에 있으며 부모가 지정된 공간 ID인 공간 및 해당 디바이스/센서 정보를 가져옵니다. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- 지정된 ID가 있는 공간을 가져오고, 계산된 값 및 센서 값을 포함합니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- 특정 공간에 대한 속성 키를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- *AreaInSqMeters*라는 속성 키가 있는 공간을 가져오며 해당 값은 30입니다. 문자열 작업을 수행할 수도 있습니다. 예를 들어 `name = X contains Y`를 사용하여 속성 키를 포함하는 공간을 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- *온도*라는 이름의 모든 이름과 관련 종속성 및 온톨로지를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>역할 및 역할 할당에 대한 쿼리

이 섹션에서는 역할 및 해당 할당에 대한 자세한 정보를 가져오는 일부 쿼리를 보여줍니다. 

- Azure Digital Twins에서 지원되는 모든 역할을 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Digital Twins 인스턴스에서 모든 역할 할당을 가져옵니다. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- 특정 경로의 역할 할당을 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>디바이스 쿼리

이 섹션에서는 디바이스에 대한 특정 정보를 가져오는 관리 API를 사용하는 방법의 몇 가지 예제를 보여줍니다. 모든 API 호출은 GET HTTP 요청으로 인증되어야 합니다.

- 모든 디바이스를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- 모든 디바이스 상태를 찾습니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- 특정 디바이스를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- 루트 공간에 연결된 모든 디바이스를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- 수준 2~4에서 공간에 연결된 모든 디바이스를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- 특정 공간 ID에 직접 연결된 모든 디바이스를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- 특정 공간 및 해당 하위 항목에 연결된 모든 디바이스를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- 해당 공간을 제외하고 공백의 하위 항목에 연결된 모든 디바이스를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- 공간의 직접 자식에 연결된 모든 디바이스를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- 공간의 상위 항목 중 하나에 연결된 모든 디바이스를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- 수준 5보다 작거나 같은 공간의 하위 항목에 연결된 모든 디바이스를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- ID *YOUR_SPACE_ID*의 공간과 동일한 수준에 있는 공간에 연결된 모든 디바이스를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- 사용 중인 디바이스의 IoT Hub 디바이스 연결 문자열을 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- 연결된 센서를 포함하여 지정된 하드웨어 ID가 있는 디바이스를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- 특정 데이터 형식에 대한 센서를 가져옵니다. 이 경우 *모션* 및 *온도*입니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>검사기 및 사용자 정의 함수에 대한 쿼리 

- 프로비전된 모든 검사기 및 해당 ID를 가져옵니다.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- 연결된 공간 및 사용자 정의 함수를 포함하여 특정 검사기에 대한 세부 정보를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- 센서에 대해 선택기를 평가하고 디버깅 목적으로 로깅을 사용하도록 설정합니다. 이 HTTP GET 메시지의 반환은 검사기 및 센서가 데이터 형식에 속하는지 여부를 알려 줍니다. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- 사용자 정의 함수의 ID를 가져옵니다. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- 특정 사용자 정의 함수의 콘텐츠 가져오기 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>사용자에 대한 쿼리

이 섹션에서는 Azure Digital Twins에서 사용자 관리를 위한 몇 가지 샘플 API 쿼리를 보여줍니다. 자리 표시자를 설치 프로그램의 값으로 대체하여 HTTP GET 요청을 만듭니다. 

- 모든 사용자를 가져옵니다. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- 특정 사용자를 가져옵니다.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>다음 단계

관리 API를 사용하여 인증하는 방법을 알아보려면 [API를 사용하여 인증](./security-authenticating-apis.md)을 참고하세요.

모든 API 엔드포인트를 확인하려면 [Digital Twins Swagger를 사용하는 방법](./how-to-use-swagger.md)을 참조하세요.
