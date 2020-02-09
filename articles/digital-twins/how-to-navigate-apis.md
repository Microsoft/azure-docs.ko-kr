---
title: Api 탐색-Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins 관리 API를 쿼리하는 일반적인 패턴에 대해 알아봅니다.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 86ade45cd00e82e8787a117c23003d2a74750cf0
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552171"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Azure Digital Twins 관리 API를 사용하는 방법

Azure Digital Twins 관리 API는 IoT 앱에 대한 강력한 기능을 제공합니다. 이 문서에서는 API 구조를 탐색하는 방법을 보여줍니다.  

## <a name="api-summary"></a>API 요약

다음 목록은 Digital Twins API의 구성 요소를 보여줍니다.

* [/공간](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): 이러한 api는 설치의 물리적 위치와 상호 작용 합니다. [공간 그래프](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)의 양식으로 물리적 위치의 디지털 매핑을 만들고, 삭제 및 관리할 수 있도록 돕습니다.

* [/장치](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): 이러한 api는 설정의 장치와 상호 작용 합니다. 이러한 디바이스는 하나 이상의 센서를 관리할 수 있습니다. 예를 들어 사용자의 휴대폰 또는 Raspberry Pi 센서 Pod 또는 Lora 게이트웨이 등을 디바이스로 사용할 수 있습니다.

* [/센서](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): 이러한 api를 사용 하면 장치와 실제 위치와 연결 된 센서와 통신할 수 있습니다. 센서는 공간 환경을 조작하는 데 사용할 수 있는 앰비언트 값을 기록하고 보냅니다.  

* [/리소스](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): 이러한 api를 통해 디지털 쌍 인스턴스에 대해 IoT hub와 같은 리소스를 설정할 수 있습니다.

* [/형식](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): 이러한 api를 사용 하면 확장 형식을 디지털 쌍 개체와 연결 하 여 해당 개체에 특정 특성을 추가할 수 있습니다. 이러한 형식은 UI에서 개체의 쉬운 필터링 및 그룹화를 허용하고 원격 분석 데이터를 처리하는 사용자 지정 함수를 허용합니다. 확장된 유형의 예제는 *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType* 등입니다.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): 이러한 api는 확장 형식의 컬렉션인 온톨로지를 관리 하는 데 도움이 됩니다. 온톨로지는 나타내는 물리적 공간에 따라 개체 형식에 대한 이름을 제공합니다. 예를 들어 *BACnet* 온톨로지는 *sensor types*, *datatypes*, *datasubtypes* 및 *dataunittypes*에 대한 특정 이름을 제공합니다. 온톨로지는 서비스에 의해 관리되고 생성됩니다. 사용자는 온톨로지를 로드 및 언로드할 수 있습니다. 온톨로지가 로드되면 관련된 모든 해당 형식 이름이 활성화되고 공간 그래프에서 프로비전할 준비가 됩니다. 

