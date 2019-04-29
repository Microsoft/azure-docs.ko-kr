---
title: Azure Digital Twins API 탐색 | Microsoft Docs
description: Azure Digital Twins 관리 API를 쿼리하는 일반적인 패턴에 대해 알아봅니다.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: 9cfcdc879e36b93e21bff6f91886536d799553bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924497"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Azure Digital Twins 관리 API를 사용하는 방법

Azure Digital Twins 관리 API는 IoT 앱에 대한 강력한 기능을 제공합니다. 이 문서에서는 API 구조를 탐색하는 방법을 보여줍니다.  

## <a name="api-summary"></a>API 요약

다음 목록은 Digital Twins API의 구성 요소를 보여줍니다.

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): 이러한 API는 설정에서 물리적 위치와 상호 작용합니다. [공간 그래프](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)의 양식으로 물리적 위치의 디지털 매핑을 만들고, 삭제 및 관리할 수 있도록 돕습니다.

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): 이러한 API는 설정에서 디바이스와 상호 작용합니다. 이러한 디바이스는 하나 이상의 센서를 관리할 수 있습니다. 예를 들어 사용자의 휴대폰 또는 Raspberry Pi 센서 Pod 또는 Lora 게이트웨이 등을 디바이스로 사용할 수 있습니다.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): 이러한 API는 디바이스와 연결된 센서 및 물리적 위치와 통신하는 데 도움이 됩니다. 센서는 공간 환경을 조작하는 데 사용할 수 있는 앰비언트 값을 기록하고 보냅니다.  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): 이러한 API는 Digital Twins 인스턴스에 대한 IoT Hub와 같은 리소스를 설정하는 데 도움이 됩니다.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): 이러한 API를 사용하면 Digital Twins 개체와 확장된 유형을 연결하여 해당 개체에 특정 특성을 추가할 수 있습니다. 이러한 형식은 UI에서 개체의 쉬운 필터링 및 그룹화를 허용하고 원격 분석 데이터를 처리하는 사용자 지정 함수를 허용합니다. 확장된 유형의 예제는 *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType* 등입니다.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): 이러한 API는 확장된 유형의 컬렉션인 온톨로지를 관리하는 데 도움이 됩니다. 온톨로지는 나타내는 물리적 공간에 따라 개체 형식에 대한 이름을 제공합니다. 예를 들어 *BACnet* 온톨로지는 *sensor types*, *datatypes*, *datasubtypes* 및 *dataunittypes*에 대한 특정 이름을 제공합니다. 온톨로지는 서비스에 의해 관리되고 생성됩니다. 사용자는 온톨로지를 로드 및 언로드할 수 있습니다. 온톨로지가 로드되면 관련된 모든 해당 형식 이름이 활성화되고 공간 그래프에서 프로비전할 준비가 됩니다. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): 이러한 API를 사용하여 *공간*, *디바이스*, *사용자* 및 *센서*에 대한 사용자 지정 속성을 만들 수 있습니다. 이러한 속성은 키/값 쌍으로 생성됩니다. 해당 *PrimitiveDataType*을 설정하여 이러한 속성에 대한 데이터 형식을 정의할 수 있습니다. 예를 들어 센서에 대한 *uint* 형식의 *BasicTemperatureDeltaProcessingRefreshTime*이라는 속성을 정의한 다음, 각 센서에 대한 이 속성의 값을 할당할 수 있습니다. *Min* 및 *Max* 범위와 같은 속성을 만드는 동안 이러한 값 및 *ValidationData*로 허용된 값에 대한 제약 조건을 추가할 수도 있습니다.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): 이러한 API를 사용하여 들어오는 디바이스 데이터를 평가하려는 조건을 지정할 수 있습니다. 자세한 내용은 [이 문서](concepts-user-defined-functions.md#matchers)를 참조하세요. 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): 이러한 API를 통해 *검사기*에 의해 정의된 조건이 발생할 때 실행되는 사용자 지정 함수를 만들고, 삭제 또는 업데이트하여 설치 프로그램에서 가져온 데이터를 처리할 수 있습니다. *사용자 정의 함수*라고도 하는 이러한 사용자 지정 함수에 대한 자세한 내용은 [이 문서](concepts-user-defined-functions.md#user-defined-functions)를 참조하세요. 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): 이러한 API를 사용하면 Digital Twins 솔루션에서 데이터 스토리지 및 분석에 대해 다른 Azure 서비스와 통신할 수 있도록 엔드포인트를 만들 수 있습니다. 자세한 내용은 [이 문서](concepts-events-routing.md)를 참조하세요. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): 이러한 API를 사용하면 공간에 대한 보안 키 저장소를 관리할 수 있습니다. 이러한 저장소는 보안 키의 컬렉션을 포함하고, 유효한 최신 키를 쉽게 검색할 수 있도록 합니다.

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): 이러한 API를 사용하면 사용자를 공간과 연결하여 필요한 경우 이러한 개인을 찾을 수 있습니다. 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): 이러한 API를 사용하면 공간 및 센서의 기본 형식과 같은 시스템 수준 설정을 관리할 수 있습니다. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): 이러한 API를 사용하면 사용자 ID, 사용자 정의 함수 ID 등과 같은 엔터티에 역할을 연결할 수 있습니다. 각 역할 할당은 연결할 엔터티의 ID, 엔터티 형식, 연결할 역할의 ID, 테넌트 ID 및 엔터티에서 해당 연결을 사용하여 액세스할 수 있는 리소스의 상한값을 정의하는 경로를 포함합니다. 자세한 내용은 [이 문서](security-role-based-access-control.md)를 참조하세요.


## <a name="api-navigation"></a>API 탐색

Digital Twins API는 다음 매개 변수를 사용하여 공간 그래프 전체에 대한 필터링 및 탐색을 지원합니다.

- **spaceId**: API는 지정된 공간 ID를 기준으로 결과를 필터링합니다. 또한 지정된 공간 ID가 현재 공간 대신 부모 공간을 나타냄을 표시하는 [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) API에 부울 플래그 **useParentSpace**를 적용할 수 있습니다. 

- **minLevel** 및 **maxLevel**: 루트 공간은 1 수준으로 간주됩니다. *n* 수준에서 부모 공간이 있는 공간은 수준 *n+1*입니다. 이러한 값을 설정하여 특정 수준에서 결과를 필터링 할 수 있습니다. 설정되었을 때 결과는 포괄적인 값입니다. 디바이스, 센서 및 기타 개체는 가장 가까운 공간과 동일한 수준으로 간주됩니다. 지정된 수준에서 모든 개체를 가져오려면 **minLevel** 및 **maxLevel**을 동일한 값으로 설정합니다.

- **minRelative** 및 **maxRelative**: 이러한 필터가 지정된 경우 해당 수준은 지정된 공간 ID의 수준을 기준으로 합니다.
   - 상대 수준 *0*은 지정된 공간 ID와 동일한 수준입니다.
   - 상대 수준 *1*은 지정된 공간 ID의 자식과 동일한 수준에서 공간을 나타냅니다. 상대 수준 *n*은 *n* 수준으로 지정된 공간보다 낮은 공간을 나타냅니다.
   - 상대 수준 *-1*은 지정된 공간의 부모 공간과 동일한 수준에서 공간을 나타냅니다.

- **트래버스**: 다음 값으로 지정된 것과 같이 지정된 공간 ID의 방향에서 트래버스할 수 있습니다.
   - **없음**: 이 기본 값은 지정된 공간 ID로 필터링합니다.
   - **아래로**: 지정된 공간 ID 및 해당 하위 항목으로 필터링합니다. 
   - **위로**: 지정된 공간 ID 및 해당 상위 항목으로 필터링합니다. 
   - **범위**: 지정된 공간 ID와 동일한 수준에서 공간 그래프의 가로 부분을 필터링합니다. **minRelative** 또는 **maxRelative**를 true로 설정해야 합니다. 


### <a name="examples"></a>예

다음 목록은 [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API를 통한 탐색의 몇 가지 예제를 보여줍니다. 자리 표시자 `YOUR_MANAGEMENT_API_URL`은 `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/` 양식으로 Digital Twins API의 URI를 나타냅니다. 여기서 `YOUR_INSTANCE_NAME`은 Azure Digital Twins 인스턴스의 이름이며, `YOUR_LOCATION`은 인스턴스가 호스팅되는 지역입니다.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1`은 루트 공간에 연결된 모든 디바이스를 반환합니다.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4`는 수준 2, 3 또는 4의 공간에 연결된 모든 디바이스를 반환합니다.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId`는 mySpaceId에 직접 연결된 모든 디바이스를 반환합니다.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down`은 mySpaceId 또는 해당 하위 항목 중 하나에 연결된 모든 디바이스를 반환합니다.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true`는 mySpaceId를 제외한 mySpaceId의 하위 항목에 연결된 모든 디바이스를 반환합니다.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true`는 mySpaceId의 직접 자식에 연결된 모든 디바이스를 반환합니다.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true`는 mySpaceId의 상위 항목 중 하나에 연결된 모든 디바이스를 반환합니다.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5`는 5보다 작거나 같은 수준에 있는 mySpaceId의 하위 항목에 연결된 모든 디바이스를 반환합니다.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true`는 mySpaceId와 동일한 수준에 있는 공간에 연결된 모든 디바이스를 반환합니다.


## <a name="odata-support"></a>OData 지원
/spaces에서 GET 호출과 같은 컬렉션을 반환하는 대부분의 API는 일반 [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) 시스템 쿼리 옵션의 다음과 같은 하위 집합을 지원합니다.  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** - 전체 컬렉션을 표시하려는 경우 단일 호출에서 전체 집합으로 요청한 다음, 애플리케이션에서 페이징을 수행해야 합니다. 

$count, $expand, $search와 같은 다른 쿼리 옵션은 지원되지 않습니다.

### <a name="examples"></a>예

다음 목록은 OData의 시스템 쿼리 옵션을 사용하여 쿼리하는 몇 가지 예제를 보여줍니다.

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 

## <a name="next-steps"></a>다음 단계

몇 가지 일반적인 API 쿼리 패턴에 대해 알아보려면 [일반적인 작업에 대해 Azure Digital Twins API를 쿼리하는 방법](how-to-query-common-apis.md)을 참조하세요.