* [/Svkeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): 이러한 api를 사용 하 여 *스페이스*, *장치*, *사용자*및 *센서*에 대한 사용자 지정 속성을 만들 수 있습니다. 이러한 속성은 키/값 쌍으로 생성됩니다. 해당 *PrimitiveDataType*을 설정하여 이러한 속성에 대한 데이터 형식을 정의할 수 있습니다. 예를 들어 센서에 대한 *uint* 형식의 *BasicTemperatureDeltaProcessingRefreshTime*이라는 속성을 정의한 다음, 각 센서에 대한 이 속성의 값을 할당할 수 있습니다. *Min* 및 *Max* 범위와 같은 속성을 만드는 동안 이러한 값 및 *ValidationData*로 허용된 값에 대한 제약 조건을 추가할 수도 있습니다.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): 이러한 api를 사용 하 여 들어오는 장치 데이터에서 평가 하려는 조건을 지정할 수 있습니다. 자세한 내용은 [이 문서](concepts-user-defined-functions.md#matchers)를 참조하세요. 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): 이러한 api를 사용 하면 *선택기* 에서 정의한 조건이 설치에서 제공 되는 데이터를 처리할 때 실행 되는 사용자 지정 함수를 만들거나 삭제 하거나 업데이트할 수 있습니다. *사용자 정의 함수*라고도 하는 이러한 사용자 지정 함수에 대한 자세한 내용은 [이 문서](concepts-user-defined-functions.md#user-defined-functions)를 참조하세요. 

* [/엔드포인트](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): 이러한 api를 사용 하 여 엔드포인트을 만들 수 있습니다. 그러면 디지털 쌍 솔루션이 데이터 저장 및 분석을 위해 다른 Azure 서비스와 통신할 수 있습니다. 자세한 내용은 [이 문서](concepts-events-routing.md)를 참조하세요. 

* [/Keystores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): 이러한 api를 사용 하면 스페이스에 대한 보안 키 저장소를 관리할 수 있습니다. 이러한 저장소는 보안 키의 컬렉션을 포함하고, 유효한 최신 키를 쉽게 검색할 수 있도록 합니다.

* [/사용자](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): 이러한 api를 사용 하면 필요할 때 사용자를 공백과 연결 하 여 이러한 개인을 찾을 수 있습니다. 

* [/시스템](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): 이러한 api를 사용 하면 기본 공백 유형 및 센서와 같은 시스템 수준 설정을 관리할 수 있습니다. 

* [/Roleassignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): 이러한 api를 사용 하 여 사용자 ID, 사용자 정의 함수 ID 등과 같은 엔터티에 역할을 연결할 수 있습니다. 각 역할 할당에는 연결할 엔터티의 ID, 엔터티 형식, 연결할 역할의 ID, 테넌트 ID 및 엔터티가 해당 연결을 사용 하 여 액세스할 수 있는 리소스의 상한을 정의 하는 경로가 포함 됩니다. 자세한 내용은 [이 문서](security-role-based-access-control.md)를 참조하세요.


## <a name="api-navigation"></a>API 탐색

Digital Twins API는 다음 매개 변수를 사용하여 공간 그래프 전체에 대한 필터링 및 탐색을 지원합니다.

- **spaceId**: API는 지정 된 공간 ID를 기준으로 결과를 필터링 합니다. 또한 지정된 공간 ID가 현재 공간 대신 부모 공간을 나타냄을 표시하는 [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) API에 부울 플래그 **useParentSpace**를 적용할 수 있습니다. 

- **minlevel** 및 **maxlevel**: 루트 공간은 수준 1에 있는 것으로 간주 됩니다. *n* 수준에서 부모 공간이 있는 공간은 수준 *n+1*입니다. 이러한 값을 설정하여 특정 수준에서 결과를 필터링 할 수 있습니다. 설정되었을 때 결과는 포괄적인 값입니다. 디바이스, 센서 및 기타 개체는 가장 가까운 공간과 동일한 수준으로 간주됩니다. 지정된 수준에서 모든 개체를 가져오려면 **minLevel** 및 **maxLevel**을 동일한 값으로 설정합니다.

- **minrelative** 및 **maxrelative**: 이러한 필터를 지정 하면 해당 수준은 지정 된 공간 ID의 수준을 기준으로 합니다.
   - 상대 수준 *0*은 지정된 공간 ID와 동일한 수준입니다.
   - 상대 수준 *1*은 지정된 공간 ID의 자식과 동일한 수준에서 공간을 나타냅니다. 상대 수준 *n*은 *n* 수준으로 지정된 공간보다 낮은 공간을 나타냅니다.
   - 상대 수준 *-1*은 지정된 공간의 부모 공간과 동일한 수준에서 공간을 나타냅니다.

- **트래버스**: 다음 값에 지정 된 대로 지정 된 공간 ID에서 어느 방향으로 나 트래버스할 수 있습니다.
   - **없음**:이 기본값은 지정 된 공간 ID를 필터링 합니다.
   - **Down**: 지정 된 공간 ID 및 해당 하위 항목을 기준으로 필터링 합니다. 
   - **Up**: 지정 된 공간 ID 및 해당 상위 항목을 기준으로 필터링 합니다. 
   - **Span**: 지정 된 공간 ID와 동일한 수준에서 공간 그래프의 가로 부분을 필터링 합니다. **minRelative** 또는 **maxRelative**를 true로 설정해야 합니다. 


### <a name="examples"></a>예시

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

> [!NOTE]
> 일부 OData 옵션 (예: 쿼리 옵션 **$count**, **$expand**및 **$search**)은 현재 지원 되지 않습니다.

### <a name="examples"></a>예시

다음 목록에서는 유효한 OData 구문을 사용한 몇 가지 쿼리를 보여 줍니다.

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>다음 단계

몇 가지 일반적인 API 쿼리 패턴에 대해 알아보려면 [일반적인 작업에 대해 Azure Digital Twins API를 쿼리하는 방법](./how-to-query-common-apis.md)을 참조하세요.

API 엔드포인트에 대한 자세한 내용은 [Digital Twins Swagger를 사용 하는 방법](./how-to-use-swagger.md)을 참조 하세요.

OData 구문 및 사용 가능한 비교 연산자를 검토 하려면 [Azure Cognitive Search의 odata 비교 연산자](../search/search-query-odata-comparison-operators.md)를 참조 하세요.
